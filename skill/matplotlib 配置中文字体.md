

> https://blog.csdn.net/sinat_36728101/article/details/124349841
> https://blog.csdn.net/xovee/article/details/122287986


- 下载字体 -> [[Linux_安装字体]]
- 将字体放到 matplotlib 的字体目录下
```python
import matplotlib
print(matplotlib.matplotlib_fname())  # 查询字体路径

# xxx/python3.9/site-packages/matplotlib/mpl-data/matplotlibrc  # 输出的路径
# xxx/python3.9/site-packages/matplotlib/mpl-data/fonts/ttf  # 字体放到这个目录下
```
- 清除字体缓存文件
```python
import matplotlib
print(matplotlib.get_cachedir())

# /root/.cache/matplotlib  # 输出路径
# rm -rf /root/.cache/matplotlib/*  # 删除该目录下所有文件
```


