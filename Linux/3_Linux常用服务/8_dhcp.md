##### 1. centos部署dhcp
1. 下载 `yum -y install dhcp`

2. 编辑dhcpd服务配置文件 `vim /etc/dhcp/dhcpd.conf`
```conf
#分配的网段及子网掩码
subnet 192.168.148.0 netmask 255.255.255.0 {

  #设置分配地址范围
  range 192.168.148.110 192.168.148.120;
  
  #设置分配给客户机dns服务器地址
  option domain-name-servers 8.8.8.8;
  
  #配置分配给客户机的路由网关
  option routers 192.168.148.2;
  
  #给客户机指定广播地址
  option broadcast-address 192.168.148.255;
  
  #指定租约时间，秒为单位
  default-lease-time 600;
  #指定最大租约时间
  max-lease-time 7200;
}
```

3. 重启dhcpd服务 `systemctl restart dhcpd`