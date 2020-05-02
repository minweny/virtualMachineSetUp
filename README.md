# virtualMachineSetUp 

virtualbox official tutorial[https://www.virtualbox.org/manual/ch06.html]

virtualbox vs. vmware[https://www.nakivo.com/blog/vmware-vs-virtual-box-comprehensive-comparison/#] 
![virtualboxNetwork](virtualboxNetwork.png)
![vmwareNetwork](vmwareNetwork.png)

VirtualBox sets two NIC  
[https://medium.com/@smiles.aayush/setup-nat-and-host-only-network-on-oracle-vm-virtualbox-9341d7babafe]  

hyper-v 
[https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/user-guide/setup-nat-network] 

```


show ip, NIC
ip add
ifconfig

restart network
sudo systemctl restart networking
service network restart

sudo vim /etc/network/interfaces
Make the following entry in the end and save the file.
auto enp0s8
iface enp0s8 inet static
192.168.56.101

switch on the interface
sudo ifup enp0s8 up
sudo reboot





Routing and Remote Access - Windows 10 Service
doesn't find any tutorials
```
VirtualBox port forwarding   
https://dbabasis.wordpress.com/2017/09/16/port-forward-to-nat-networks-from-different-hosts-on-virtualbox/ (nat port forwarding)  
https://www.golinuxcloud.com/configure-nat-port-forwarding-virtualbox-cli/ (bridge port forwarding?)  
https://www.techrepublic.com/article/how-to-use-port-forwarding-in-virtualbox/  

VirtualBox Host Network Manager(create host-only network) 
https://www.techrepublic.com/article/how-to-create-virtualbox-networks-with-the-host-network-manager/   

how bridge works[https://www.jianshu.com/p/c62bb377b016]  
在桥接模式下，例如在 windows 下使用 vmware 时，明显看到会生成多个虚拟网卡出来，但却没有一个网卡是属于桥接的，原因是虚拟机采用桥接方式时，是不需要虚拟网卡的，这时候虚拟机操作系统和实体机操作系统同时使用真实网卡工作，然后真是网卡发送数据出去，而如果虚拟机发送数据给真实机，则数据流向是：虚拟机系统->真实网卡->路由器->真实网卡->实体机系统，不难看出同一个数据经过网卡在路由器上面绕了一圈又回到了网卡。 

hwo nat works 
NAT模式实际是虚拟了一个网卡出来，虚拟机直接使用链接这个虚拟网卡，每次访问和交互通过这个虚拟网卡交换数据。虚拟机发送数据给实体机：虚拟机系统->虚拟网卡->实体机系统（可以发现是不经过真实网卡的流程简单很多）；虚拟机访问外网：虚拟机系统->虚拟网卡->实体机系统->真实网卡->路由器->外网* 。* 

