1. 虚拟环境
	linux
	- `python -m venv tutorial-env` 创建虚拟环境 , `tutorial-env`如果该目录不存在，则会创建该目录
	- `source tutorial-env/bin/activate` 激活虚拟环境
	- `deactivate` 停用虚拟环境
	windows
	- `python -m venv myenv` 虚拟环境名称
	- `myenv\Scripts\activate.bat` 激活虚拟环境
	- `deactivate` 停用虚拟环境

2. pip
	查询软件包
	- `pip list` 列出所有软件包
	- `pip search pkg` 查询 pypi 上含有某名字的包
	- `pip list --outdated` 查询当前环境中可升级的包
	- `pip show pkg` 查询一个包的详细内容
	下载软件包
	- `pip download --destination-directory /local/wheels -r requirements.txt` 不安装软件包的情况下下载软件包到本地
	- `pip install --no-index --find-links=/local/wheels -r requirements.txt` 指定这个目录中安装软件包，而不从 pypi 上安装
	安装软件包
	- `pip install <pkg>`  安装软件包
	- `pip install --no-index --find-links=/local/wheels pkg`  只从本地安装，而不从 pypi 安装
	- `pip install pkg==2.1.2` 限定版本安装 , 也可以写成`>=2.1.2`(所安装的包必须大于等于 )
	卸载软件包
	- `pip uninstall pkg`

3. other
	- `python -m http.server` 启动一个简易http服务，加载当前目录