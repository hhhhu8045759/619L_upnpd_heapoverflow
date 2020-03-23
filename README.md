# 619L_upnpd_heapoverflow

The mini_upnpd in 619L firmware2.06beta has a heap overflow vulnerability. An attacker can send a specific ssdp M-search request without authorization

![image](https://github.com/hhhhu8045759/619L_upnpd_heapoverflow/blob/master/ssdp.png)
![image](https://github.com/hhhhu8045759/619L_upnpd_heapoverflow/blob/master/ssdp1.png)

When the ST value of the ssdp M-search request packet is not filtered and checked, the value of ST: upnp: rootdevice + 'a' * 3000 can be constructed. The function SendSSDPAnnounce2 copies the requested ST value as a sprint parameter to the heap space, causing Heap overflow, causing the router to restart, may cause remote code execution


123 456 
