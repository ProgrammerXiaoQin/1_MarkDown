- - -
#### 1. 简介和安装
1. Nginx是⼀一个开源且⾼高性能、可靠的HTTP中间件、代理理服务

2. 优点: 高并发 , 内存消耗少 , 简单稳定 , 支持Rwrite重写规则 , 支持网站url地址重写 , 根据url的特点进行请求转发 , 支持高可用性配置(防止单点故障 , 服务器崩溃) , 支持静态文件压缩后传输

3. 缺点: 动态处理差 , rewrite弱
 
4. 应用场景 : 静态处理 , 反向代理 , 负载均衡 , 资源缓存 , 安全防护 , 访问限制 , 访问认证

5. 安装
	- `yum install  -y gcc gcc-c++ autoconf automake make` 安装编译工具
	- `yum install -y zlib zlib-devel openssl openssl-devel pcre pcre-devel wget httpd-tools` 安装静态资源压缩功能所需的`gzip lib库` ,url重写所需的`pcre库`, 搭建加密站点https所需的`openssl` 等
	- 解压后进入目录 ` ./configure --help` 查看帮助信息
	- `./configure --prefix=/opt/nginx/ --with-http_ssl_module --with-http_flv_module  --with-http_gzip_static_module --with-http_stub_status_module --with-threads --with-file-aio` 通过参数释放配置, 添加开启指定功能
	- `make && make install` 开始编译安装
#### 2. Nginx进程模型
1. Nginx的进程模型 , nginx的启动方式有两种:
	- 单进程启动:
		- 此时系统中只有一个进程，这个进程既是master进程，也是worker进程
	- 多进程启动: 
		- 此时系统中有且仅有一个master进程，有多个worker进程，master进程主要是用来管理worker进程的。

2. nginx的两种进程:
	1. master主进程:
		- 接收客户端请求 , 然后在将客户端的请求交给他们下面的子进程 , 子进程返回处理结果后 , 再次将处理结果返回给客户端
		- 监控并管理子进程的运行状态 , 当子进程出现异常情况下退出后 , 自动生成新的子进程
	2. worker子进程:
		- 将主进程的请求交给内核去处理，然后在继续接收主进程发来的请求，当内核处理完后，返回给子进程处理结果，然后子进程再将结果返回给主进程。
		- Nginx子进程的数量一般我们会设置与当前服务器的CPU核心数一致，可以使用`auto`指令让Nginx跟进系统自动设置worker

#### 3.命令行操作
1. `nginx` 启动nginx服务
2. `nginx -s [stop, quit, reopen, reload]` 强制关闭 , 等待链接结束关闭 , 重新记录nginx日志和重新加载配置文件
3. `nginx -t` 检查配置文件是否正确
4. `nginx -c file.conf` 指定配置文件启动
5. `-V` 检查nginx 编译参数

#### 4. Nginx配置文件
>Nginx配置文件的位置随着安装方式的不同，所在的位置也会不同，通过yum/dnf方式安装，那配置文件是在/etc/nginx/nginx.conf；通过手动编译安装的话可以指定配置文件位置，不指定的话默认是在<Nginx安装目录>/conf/nginx.conf

1. 语法
	- nginx支持用`$变量名` 引用变量
	- 支持`include`语句组合多个配置文件
	- 部分指令支持正则表达式 , 如`rewrite` 重写指令

