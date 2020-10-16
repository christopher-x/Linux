#使用nmap来进行二层扫描

直接使用nmap -sn 
nmap 192.168.254.1/24	-sn		
nmap -iL ~/Desktop/test	-sn		

nmap 1.1.1.1-254 -PU	-sn		pu后面跟上端口号,UDP扫描
nmap 1.1.1.1-254 -PA80 	-sn		ACK扫描方式，PA后面跟端口号

#在后面跟上-sn不做端口扫描，只做四层主机发现。

-sC　　根据端口识别的服务，调用默认脚本

hping3  --upd 11.23.231.222 -c 1		使用hping3来进行四层扫描

nmap -sU 1.1.1.1		不指定端口范围，默认扫描一千个端口。
nmap -sU 1.1.1.1 -p	扫描全端口

#TCP端口扫描  
基于连接的协议  
三次握手  
隐蔽扫描  
僵尸扫描  
全连接扫描  
所有的TCP扫描方式，都是基于三次握手的变化来判断目标端口状态。

#隐蔽扫描---syn  
不建立完整链接。 发送Syn/ACK。判断端口开放与否  
应用层日志不记录扫描行为--------隐蔽   

#僵尸扫描  
极度隐蔽  
实施条件较为苛刻		1 首先扫描发起方，必须能达到可以伪造源地址。  
可伪造源地址  
选择僵尸机  
	闲置系统  
	系统使用递增的IPID		2 XP，2000，2003，早期的操作系统。 必须要达到此条件。  
		0  
		随机   

#测试使用本地机器来进行僵尸扫描  

```
a=sr1(IP(dst="192.168.254.114")/TCP(flags="S"),timeout=1,verbose=0)
```



#隐蔽端口扫描1  
nmap 192.168.254.122		扫描当前机器的端口，不需要加任何参数或者加上-sS  
nmap 192.168.254.122 --open		只显示开放的端口号  
nmap 192.168.254.122 -p -65535 	-p指定端口扫描  

#隐蔽端口扫描2  
hping3  
hping 192.168.254.122 --scan 1-80 -S	参数--scan 从1到80号端口来进行扫描。  
hping3 -c 10 -S --spoof 192.168.254.121 -p ++1 192.168.254.122		发送10个包，增加欺骗spoof。-p ++1 每次递增1个端口号，目标地址  

#全连接扫描  
scapy  
	syn扫描不需要raw packets  
	内核认为syn/ack是非法包，直接发rst中断连接  
	全连接扫描对scapy比较困难  

#全链接扫描nmap  
nmap -sT 192.168.254.114 -p 1-100	直接使用-sT来进行扫描即可。  
			--script=bannner.nse		在语句中跟上此语句，可以查询到banner信息  

#dmitry  
dmitry -p 192.168.254.114		单命令扫描。  

#nc扫描  
nc -nv -w 1 -z 192.168.254.114	1-100	-w超时时间设定。-z 设定下一次扫描  

#使用nmap来进行僵尸机扫描  
nmap p-445 192.168.254.122 --script=ipidseq.nse		p代表端口。  
nmap  192.168.254.123 -sI 192.168.254.122 -Pn -p  0-100	使用sI后面跟上使用的僵尸机    


/usr/share/nmap/script		脚本存放地址  



#查看目标主机的系统  
nmap 192.168.254.125 -O		扫描目标主机，最后的参数为大写的O  
p0f		监听网络访问，并识别目标主机操作系统os   
onesixtone 192.168.254.125 	查询该主机的架构信息（系统版本，OS）  
onesixtyone -c /usr/share/doc/onesixtyone/dict.txt 192.168.254.125 -o my.log -w 100  

snmpwalk 192.168.254.125 -c public -v 2c  
snmpcheck -t 192.168.254.125 	使用snmp来进行检查   



#扫描端口  
nmap -v -p 139,445 192.168.254.125		扫描139和445端口，看服务是否开启！  
nmap -v -p 139.445 192.168.254.0/24 --open 	扫描网段查看有那些主机开放了139和445端口  
nmap -v -p 139,445 192.168.254.125 --script=smb-os-discovery.nse	查询详细信息，使用脚本  
nbtscan -r 192.168.254.125		扫描主机在线  
enum4linux -a 192.168.254.125	详细信息。  

#判断防火墙如何过滤端口-判断是哪一种的防火墙  
nmap -p22 192.168.254.125  

#判断是否有waf进行流量清洗  
nmap www.baidu.com --script=http-waf-detect.nse  