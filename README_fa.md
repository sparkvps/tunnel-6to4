# tunnel-6to4

### نحوه راه اندازی و پیکربندی تونل 6to4 IPv6 در لینوکس را با این راهنما بیاموزید.

## دستورالعمل نصب ایران
1 = باز کنید`/etc/rc.local file:`

```
nano /etc/rc.local
```
2= ​​دستورات زیر را کپی و در فایل پیست کنید و آی پی های جایگیر را با آی پی های خارجی و ایرانی خود جایگزین کنید:

```
#!/bin/bash

ip tunnel add 6to4tun_IR mode sit remote ipharej local ipiran
ip -6 addr add 2001:470:1f10:e1f::1/64 dev 6to4tun_IR
ip link set 6to4tun_IR mtu 1480
ip link set 6to4tun_IR up
# confige tunnele GRE6 ya IPIPv6 IR
ip -6 tunnel add GRE6Tun_IR mode ip6gre remote 2001:470:1f10:e1f::2 local 2001:470:1f10:e1f::1
ip addr add 172.16.1.1/30 dev GRE6Tun_IR
ip link set GRE6Tun_IR mtu 1436
ip link set GRE6Tun_IR up

iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -A FORWARD  -j ACCEPT
sudo sysctl -w net.ipv4.ip_forward=1
echo "net.ipv4.ip_forward=1" > /etc/sysctl.conf
sysctl -p
service iptables save
service iptables restart
service iptables stop
service iptables start
```

3= پس از ذخیره و خروج، دستورات زیر را به ترتیب اجرا کنید:
```
chmod +x /etc/rc.local
echo "net.ipv4.ip_forward=1" > /etc/sysctl.conf 
sysctl -p
/etc/rc.local
```

## دستورالعمل نصب سرور خارجی

1 = باز کنید`/etc/rc.local file:`

```
nano /etc/rc.local
```
2= ​​دستورات زیر را کپی و در فایل پیست کنید و آی پی های جایگیر را با آی پی های خارجی و ایرانی خود جایگزین کنید:
```
#!/bin/bash
ip tunnel add 6to4tun_KH mode sit remote ipIran local ipKharej
ip -6 addr add 2001:470:1f10:e1f::2/64 dev 6to4tun_KH
ip link set 6to4tun_KH mtu 1480
ip link set 6to4tun_KH up

ip -6 tunnel add GRE6Tun_KH mode ip6gre remote 2001:470:1f10:e1f::1 local 2001:470:1f10:e1f::2
ip addr add 172.16.1.2/30 dev GRE6Tun_KH
ip link set GRE6Tun_KH mtu 1436
ip link set GRE6Tun_KH up

iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -A FORWARD  -j ACCEPT
sudo sysctl -w net.ipv4.ip_forward=1
echo "net.ipv4.ip_forward=1" > /etc/sysctl.conf
sysctl -p
service iptables save
service iptables restart
service iptables stop
service iptables start
```
3= پس از ذخیره و خروج، دستورات زیر را به ترتیب اجرا کنید:

```
chmod +x /etc/rc.local
echo "net.ipv4.ip_forward=1" > /etc/sysctl.conf 
sysctl -p
/etc/rc.local
```
اکنون هر دو سرور شما متصل هستند و شما یک آدرس IPv6 محلی دارید:

ایپی خارج: `2001:470:1f10:e1f::2`

ایپی ایران: `2001:470:1f10:e1f::1`

### اکنون می توانید از IP ساخته شده برای خارج از کشور برای تونل هایی مانند Decodemodo و ... استفاده کنید.
