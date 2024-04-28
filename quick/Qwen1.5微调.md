

# 1 下载依赖

```sh
python=3.10

pip install transformers>=4.37.0
pip install torch
pip install -U accelerate
pip install -U optimum
pip install auto-gptq
pip install wandb
```


# 2 微调代码

```python
#!/usr/bin/env python
# coding: utf-8

# # 导入环境
from datasets import Dataset
import pandas as pd
from transformers import AutoTokenizer, AutoModelForCausalLM, DataCollatorForSeq2Seq, TrainingArguments, Trainer, GenerationConfig


# 将JSON文件转换为CSV文件
df = pd.read_json('/model/datasets/huanhuan.json')
ds = Dataset.from_pandas(df)


# In[3]:


ds[:3]


# # 处理数据集

# In[4]:


tokenizer = AutoTokenizer.from_pretrained('/model/Qwen1.5-1.8B-Chat-GPTQ-Int8/', use_fast=False, trust_remote_code=True)
tokenizer


# In[5]:


def process_func(example):
    MAX_LENGTH = 384    # Llama分词器会将一个中文字切分为多个token，因此需要放开一些最大长度，保证数据的完整性
    input_ids, attention_mask, labels = [], [], []
    instruction = tokenizer(f"<|im_start|>system\n现在你要扮演皇帝身边的女人--甄嬛<|im_end|>\n<|im_start|>user\n{example['instruction'] + example['input']}<|im_end|>\n<|im_start|>assistant\n", add_special_tokens=False)  # add_special_tokens 不在开头加 special_tokens
    response = tokenizer(f"{example['output']}", add_special_tokens=False)
    input_ids = instruction["input_ids"] + response["input_ids"] + [tokenizer.pad_token_id]
    attention_mask = instruction["attention_mask"] + response["attention_mask"] + [1]  # 因为eos token咱们也是要关注的所以 补充为1
    labels = [-100] * len(instruction["input_ids"]) + response["input_ids"] + [tokenizer.pad_token_id]  
    if len(input_ids) > MAX_LENGTH:  # 做一个截断
        input_ids = input_ids[:MAX_LENGTH]
        attention_mask = attention_mask[:MAX_LENGTH]
        labels = labels[:MAX_LENGTH]
    return {
        "input_ids": input_ids,
        "attention_mask": attention_mask,
        "labels": labels
    }


# In[6]:


tokenized_id = ds.map(process_func, remove_columns=ds.column_names)
tokenized_id


# In[7]:


tokenizer.decode(tokenized_id[0]['input_ids'])


# In[8]:


tokenizer.decode(list(filter(lambda x: x != -100, tokenized_id[1]["labels"])))


# # 创建模型

# In[9]:


import torch

model = AutoModelForCausalLM.from_pretrained('/model/Qwen1.5-1.8B-Chat-GPTQ-Int8/', device_map="auto",torch_dtype=torch.bfloat16)
model


# In[10]:


model.enable_input_require_grads() # 开启梯度检查点时，要执行该方法


# In[11]:


model.dtype


# # lora 

# In[12]:


from peft import LoraConfig, TaskType, get_peft_model

config = LoraConfig(
    task_type=TaskType.CAUSAL_LM, 
    target_modules=["q_proj", "k_proj", "v_proj", "o_proj", "gate_proj", "up_proj", "down_proj"],
    inference_mode=False, # 训练模式
    r=8, # Lora 秩
    lora_alpha=32, # Lora alaph，具体作用参见 Lora 原理
    lora_dropout=0.1# Dropout 比例
)
config


# In[13]:


model = get_peft_model(model, config)
config


# In[14]:


model.print_trainable_parameters()


# # 配置训练参数

# In[15]:


args = TrainingArguments(
    output_dir="./output/Qwen1.5",
    per_device_train_batch_size=4,
    gradient_accumulation_steps=4,
    logging_steps=10,
    num_train_epochs=3,
    save_steps=100,
    learning_rate=1e-4,
    save_on_each_node=True,
    gradient_checkpointing=True
)


# In[16]:


trainer = Trainer(
    model=model,
    args=args,
    train_dataset=tokenized_id,
    data_collator=DataCollatorForSeq2Seq(tokenizer=tokenizer, padding=True),
)


# In[ ]:


trainer.train()


```


# 3 加载lora权重推理

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
import torch
from peft import PeftModel

mode_path = '/model/Qwen1.5-1.8B-Chat-GPTQ-Int8/'
lora_path = '/model/temp/output/Qwen1.5/checkpoint-600/'


# 加载tokenizer
tokenizer = AutoTokenizer.from_pretrained(mode_path)

# 加载模型
model = AutoModelForCausalLM.from_pretrained(mode_path, device_map="auto",torch_dtype=torch.bfloat16)

# 加载lora权重
model = PeftModel.from_pretrained(model, model_id=lora_path)

prompt = "你是谁？"
messages = [
    {"role": "system", "content": "现在你要扮演皇帝身边的女人--甄嬛"},
    {"role": "user", "content": prompt}
]

text = tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True)

model_inputs = tokenizer([text], return_tensors="pt").to('cuda')

generated_ids = model.generate(
    model_inputs.input_ids,
    max_new_tokens=512
)
generated_ids = [
    output_ids[len(input_ids):] for input_ids, output_ids in zip(model_inputs.input_ids, generated_ids)
]

response = tokenizer.batch_decode(generated_ids, skip_special_tokens=True)[0]

print(response)

```

