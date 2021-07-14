CentOS7 通过代理上网
1、修改/etc/profile，增加以下内容：

http_proxy=http://[代理地址]:[代理地址的端口]/
https_proxy=http://[代理地址]:[代理地址的端口]/
export http_proxy https_proxy
----------------------------------------------------------
http_proxy=http://192.168.130.2:7890/
https_proxy=http://192.168.130.2:7890/
export http_proxy https_proxy
source /etc/profile
curl http://toyaml.com:9999/whoami
2、马上生效

source /etc/profile
3、测试

curl http://toyaml.com:9999/whoami
可以看到，获取到的IP已经变成了代理服务器的外网IP

 

4、如果想和修改前对比，可以

unset http_proxy
curl http://toyaml.com:9999/whoami
