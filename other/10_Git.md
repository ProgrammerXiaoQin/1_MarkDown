1. git配置 ,初始化
	1. 配置用户名跟邮件  --说明性配置 , 跟注册登录无关
		- `git config --global user.name "username"`    设置用户名
		- `git config --global user.email "email@qq.com"`  设置邮箱

	2. 创建克隆仓库
		- `git init`   创建仓库
		- `git clone [url]`   克隆一个远程仓库


2. git文件状态 , 提交版本
	1. 文件状态
		- 未跟踪       
			- 可通过`git add filename`变为暂存状态
			- 可通过`git commit`提交, 形成一个新的版本 ,该新的版本中该文件就是未修改状态(但是已跟踪)
		- 未修改
			- 可通过`git rm filename`变为未跟踪状态
		- 已修改(status为红色)
			- `git add filename` 将文件变为暂存状态
		- 暂存(status为绿色)
			- `git commit` 提交, 形成一个新的版本

	2. 命令操作
		- `git add filename`     跟踪一个文件 , 并把该文件设置为缓存状态
			- `git reset HEAD filename`     取消缓存状态
		- `git rm filename`      取消跟踪并删除一个文件
			- `--cache`      取消跟踪并保留该文件
		- `git commit`  提交, 形成一个新的版本
			- `-m`  直接添加提交信息
			- -a  提交所有文件包括没有暂存的
			- `git reset head~ --soft` 撤销上一次提交
		- `git status`   查看文件状态
		- `git diff`      查看文件修改地方
		- `git log`        查看历史提交


3. git远程仓库
	1. 远程仓库
		- `git remote add origin [url]`  链接远程仓库 , 设置仓库名为`orgin`
		- `git remote`    查看已添加的远程仓库
		- `git remote rename origin origin2`     修改远程仓库的名字
		- `git push origin master`     将暂存的master分支推送到远程仓库
		- `git remote rm origin`        删除一个已经存在的origin
		- `git pull [url]`                   拉取远程代码
	2. 免密登录
		1. `ssh-keygen -t rsa`      进入用户家目录下/.ssh生成公钥和私钥
		2. 将公钥内容复制粘贴到github的`SSH and GPG keys`
	3. pull拉取最新代码
		1. `git fetch --all`       更新远程仓库代码为最新
		2. `git reset --hard origin/master`   让本地代码与origin/master相同
		3. `git pull origin master`                  拉取远程代码
		4. `git merge master`                            将暂存区代码更新到本地工作区


4. git分支
	- `git branch --list`           所有分支, 带`*`的为主分支
	- `git branch feature1`        创建feature1分支
		- -b                              创建分支并切换到该分支
	- `git checkout feature1`    切换到feature1分支

5. 设置代理
```bash
git config --global http.proxy socks5://127.0.0.1:7890
git config --global https.proxy socks5://127.0.0.1:7890
##设置http和https代理 , socks5://127.0.0.1:7890为clash代理
git config --global -l
##查看已有代理
git config --global --unset http.proxy
git config --global --unset https.proxy
##取消代理
```