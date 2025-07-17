- python记录日志
	- ```
	  import logging
	  from logging.handlers import TimedRotatingFileHandler
	  
	  # 创建日志记录器
	  logger = logging.getLogger(__name__)
	  logger.setLevel(logging.INFO)
	  
	  # 创建一个处理器，设置为每天轮换
	  handler = TimedRotatingFileHandler(
	      "my_log.log",  # 日志文件名
	      when="midnight",  # 每天午夜轮换
	      interval=1,  # 每天轮换一次
	      backupCount=7  # 保留最近7天的日志
	  )
	  
	  # 设置日志格式
	  formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(message)s')
	  handler.setFormatter(formatter)
	  
	  # 将处理器添加到日志记录器
	  logger.addHandler(handler)
	  
	  # 示例日志记录
	  logger.info("这是一个信息日志")
	  ```