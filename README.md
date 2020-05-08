# virtualMachineSetUp 

virtualbox official tutorial[https://www.virtualbox.org/manual/ch06.html]

virtualbox vs. vmware[https://www.nakivo.com/blog/vmware-vs-virtual-box-comprehensive-comparison/#] 
![virtualboxNetwork](virtualboxNetwork.png)
![vmwareNetwork](vmwareNetwork.png)

vmware ip range 
[https://www.vmware.com/support/ws55/doc/ws_net_advanced_ipaddress.html] 
![](vmwareIPRange.png)

VirtualBox sets two NIC  
[https://medium.com/@smiles.aayush/setup-nat-and-host-only-network-on-oracle-vm-virtualbox-9341d7babafe]  

hyper-v 
[https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/user-guide/setup-nat-network] 

vmware tutorial   
[https://rednectar.net/2011/07/20/vmware-interfaces-tutorial/] 
[https://blog.csdn.net/tjcwt2011/article/details/78659242]  

install vnc 
take snapshot before your operation 
[https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-vnc-on-ubuntu-18-04] 

ubuntu change hostname 
```
Type the following command to edit /etc/hostname using nano or vi text editor:
sudo nano /etc/hostname
Delete the old name and setup new name.
Next Edit the /etc/hosts file:
sudo nano /etc/hosts
Replace any occurrence of the existing computer name with your new one.
Reboot the system to changes take effect:
sudo reboot
```

vmware virtual machine clone 
To save time, just choose linked clone. Then change adapter mac address, hostname. 

My summary for vmware netwrok:   
vmnet8 is just the device in vmware nat network. it's not the gateway. In windows route table, vmnet8 ip points to itself, so windows host cannot ping nat DNS, NHCP device. However, windows host can ping guest with the vmnet8 adapter. Don't know why.  
vmnet8(nat) gateway ip, .2   
vmnet1(host-only) gateway ip, .1 
vmnet1 itself is the gateway. This is why host can ping every device in the host-only network. 

change route table  
[https://en.wikiversity.org/wiki/Computer_Networks/Route/Modify]  
```
 route delete 0.0.0.0
 route add 0.0.0.0 mask 0.0.0.0 <gateway>
```

How To List and Delete Iptables Firewall Rules  
[https://www.digitalocean.com/community/tutorials/how-to-list-and-delete-iptables-firewall-rules] 


If you use second NIC in vmware, it may down at the beginning. 
[https://www.2daygeek.com/enable-disable-up-down-nic-network-interface-port-linux-using-ifconfig-ifdown-ifup-ip-nmcli-nmtui/] 

ubuntu static ip  
[https://linuxconfig.org/how-to-configure-static-ip-address-on-ubuntu-18-10-cosmic-cuttlefish-linux]  
[https://www.howtoforge.com/linux-basics-set-a-static-ip-on-ubuntu]  

two NIC 
You have built two or more network cards into one Linux system and each of these cards has its own default gateway. By default, you can only have one default gateway on a system. The case described would lead to asynchronous routing, whereby the router would reject the packets as appropriate. 
[https://www.thomas-krenn.com/en/wiki/Two_Default_Gateways_on_One_System] 

windows route vs. linux route 
route add is persistent in windows. 
it disappears in linux after reboot. 
if you have multiple nics, set up static ip, or it may be down. 
do not change yaml file name, which would influence route table. 

edit windows route table 
[https://www.cnblogs.com/yzeng/p/3787717.html] 
[https://www.cnblogs.com/lightnear/archive/2013/02/03/2890835.html] 

edit linux route table 
[https://www.computerhope.com/unix/route.htm] 
[https://www.thegeekstuff.com/2012/04/route-examples/] 
[https://www.cyberciti.biz/faq/linux-route-add/]
```
use
route -n
instead of 
route

route add -net 192.56.76.0 netmask 255.255.255.0 dev eth0
adds a route to the network 192.56.76.x via "eth0" The Class C netmask modifier is not really necessary here because >192.* is a Class C IP address. The word "dev" can be omitted here.

route add default gw 192.168.1.10

route add -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.1.10

Route all traffic via 192.168.1.254 gateway connected via eth0 network interface:
# route add default gw 192.168.1.254 eth0

sudo route del -net 192.168.3.0 gw 192.168.1.1 netmask 255.255.255.0 dev eth0

route del default gw 192.168.1.1
```

netplan tutorial, and route 
[https://netplan.io/examples] 
[https://linuxconfig.org/how-to-add-static-route-with-netplan-on-ubuntu-20-04-focal-fossa-linux] 
[https://www.networkinghowtos.com/howto/adding-persistent-static-routes-on-ubuntu-18-04-and-higher-using-netplan/] 
```
example[https://askubuntu.com/questions/1062902/ubuntu-18-04-netplan-static-routes] 

In general, what you want here is:

Set up a single default gateway (with gateway4), on the interface that goes to the Internet. If you set default gateways on both, then half the packets will be routed to your Intranet and won't be able to reach their desintation.

If your Intranet has multiple subnets, then you need static routes to reach those through the interface connected to your Intranet. (One example might be routing any RFC1918 subnets to that interface, which would probably be a good idea.)

Now, in your specific example, you didn't describe your Intranet completely, but let's assume your Intranet is made of the 10.185.x.y network, in other words, 10.185.0.0/16.

Let's also assume your enp3s0 interface giving you access to the intranet will receive an IP in the 10.185.0.z subnet, in other words, 10.185.0.0/24 subnet, and that the gateway in that subnet is 10.185.0.1.

So you need a static route to reach the remaining of the 10.185.x.y subnets where x is not 0.

You can use a configuration such as the one below to set this up:

network:
  version: 2
  renderer: networkd
  ethernets:
        enp3s0:
            addresses: []
            dhcp4: true
            nameservers:
              addresses: [10.185.x.x, y.y.y.y]
            routes:
            - to: 10.185.0.0/16
              via: 10.185.0.1
  wifis:
        wlp2s0:
            addresses: []
            dhcp4: true
            optional: true
            gateway4: 192.168.8.1
            access-points:
                 "Wifi":
                    password: "password"
            nameservers:
              addresses: [8.8.8.8,8.8.4.4]
In this edited configuration, notice that:

There is no gateway4 in enp3s0 configuration, since you don't want traffic to go to that interface by default, only when it's traffic destined to your Intranet, which is set up through the static route.

Conversely, the wlp2s0 doesn't need any static routes, since it has a default gateway attached to it, which is enough.

There's no need for routing tables and routing policies, all you need to do is set up a static route (or a few static routes) to cover the internal addresses in your Intranet and route them through the IP of the gateway in that network that can route you to the other subnets you're not directly connected to.

Please note that this setup actually depends on some of what your DHCP server in the internal network is provisioning for you, such as your interface being in the 10.185.0/24 network and that 10.185.0.1 is the gateway you can use in that interface... For that reason, perhaps a better setup would be to have the DHCP server in your Intranet push the static routes (instead of configuring them in netplan.) That way if the Intranet is reconfigured, perhaps to change the IP of the gateway, or perhaps extended to include other RFC1819 private ranges, only the DHCP server needs to be reconfigured and not everything else...

But if the DHCP server is out of your control, then this setup might be acceptable, assuming the internal network is not reconfigured too often so that the gateway IP would change. You might want to consider adding static routes to all the RFC1918 ranges, since those will be invalid in the Internet, so they might only be made valid in the Intranet:

routes:
- to: 10.0.0.0/8
  via: 10.185.0.1
- to: 172.16.0.0/12
  via: 10.185.0.1
- to: 192.168.0.0/16
  via: 10.185.0.1
I hope you find this helpful!

```



linux multi task 
[https://chrisjean.com/multitasking-from-the-linux-command-line-plus-process-prioritization/] 
```
fg 1
sudo kill %1
# more powerful
sudo kill -9 %1
```

```
new version
/etc/netplan/50-cloud-init.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
     dhcp4: no
     addresses: [192.168.1.233/24]
     gateway4: 192.168.1.1
     nameservers:
       addresses: [8.8.8.8,8.8.4.4]
       
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      # Note, "dhcp4/6: no" not needed
      addresses: [192.168.10.254/24]
      gateway4: 192.168.10.1
      nameservers:
        addresses: [192.168.10.1,192.168.10.252]
    eth1:
      # Note, "dhcp4/6: no" not needed
      addresses: [192.168.20.10/24]
      gateway4: 192.168.20.1
      nameservers:
        addresses: [192.168.20.1,192.168.20.252]

sudo netplan apply
```

start nic 
```
ifconfig <NIC> up
```

nic codes 
```
show ip, NIC
ip add
ifconfig

show all NIC
ifconfig -a

# old version
restart network
sudo systemctl restart networking
service network restart
# new version
sudo netplan apply

# old
sudo vim /etc/network/interfaces
Make the following entry in the end and save the file.
auto enp0s8
iface enp0s8 inet static
192.168.56.101
# new
sudo nano /etc/netplan/*.yml

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

虚拟机 nat 原理[https://bbs.csdn.net/topics/390760405]  
1 vm8 带nat的功能 
   nat不是转化ip地址  而是靠修改传送的数据包 来 实现的
 
  这里先知道一下 3个网卡 
   虚拟机的网卡    （虚拟机里的网卡）    虚拟
  nat的网卡  （ 实体机里 网络里的 适配器 里的 VMnet1 ） 虚拟
  物理网卡  （实体机的物理网卡） 物理
   
 nat的连接方式是  物理网卡 ——》vm nat程序 ——》nat的网卡（inside）——》虚拟机网卡
 
  在知道一下数据包 

一个网络数据  包含了 发送者的ip 发送者的端口  接收者的ip 接受者的端口
------------------------------------------------------------------------------------------------------------------------   
假设
       nat outside 为 172.16.5.4 inside为 192.168.1.1

  1.  从虚拟机网卡发出数据包
          接受者  172.16.5.1端口 80   发送者 192.168.1.2 端口 5421 

2.         网关收到数据包后  交给nat
          nat 修改发送者 ip为   outside的ip     发送者端口  修改为 ouside空闲的端口
           数据包变为 接受者  172.16.5.1端口 80   发送者  172.16.5.4 端口65
            nat 上建立一个表 
                     172.16.5.4 :65-------192.168.1.2:5421
             然后把数据 从172.16.5.4 (outside端) 发送. 出去

3.      接受返回的数据包 
         对方返回的数据包  （依据收到的数据包 得到 接收者的ip 端口 ）
          发送者 是 172.16.5.1 端口80  接受者 172.16.5.4 端口65                     
            
4.   nat outside 收到数据包后
      首先查 表
     看看有没有匹配的 没有就丢弃
     查表一看 匹配这一条
    172.16.5.4 :65-------192.168.1.2:5421
    nat 修改数据包的接收方的ip 和端口
     修改为
     发送者 是 172.16.5.1 端口80  接受者 192.168.1.2端口5421
    再从192.168.1.1（ inside端 ）发给192.168.1.2 
  
这样实现了 跨网段的 nat过程
整理一下 数据包的变化过程
     发出
       inside （ 进）
     接受者  172.16.5.1端口 80   发送者 192.168.1.2 端口 5421 
       nat后 
      接受者  172.16.5.1端口 80   发送者  172.16.5.4 端口65
       outside （出）
  
      收数据包
        outside （进） 
      发送者 是 172.16.5.1 端口80  接受者 172.16.5.4 端口65    
      nat 后
        发送者 是 172.16.5.1 端口80  接受者 192.168.1.2端口5421 
      inside （出）
 --------------------------------------------------------------------------------------------------------------------- 
2 .3 问题  都对
4的问题

虚拟机——》外网
虚拟机网卡 ——》nat的网卡（inside） ——》vm nat——》物理网卡

外网——》虚拟机
物理网卡 ——》vm nat——》nat的网卡（inside）——》虚拟机网卡

--------------
Windows equivalent of iptables?  
One way would be with the netsh command:  
netsh firewall (deprecated after XP and 2003)   
netsh advfirewall (Vista, 7, and 2008) 
no tutorials, to be continued 

iptables tutorial 
[http://www.zsythink.net/archives/1199]   
[https://www.karlrupp.net/en/computer/nat_tutorial]   

Setup Windows Server 2016 as a NAT Router    
[https://www.experiencingit.net/windows/windows-server/windows-server-2016-nat-router/]   
For win10, we don't have server manager, so it doesn't work.   

Good question: 
Port forward- Why is iptables with POSTROUTING rule required?  
[https://unix.stackexchange.com/questions/322879/port-forward-why-is-iptables-with-postrouting-rule-required]  

Windows 10 pro as a NAT between two interfaces (to route VM traffic trough a VPN connection on the host) 
[https://serverfault.com/questions/911578/windows-10-pro-as-a-nat-between-two-interfaces-to-route-vm-traffic-trough-a-vpn] 

routing table  
```
linux:
sudo route -n
route add default gw 192.168.1.200
windows:
route PRINT -4

```   

how router works  
[https://blog.51cto.com/13401027/1975059] 
[https://www.cnblogs.com/hoojjack/p/6747357.html]  
[https://blog.51cto.com/13683137989/1880744] 
[https://www.linuxidc.com/Linux/2016-12/138661.htm]   

how nat works  
[https://blog.csdn.net/hzhsan/article/details/45038265] 
![](nat.jpg)   
![](napt.jpg)   

gitbook 网络基本功  
[https://wizardforcel.gitbooks.io/network-basic/0.html]  

Host Virtual Adapter[https://www.vmware.com/support/ws5/doc/ws_net_component_hostadapter.html]  
The host virtual adapter is a virtual Ethernet adapter that appears to your host operating system as a VMware virtual Ethernet adapter on a Windows host and as a host-only interface on a Linux host. The host virtual adapter allows you to communicate between your host computer and the virtual machines on that host computer. The host virtual adapter is used in host-only and NAT configurations.

The host virtual adapter is not connected to any external network unless you set up special software on the host computer — a proxy server, for example — to connect the host-only adapter to the physical network adapter.

The software that creates the host virtual adapter is installed when you install VMware Workstation (on a Linux host, you must choose to make host-only networking available to your virtual machines). A host virtual adapter is then created automatically when you boot the host computer.

You can set up additional host virtual adapters as needed.  

VMware official doc  
[https://www.vmware.com/support/ws5/doc/ws_net.html] 
[https://pubs.vmware.com/workstation-11/index.jsp] 

The difference between nat and host-only network   
![](vmwareNatGraph.png)    
The nat device in nat netwrok is connected to network. In host-only, it's not.   

unix routing basic   
[https://www.networkworld.com/article/2707620/unix---getting-from-here-to-there--routing-basics-.html]   
[https://opensource.com/business/16/8/introduction-linux-network-routing]  
[https://unix.stackexchange.com/questions/14056/what-is-kernel-ip-forwarding] 
[https://unix.stackexchange.com/questions/322879/port-forward-why-is-iptables-with-postrouting-rule-required]  
[https://unix.stackexchange.com/questions/402233/what-is-the-difference-between-nat-output-chain-and-nat-postrouting-chain]   
[https://unix.stackexchange.com/questions/280114/why-does-snat-happen-in-postrouting-chain-and-dnat-in-prerouting-chain]   


windows ip forwarding, acts as a router   
[https://support.tetcos.com/support/solutions/articles/14000098272-how-to-enable-ip-forwarding-in-windows-to-perform-emulation-using-netsim-]   
```
netsh interface ipv4 show interfaces
netsh interface ipv4 show interface <if id>

```

Masquerading in windows is called Internet Connection Sharing (ICS)  
[https://superuser.com/questions/1088309/windows-10-nat-port-forwarding-ip-masquerade] 
[https://www.keepthetech.com/2016/01/enable-ip-routing-on-windows10.html]  

Why Does My VMware NAT work?  
[https://superuser.com/questions/1127750/why-does-my-vmware-nat-work]   

Linux find gateway   
[https://www.cyberciti.biz/faq/how-to-find-gateway-ip-address/]   
```
ip r
ip route
```


