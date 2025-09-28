
#wsl #Alma

Alma 发行版无法直接打开图形化的程序，也就是不会弹出窗口，需要安装一些依赖库才行，执行下面的命令：

```bash
dnf install -y \
  gtk3 \
  libXext libXrender libXtst libXi libXrandr libXcursor libXcomposite libXdamage \
  libXfixes libXinerama libXxf86vm libdrm mesa-libGL \
  fontconfig freetype \
  bzip2-libs \
  nss
```

