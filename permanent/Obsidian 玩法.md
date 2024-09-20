

# 1 插件

```
- Paste image rename  粘贴图片重命名
- Remotely Save  远程同步
- Number Headings  自动添加序号
- open in new tab  打开新标签, 不覆盖原有的
- Excalidraw  手绘白板
- Code Styler  代码块样式
- Editor Width Slider  调整编辑区域的宽度
- Control Characters  显示隐藏的标点符号
```


# 2 CSS 代码片段

- 简介

```
  外观 -> css 代码片段 -> 在目下创建并编辑css文件, 即可修改样录式
```

- 调整代码块字体大小

```css
code, span.cm-hmd-codeblock { font-size: 24px!important; }
```

- 鼠标移动到图片上自动放大, 只在阅读模式有效果

```css
.markdown-preview-view img {
  display: block;
  margin-top: 20pt;
  margin-bottom: 20pt;
  margin-left: auto;
  margin-right: auto;
  width: 50%; /* experiment with values */
  transition: transform 0.25s ease;
}

.markdown-preview-view img:hover {
  -webkit-transform: scale(1.8); /* experiment with values */
  transform: scale(2);
}
```



