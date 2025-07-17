- 配置
	- ```
	  {
	      "workbench.colorTheme": "GitHub Light",  // 主题, 需要下载主题插件
	      "editor.wordWrap": "wordWrapColumn",  // 指定列数换行
	      "editor.wordWrapColumn": 200,  // 200列数换行
	      "files.eol": "\n",  // 一行的结尾符号 LF
	      "workbench.iconTheme": "material-icon-theme",  // 图标主题, 需要下载插件
	      "typescript.locale": "zh-CN",  // 设置中文, 需要下载插件
	      "explorer.confirmDelete": true,  // 删除操作时, 给出提示
	      "workbench.editor.enablePreview": true,  // 预览模式
	  
	      "workbench.editor.empty.hint": "hidden",  // 打开空文件时, 隐藏不必要的提示信息
	      "git.ignoreLegacyWarning": true,  // 显示或忽略git的告警信息
	  
	      "editor.mouseWheelZoom": true,  // 可以通过鼠标滚轮缩放大小
	      "workbench.editor.wrapTabs": true,  // Tab页可以多行展示
	  
	      "editor.tabSize": 2,  // tab键一次插入几个空格
	      "editor.insertSpaces": true,  // tab键使用空格
	      "editor.detectIndentation": false,  // 不检查制表符或空格
	  
	      "editor.suggestSelection": "first",  // 代码补全, 默认选择第一个
	      "files.trimTrailingWhitespace": true,   // 保存时修剪空白字符
	      "extensions.ignoreRecommendations": false,  // 不提示扩展建议
	      "editor.renderControlCharacters": true,  // 是否显示控制字符, 换行符等
	      "editor.renderWhitespace": "all",  // 显示空格和制表符
	  
	       // 字体设置
	       // "editor.fontFamily": "'Source Code Pro Regular', monospace",
	      "editor.fontFamily": "'霞鹜新晰黑 屏幕阅读版 补全', monospace",  // 设置字体
	      "editor.fontLigatures": true,//这个控制是否启用字体连字，true启用，false不启用，这里选择启用
	      "editor.fontSize": 18,//设置字体大小
	      "editor.fontWeight": "bold",//这个设置字体粗细，可选normal,bold,"100"~"900"等，选择合适的就行
	  
	      "security.workspace.trust.untrustedFiles": "open",  // 允许打开不受信任的文件
	      "workbench.editor.untitled.hint": "hidden",  // 新建文件时, 是否提示选择该文件是什么类型或语言的
	  }
	  ```
	- 打开默认配置
		- ```
		  快捷键 ctrl + shift + p
		  > Open Default Settings
		  ```
- 修改样式
	- 样式文件路径
		- vscode/resources/app/out/vs/workbench/workbench.desktop.main.css
	- 调整 Tab 大小
		- 搜索 monaco-workbench .part.editor>.content .editor-group-container>.title .title-label
		- 总共有多处，其中一个后面有font-size，修改这个值
	- 修改左侧栏大小
		- 搜索 .monaco-workbench .part>.content
		- 总共有多处，其中一个后面有font-size，修改这个值
- 搜索
	- 匹配空行 `^\s*(?=\r?$)\n`