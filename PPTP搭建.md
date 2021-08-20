# PPTP搭建

## 1.安装所需包


    yum install pptpd

## 2.修改配置文件

### 2.1配置分配客户端的IP

    vim /etc/pptpd.conf
    localip 10.0.8.11  #本机内网ip地址。（如果服务器有公网ip就写公网ip）
    remoteip 10.0.8.20-50  #分配给客户端的网段

### 2.2配置option参数

    vim /etc/ppp/options.pptpd
    ms-dns 8.8.8.8
    ms-dns 114.114.114.114

## 3.用户认证配置

    vim /etc/ppp/chap-secrets
    #格式：用户名 pptpd 密码 *
    xuchangyuan pptpd xuchangyuan *

## 4.配置系统IPV4转发

    vim /etc/sysctl.conf
    #添加以下内容
    net.ipv4.ip_forward=1

    #生效
    # sysctl -p

## 5.配置防火墙规则

--eth0 物理网卡
--ppp0 pptp映射网卡

    iptables -t nat -A POSTROUTING -s 10.0.8.0/24 -o eth0 -j MASQUERADE
    iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
    iptables --table nat --append POSTROUTING --out-interface ppp0 -j MASQUERADE
    iptables -I INPUT -s 10.0.8.0/8 -i ppp0 -j ACCEPT
    iptables-save > firewall
    systemctl restart pptpd

