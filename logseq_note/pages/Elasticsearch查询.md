- 全文查询
	- match 查询
		- ```
		  match会将查询内容也进行分词，只有一个分词匹配就可以返回结果。（查询比较宽泛）
		  
		  ---
		  {
		    "query": {
		      "match": {
		        "字段名称":"查询内容"
		      }
		    }
		  }
		  
		  ---
		  {
		    "query": {
		      "match": {
		        "title": {
		          "query": "查询内容",
		          "operator": "or"
		        }
		      }
		    }
		  }
		  
		  ---
		  {
		    "query": {
		      "match": {
		        "title": {
		          "query": "查询内容",
		          "operator": "and"
		        }
		      }
		    }
		  }
		  ```
	- match_phrase 查询
	  collapsed:: true
		- ```
		  对查询内容分词后，必须匹配所有分词，并且顺序也要一致
		  
		  ---
		  {
		    "query": {
		      "match_phrase": {
		        "字段名称":"查询内容1 查询内容2"
		      }
		    }
		  }
		  ```
		- slop
			- ```
			  phrase查询太严格，分词全匹配还要保证顺序，slop就是放宽一些，中间漏掉几个分词也没事。
			  
			  ---
			  {
			    "query": {
			      "match_phrase": {
			        "字段名称":{
			          "query":"查询内容1 查询内容3",
			          "slop":1
			        }
			      }
			    }
			  }
			  ```
		- match_phrase_prefix
			- ```
			  支持最后一个词的前缀匹配
			  
			  ---
			  {
			    "query": {
			      "match_phrase_prefix": {
			        "desc": "what li"
			      }
			    }
			  }
			  ```
	- multi_match 查询
	  collapsed:: true
		- ```
		  查询多个字段，有一个字段能找到就行
		  
		  ---
		  {
		    "query": {
		      "multi_match": {
		          "query" : "查询内容",
		          "fields" : ["字段1", "字段2"]
		      }
		    }
		  }
		  ```
		- 可以使用通配符
			- ```
			  字段可以使用通配符
			  
			  ---
			  {
			    "query": {
			      "multi_match": {
			        "query": "查询内容",
			        "fields": ["title", "*_name"]
			      }
			    }
			  }
			  ```
	- query_string 查询
	  collapsed:: true
		- ```
		  query_string 对查询内容先分词，再查询
		  
		  ---
		  {
		      "query": {
		          "query_string": {
		              "default_field": "字段名称",
		              "query": "查询内容"
		          }
		      }
		  }
		  ```
- 词项查询
	- term 查询
		- ```
		  和match的区别是查询内容不会分词后进行匹配
		  
		  只是查询内容不分词，但是匹配的时候，是匹配库中的分词，所以查询内容是一个词就行了，不要完整内容
		  
		  ---
		  {
		      "query": {
		          "term": {
		              "字段名称": "查询内容"
		          }
		      }
		  }
		  ```
		- terms 查询
			- ```
			  满足一个就行，相当于or查询
			  
			  ---
			  {
			    "query": {
			      "terms": {
			        "字段名称": ["查询内容", "查询内容"]
			      }
			    }
			  }
			  ```
	- fuzzy 查询
		- ```
		  fuzzy是模糊匹配，它也不会把查询内容进行分词，它允许查询内容有错误，比如查询内容是test，但是库中是text，这样也能查出来
		  
		  ---
		  {
		    "query": {
		      "fuzzy": {
		        "字段名称":"查询内容"
		      }
		    }
		  }
		  ```
	- fuzzy 配置
		- ```
		  fuzziness 的值表示可以允许有几个错误字母
		  
		  ---
		  {
		    "query": {
		      "fuzzy": {
		        "字段名称":{
		          "value":"查询内容",
		          "fuzziness":1
		        }
		      }
		    }
		  }
		  ```
	- type 查询
		- ```
		  {
		    "query": {
		      "type": {
		        "value": "computer"
		      }
		    }
		  }
		  ```
	- ids 查询
		- ```
		  {
		    "query": {
		      "ids": {
		        "type": "computer",
		        "values": ["1", "3", "5"]
		      }
		    }
		  }
		  ```
	- range 查询
		- ```
		  字段必须是数值类型
		  
		  - gte：大于等于
		  - gt：大于
		  - lt：小于
		  - lte：小于等于
		  
		  ---
		  { 
		    "query": {
		      "range": { 
		        "字段名称": { 
		            "gte":20, 
		            "lt":30 
		        } 
		      }
		    } 
		  }
		  
		  ---
		  时间范围
		  {
		    "query": {
		      "range": {
		        "字段名称": {
		          "gte": "2015-01-01",
		          "lte": "2019-12-31",
		          "format": "yyyy-MM-dd"
		        }
		      }
		    }
		  }
		  ```
	- exists 查询
		- ```
		  返回该字段，值不为空的文档
		  
		  {
		    "query": {
		      "exists": {
		        "field": "字段名称"
		      }
		    }
		  }
		  ```
	- prefix 查询
		- ```
		  {
		    "query": {
		      "prefix": {
		        "字段名称": "词前缀"
		      }
		    }
		  }
		  ```
	- wildcard 查询
		- ```
		  {
		    "query": {
		      "wildcard": {
		        "字段名称": "李永*"
		      }
		    }
		  }
		  ```
	- regexp  正则查询
		- ```
		  {
		    "query": {
		      "regexp": {
		        "postcode": "W[0-9].+"
		      }
		    }
		  }
		  ```
- 复合查询
	- bool 查询
	- ```
	  组合查询，以下是bool的关键字：
	  - must：代表且的关系，也就是必须要满足该条件
	  - should：代表或的关系，代表符合该条件就可以被查出来
	  - must_not：代表非的关系，也就是要求不能是符合该条件的数据才能被查出来
	  - filter 和 must 一样，匹配 filter 选项下的查询条件的文档才会被返回，但是 filter 不评分，只起到过滤功能，与 must_not 相反。
	  
	  ---
	  {
	      "query":{
	          "bool":{
	              "must":{  // 必须满足
	                  "match":{
	                      "字段名称":"查询内容"
	                  }
	              },
	              "should":{  // 满足一个就行
	                  "term":{
	                      "字段名称":"查询内容"
	                  },
	                  "range":{
	                      "num":{
	                          "gt":20
	                      }
	                  },
	              }
	          }
	      }
	  }
	  ```
- 分页查询
	- ```
	  和查询语句平级
	  
	  ---
	  {
	      "query":{
	          "match":{
	              "字段名称":"查询内容"
	          }
	      },
	      "from":0,  // 第几页
	      "size":100,  // 每页多少条
	      "sort":{  // 排序
	          "字段名称":{
	              "order":"desc"  // asc
	          }
	      }
	  }
	  ```