# 1  install server on ubuntu 20.04

## 1.1 更新软件包

	sudo apt update
	sudo apt upgrade

## 1.2 安装OpenVpn

	wget https://git.io/vpn -O openvpn-install.sh && bash openvpn-install.sh

## 1.3 生成client配置
	./openvpn-install.sh 

# 2 配置客户端

## 2.1 For windows

安装OpenVpn软件, 将1.3生成的vpn客户端配置导入到vpn配置目录:

	C:\Program Files\OpenVPN\config

## 2.2 启动客户端

启动openvpn客户端即可访问

# 3 备注

## 3.1 server端要开启端口安全组配置

默认端口1194, 查询进程占用端口命令

	netstat -tunlp | grep 8000
