#### 一. 常用方法
1. `os.walk(dir)` 是一个用于遍历目录树的函数。它返回一个生成器（generator），每次迭代会返回一个包含三个元素的元组：当前文件夹的路径、当前文件夹下的子文件夹列表、当前文件夹下的文件列表。
```python
for root, dirs, files in os.walk(directory):
# 在每次迭代中，root 是当前文件夹的路径，dirs 是当前文件夹下的子文件夹列表，files 是当前文件夹下的文件列表 
# 这里你可以对 root、dirs 和 files 进行处理
```

2. `os.path.join(path, path)` 路径拼接 , 返回一个新的路径