2. nginx.conf 配置注解
```nginx.conf
########################### 全局块配置 ##############################
#定义nginx运行的用户和用户组 , nobody表示所有用户都可以运行
user  nobody;

#Nginx在启动时生成worker进程的数量, 建议设置为等于CPU总核心数
#也可以设置成auto由nginx自动检测
worker_processes 2;


#Nginx全局错误日志的存放位置以及报错等级
#报错等级：[ debug调式 | info信息 | notice通知 | warn警告 | error错误 | crit重要 ]
#全局错误日志的存放位置采用的是相对路径（相对于Nginx安装目录）
error_log  logs/error.log  info;

#Nginx的进程PID文件存放位置,此文件中存放的进程ID号是master进程的
pid        logs/nginx.pid;

########################### events块配置 ############################ 
events { 
	#-->配置处理网络消息的事件驱动模型，可用的选项有:
	#-->[ kqueue | rtsig | epoll | /dev/poll | select | poll ]
	#-->此配置项默认不显示在配置文件中，此处是笔者手动添加的 
	use epoll; 
	#-->单个worker进程可以允许同时建立外部连接的数量 
	worker_connections 1024; 
}

########################### http块配置 ##############################
http {
########################### http全局块配置 ##########################
	##文件扩展名与文件类型映射表
    include       mime.types;

	##-->默认文件类型
    default_type  application/octet-stream;
	
	#-->自定义日志中要显示的内容，、日志记录内容的格式 
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';


	#-->全局访问日志的存放位置，默认不开启
	#access_log  logs/access.log  main;

	#-->零复制机制，提高文件的传输速率
	sendfile        on;

	#-->允许把httpresponse header和文件的开始放在一个文件中发布
	#-->优点是减少网络报文段的数量
    #tcp_nopush     on;

	#-->Nginx服务的响应超时时间
	send_timeout 10s;

	#-->保持连接的连接超时时间，单位是秒
    keepalive_timeout  65;

	#-->开启目录列表访问，适用于文件下载服务器，默认关闭
	#autoindex on;

	#-->gzip压缩输出，对响应数据进行在线实时压缩，减少数据传输量
    #gzip  on;

########################### server块配置 ##########################
	server {
########################### server全局块配置 ##########################
		#-->此server块监听的端口
        listen       80;
        
		#-->此server块的虚拟主机名称 ,常写为域名
        server_name  localhost;
        
		#设置web网页字符串类型
        #charset koi8-r;

		#-->对这一server块的访问日志存放位置以及和日志级别
        #access_log  logs/host.access.log  main;

########################### location块配置 ##########################

		#-->location语法格式：location [=|~|~*|^~] /path/ { ... }
		#-->支持正则表达式
        location / {
	        #-->网站根目录 ,也是也是网站程序存放的目录
            root   html;
            #-->首页排序
            index  index.html index.htm;
        }
        
		#当报错404时显示的的错误页面位置
		#error_page  404              /404.html;

		#-->当报错500 502 503 504时显示的错误页面位置
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

########################### location块配置 ##########################
        #location ~ \.php$ {
	        #-->反向代理，用于代理请求，若URL符合location匹配规则 
			#-->则将这条用户请求转发到proxy_pass配置的URL中
        #    proxy_pass   http://127.0.0.1;
        #}

########################### location块配置 ##########################
#-->这里的location模板用于将php的请求反向代理到后端的PHP服务中去
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}
    }
}

```  

#### 5. Nginx热部署
> 注意启动 nginx 需要用完整的路径启动，不能使用环境变量，否则发送 USR2 的时候无法 fork 新进程
```bash
##1.进入旧版本nginx安装目录sbin目录下 , 备份nginx二进制文件
cd /usr/nginx/sbin 
mv nginx nginxold

##2.将新版本编译后的objs目录下的nginx文件，拷贝到原来`/usr/nginx/sbin`目录下(注: 新版本编译参数必须和旧版本一样可用 nginx -V查看)
cd  nginx-1.25.3/    #进入解压后的nginx安装包
./configure 编译选项 #编译选项可以用nginx -V查看
make                 #开始编译
cp  objs/nginx /usr/nginx/sbin 

##4.检查Nginx服务是否正常
/opt/nginx/sbin/nginx -t

##5.发送USR2信号给Nginx老版本对应的master进程
kill -USR2 nginx_old_pid

##6.发送信号-WINCH给Nginx老版本对应的master进程 ,它会逐步关闭自己的工作进程（主进程不退出），这时所有的用户请求都会由新版的Nginx进程处理 , 需要回退版本继续使用旧版本Nginx，可向旧的Nginx主进程发送HUP信号，它会重新启动工作进程 , 然后可以将新版Nginx进程使用信号进行杀死（使用QUIT、TERM、或者KILL）
kill -WINCH nginx_old_pid

##7. 向旧版的Nginx主进程（master）发送（QUIT、TERM、或者KILL）信号，使旧版的master进程退出
kill -QUIT nginx_old_pid
```



#### 6. 日志切割
1. 针对nginx访客日志进行切割
	- `mv access.log "access.log_$(date +"%Y-%m-%d")"`
	- 用mv重命名access.log文件
	- `nginx -s reopen` 或 `kill -USR1 nginx_master_pid`
	- 给nginx主进程发送重新记录日志信号(指令)
	- 注: 在日志重命名时 , 不要着急对文件修改 , 而是要等几秒钟 , 因为nginx工作模式特点 , master下发指令给worker干活 , 刚发送的指令只是一个标记 , 当业务量大的时候这个操作会很慢 , 不会立即生效


