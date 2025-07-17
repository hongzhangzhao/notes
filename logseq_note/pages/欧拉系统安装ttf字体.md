- 首先将ttf字体文件放到特定的目录下
	- /usr/share/fonts
	  /usr/local/share/fonts
	  /root/.fonts
	  这几个目录都可以
- 刷新字体缓存：`sudo fc-cache -fv`
- 想要使用该字体的程序需要重启一下