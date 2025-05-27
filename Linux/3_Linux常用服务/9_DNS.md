1. DNS的安装和开启
	1. `yum install -y bind bind-utils` 安装
	2. `systemctl enable --now named` 启动DNS服务
	3. `firewall-cmd --permanent --add-service=dns `   # 设置防火墙，允许访问DNS端口
	4. `firewall-cmd --reload` # 重启防火墙
	5. `netstat -antlupe |grep named`    # 检查DNS端口开放情况(dns使用udp协议，默认监听53端口)


2.  编辑DNS服务配置文件 `vim /etc/named.conf`
```
options {
        listen-on port 53 { 127.0.0.1; }; 
        # 指定监听的IP地址和端口,修改为`any;`表示监听本机所有IPv4 53端口
        
        allow-query     { 192.168.1.0/24; };
	    # 允许查询主机列表，修改为`any;` 表示允许所有主机查询
	    
		recursion yes;
		# 是否启用递归查询（缓存服务器开启，权威服务器建议关闭）
		
		forwarders { 223.5.5.5;8.8.8.8; };
		# 上级DNS服务器
		
		dnssec-validation no;
		# 禁用DNS检测
		
```
> 修改好后可以使用 `named-checkconf /etc/named.conf` 检查语法


3. 添加主机A记录 `vim /etc/named.rfc1912.zones`
>named.conf中include块引入，自定义zones文件需要保证文件所属主和组一致
```
格式：
 zone "域名" IN {                    # 维护的域名
         type master;               # 当前服务器为主DNS服务器
         file "域名解析记录文件";      # 域名解析记录文件，记录着根域名的解析，file文件默认存放在/var/named/目录中，可以自定义文件位置，例如：file "/xieyunh/named/xieyunh.com.zone"
         allow-update { none; };    # 允许更新主机列表
 };

例：
 zone "xieyunh.com" IN {            # 维护的域名
         type master;               # 当前服务器为主DNS服务器
         file "xieyunh.com.zone";   # 域名解析记录文件，记录着根域名的解析，file文件默认存放在/var/named/目录中
         allow-update { none; };    # 允许更新主机列表
 };
```

4.配置域名解析记录文件
1. `cd /var/named`
2. `cp -p named.localhost xieyunh.com.zone` 复制一份模板
```
## $TTL 1D 解析生存时间1天
## IN表示这是一个正向解析记录
## SOA起始授权机构记录，定义了域名的管理信息；
## @表示域名的根，即当前域名；
## rname.invalid表示管理员邮箱，用来接收DNS相关邮件通知

$TTL 1D
@       IN SOA  @ rname.invalid. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        NS      @
        A       192.168.148.110
        AAAA    ::1
dns     IN   A  192.168.148.129
www     IN   A  192.168.148.110

列：
		NS      dns.xieyunh.com.
dns      A       192.168.31.137
tbj      A       192.168.31.98
tsj      A       192.168.31.129
www      A       192.168.31.98

## 此时dns.xieyunh.com解析为192.168.31.137，tbj.xieyunh.com解析为192.168.31.137，依次类推（AAAA为ipv6解析记录）
```

5. slookup或者host测试域名解析
	1. `nslookup www..xieyunh.com localhost` 
	2. `host tbj.xieyunh.com` 
	3. nslookup和host需要安装bind*

6. 注：
	1. centos DNS配置文件 `/etc/resole.conf`

