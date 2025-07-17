- 设置时间
	- ```
	  timedatectl  # 查看时间和时区
	  timedatectl set-timezone Asia/Shanghai  # 设置时区
	  timedatectl set-local-rtc 0  # RTC time = Universal time
	  timedatectl set-time "2024-08-06 08:21:00"  # 设置系统本地时间
	  date -s "2022-10-01 12:00:00"  # 也可以设置本地时间
	  date -R  # 查看本地时间
	  ```