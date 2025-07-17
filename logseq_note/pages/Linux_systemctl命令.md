- systemctl命令
	- ```
	  systemctl start 是有超时机制的，如果启动的脚本无法结束，到了超时时间，将会中断。
	  
	  修改超时时间：编辑/etc/systemd/system.conf下的DefaultTimeoutStartSec=
	  
	  如果脚本中执行java程序需要在结尾追加`>> /dev/null 2>&1 &`，否则脚本无法结束会触发超时
	  
	  还有，在成功start后，docker容器会自动关闭，这个需要在脚本结尾追加`tail -f /dev/null`让脚本超时，这样就不会执行关闭容器的操作了
	  ```