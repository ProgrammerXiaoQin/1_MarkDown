BIOS中BMC设置有两个网口设置，Dedicate lan(专口)和share lan(共享口)，一般服务器主板上有三个口，2个i350口和一个BMC管理口，这个BMC管理口就是Dedicate，链接这个口输入dedicate lan ip可以访问BMC管理界面。设置好share lan后，网线连接主板板载i350的网口，输入share lan的ip,也可以访问BMC管理界面

> 部分G220-A系列更换主板后，主板BMC是共享口，导致无法进入BMC，以下为识别和修改方法

1. 进入PE系统输入 `ipmitool raw 0x3e 0x11`
	- 返回值：`00` 专口
	- 返回值：`01` 共享口
    
2. 修改专口命: `ipmitool raw 0x32 0x71 0x06 0x00 0x01`