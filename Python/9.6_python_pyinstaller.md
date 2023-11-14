- - - 
##### 一. 简介
1. PyInstaller是一个在Windows、GNU/Linux、macOS等平台下将Python)程序冻结（打包）为独立可执行文件的工具, 用于在未安装Python的平台上执行Python编写的应用程序

#### 二.安装和使用
1.  `pip install pyinstaller`通过pip安装
	- `pyinstaller -v` 查看版本 , 一般用于验证是否安装

2. `pyinstaller options pythonfile` 使用 , 通用参数
	- `–distpath` 生成文件路径 , 默认在当前目录下dist
	- `–workpath` 生成过程文件路径 , 默认在当前目录下build
	- `-y` 如果dist文件下已有生成文件不询问直接覆盖
	与结果有关参数:
	- `-D` 生成结果是一个目录，各种第三方依赖、资源和exe同时存储在该目录(默认)
	- `-F` 生成结果是一个exe文件，所有的第三方依赖、资源和代码均被打包进该exe内
	- `-w` 比现实命令窗口
	- `-c` 显示命令窗口
	- `-i` 只当ico文件