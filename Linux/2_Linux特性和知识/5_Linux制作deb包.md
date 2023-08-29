#### 1. deb包的组成结构
- 安装的内容，这部分类似linux的根目录，表示需要将软件安装到linux系统上的文件目录。
- 控制信息（放在DEBIAN目录下），通常DEBIAN目录下有如下几个文件
	- changelog: 文件记录了deb包的作者、版本以及最后一次更新日期等信息
	- control: 文件记录了包名、版本号、架构、维护者及描述等信息；
	- copyright: 文件记录了一些版权信息；
	- postinst: 软件在进行正常目录文件拷贝到系统后需要执行的脚本。
	- postrm文件: 软件卸载后需要执行的脚本。
    其中control、postinst、postrm为必要文件

#### 2. 构建deb包
1. 安装工具及依赖
```shell
sudo apt-get install build-essential
sudo apt-get install debmake
```

2. 创建目录及文件
```
hello_deb/
├── DEBIAN
│   ├── control
│   ├── postinst
│   └── postrm
└── opt
   └── hello_deb
      └── hello_deb.sh
```
DEBIAN目录下包含控制信息文件， 而opt/hello_deb目录下创建hello_deb.sh文件表示我们需要将hello_deb.sh文件安装到 linux系统的opt/hello_deb目录下 . 

然后分别给予postinst、postrm、hello_deb.sh文件以可执行权限 .

其中control文件所包含信息如下:
```
Package: hello-deb           
Version: 1.0.0
Section: free
Priority: optional
Essential: no
Architecture: armhf
Maintainer: embedfire <embedfire@embedfire.com>
Provides: hell_deb
Description: deb test
```
packge : 软件包名(只能包含小写字母,数字,加减号以及点) , Version :  为版本号, 以后若想升级这个包可以修改该版本号 ,  Architecture : 为该deb包支持的处理器架构 (lscpu可查看处理器信息) , 若想支持所有架构可填入all属性 , 如果architercture属性和当前架构不匹配deb包将无法安装

postinst , postrm文件分别为安装时系统会默认执行的脚本和卸载时会执行的脚本, 通常我们利用该脚本来搭建一些为软件执行的环境（如创建目录、修改权限等） ,  或清理环境

hello_deb.sh则为程序主体


3. 构建deb包
	1. 进入`hello_deb/`目录下 
	2. 运行`sudo dpkg-deb -b ../hello_deb ../hello_deb_1.0.0_armhf.deb`
	3.  ../hello_deb参数表示要构建deb包原材料的路径， ../hello_deb_1.0.0_armhf.deb参数表示将该deb包构建在当前目录的上级目录中， 一般我们构建deb包的名字都会遵循这么一个原则， 其命名方式为：软件名称+软件版本号+该软件所支持的处理器架构， 如软件名为hello_deb，版本号为1.0.0，所支持的处理器架构为armhf

#### 3. 修改查看deb包
1. `dpkg -X <包名> [解压路径]`  解deb包 , 不会解压出DEBIAN目录中的信息
2. `dpkg -e <包名> [解压路径]` 解压出控制相关信息
3. `-c`  查看包文件内容控制相关内容信息 , `--info` 查看包信息

#### 4.实例, 通过安装deb包创建开机自启服务
1. 构建deb包目录
```sh
hello_deb
├── DEBIAN
│   ├── control
│   ├── postinst
│   └── postrm
├── etc
│   └── systemd
│       └── system
│           └── hello.service
└── opt
    └── hello_deb
        └── hello_deb.sh
```
其中 `/etc/systemd/system/hello.service` 为自启服务， `/opt/hello_deb/hello_deb.sh` 为程序主体

DEBIAN/postinst文件内容如下所示，脚本的内容很简单只是使能了hello相关服务并启动它
```sh
systemctl enable hello
systemctl start hello
```

DEBIAN/postinst文件内容如下所示，当卸载程序时，关闭hello相关服务。
```shell
if [ "$1" = "upgrade" ] ; then
   echo "upgrade"
elif [ "$1" = "remove" ] || [ "$1" = "purge" ] ; then
   echo "remove"
   systemctl disable hello
fi
```

/etc/systemd/system/hello.service服务内容如下，重点在于第六行，指定需要执行的程序
```
[Unit]

Description = hello daemon

[Service]
ExecStart = /opt/hello_deb/hello_deb.sh
Restart = always
Type = simple

[Install]
WantedBy = multi-user.target
```

/opt/hello_deb/hello_deb.sh为程序主体，内容如下所示，以每3秒的速度向/tmp/hello.log 写入Hello Embedfire。
```shell
#!/bin/bash
while true
do
     echo Hello Embedfire >> /tmp/hello.log
     sleep 3
done
```

将以上目录打包构建成deb文件并使用dpkg命令安装即可。 重启后使用``systemctl status hello`` 查看服务相关状态