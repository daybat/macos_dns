# macos_dns
**macos 13.5 (Ventura) : "host" &amp; "nslookup" commands can resolve the local domain, but ssh &amp; safari can't connect to host**


Inside my network I have DNS server which controls local domain _nucme_. The command host can resolve the domain name:**

>_administrator@iMac-1 / % host 123.nucme<br>
>123.nucme has address 192.168.1.22_

and nslookup command works well:

>_administrator@iMac-1 / % nslookup 123.nucme<br>
>Server:		192.168.1.22<br>
>Address:	192.168.1.22#53<br>
><br>
>Name:	123.nucme<br>
>Address: 192.168.1.22_<br>

But when I try to connect to 123.nucme over ssh:

>_administrator@iMac-1 / % ssh user@123.nucme<br>
>ssh: Could not resolve hostname 123.nucme: nodename nor servname provided, or not known

The ping command does not work also

>_administrator@iMac-1 resolver % ping 123.nucme   
>ping: cannot resolve 123.nucme: Unknown host<br>

Why? I flushed DNS cach and restarted DNS daemon on my mac:

>_sudo dscacheutil -flushcache;sudo killall -HUP mDNSResponder_

Apple support has not helped me at all. They said to connect me with senior manager but it takes a while.



**Solution:
**
open terminal and run the followin commands:

if your system does not have /etc/resolver folder:
>_sudo mkdir /etc/resolver_

(the system will ask your password)

>_echo 'nameserver ip_address_of_your_nameserver' | sudo tee your_domain_


Now you can check if your domain can be resolved:

>_administrator@iMac-1 resolver % scutil --dns<br>
>DNS configuration<br>
><br>
>resolver #1<br>
> nameserver[0] : 192.168.1.1<br>
> nameserver[1] : 192.168.1.22<br>
> nameserver[2] : 192.168.2.22<br>
> nameserver[3] : 1.1.1.1<br>
> nameserver[4] : 8.8.8.8<br>
> nameserver[5] : 8.8.4.4<br>
> flags  : Request A records<br>
> reach  : 0x00020002 (Reachable,Directly Reachable Address)<br>
><br>
>...<br>
><br>
>resolver #8<br>
> domain  : nucme<br>
> nameserver[0] : 192.168.1.22<br>
> flags  : Request A records<br>
> reach  : 0x00020002 (Reachable,Directly Reachable Address)_<br>

Now you domain can be resolved.

>_administrator@iMac-1 resolver % ping 123.nucme<br>
>PING 123.nucme (192.168.1.22): 56 data bytes<br>
>64 bytes from 192.168.1.22: icmp_seq=0 ttl=64 time=1.693 ms<br>
>64 bytes from 192.168.1.22: icmp_seq=1 ttl=64 time=0.649 ms<br>
>64 bytes from 192.168.1.22: icmp_seq=2 ttl=64 time=1.046 ms<br>
>64 bytes from 192.168.1.22: icmp_seq=3 ttl=64 time=0.663 ms<br>
>^C<br>
>--- 123.nucme ping statistics ---<br>
>4 packets transmitted, 4 packets received, 0.0% packet loss<br>
>round-trip min/avg/max/stddev = 0.649/1.013/1.693/0.424 ms_<br>



Enjoy!
