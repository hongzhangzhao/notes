- 安装字体
	- ```
	  安装字体命令 dnf install fontconfig
	  使用这个命令查看已安装字体: fc-list, fc-list :lang=zh (查看中文字体)
	  创建一个目录用来保存字体: mkdir -p /usr/share/fonts/my_fonts
	  在win10系统`C:\Windows\Fonts`查找字体, 并将字体上传到linux目录
	  安装字体索引指令 dnf install mkfontscale
	  进入字体目录中, 执行 mkfontscale
	  执行 fc-list 查看字体安装成功
	  ```