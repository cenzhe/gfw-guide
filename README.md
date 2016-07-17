# gfw-guide

GFW stands for the great firewall of China. There are quite a number of guides on how to bypass GFW, this one serves as a complementary guide.

## Shadowsocks on OpenWRT
[Here](https://github.com/softwaredownload/openwrt-fanqiang/blob/master/SUMMARY.md) is an excellent book on what is/why/how to bypass on OpenWRT. The core part is [this section](https://github.com/softwaredownload/openwrt-fanqiang/blob/master/ebook/03.5.md), where it briefs on selectively bypassing GFW.

The guide is so good that I have only a few points to note here:

* The [accelerated-domains.china.conf](https://github.com/softwaredownload/openwrt-fanqiang/blob/master/openwrt/default/etc/dnsmasq.d/accelerated-domains.china.conf) file is not exhaustive of course. But it basically covers big sites those can afford CDN services. So it suffice as a starting point, if not perfect already.
* At the time of writing, the `ss-tunnel` part is mandatory. DNS lookups are UDP-based. `ss-redir` cannot forward DNS lookups properly, even with the `-u` option on. The fact is, I tried to set the fail-safe DNS lookup address to `114.114.114.114` or `8.8.8.8` (depending on the switch), and added UDP iptables rules to redirect `8.8.8.8` destined packets to local ss port. DNS resolution failed.
* If it happed to you too that your Shadowsocks service provider limits your number of connections like me, you might want to switch remote server in command line arguments to override the `/etc/shadowsocks.json` defaults.
* The list of China gateway IPs is so long, that it takes more than 4 MINUTES to add those iptables rules on my WNDR3800. Be patient or print logs/progresses.
* The list provided by the author of the book is incomplete(outdated?) and redundant. See next section.

## List of China Gateway IPs
My list is [here](chinadns_chnroute.txt). I'll show you the ropes how to get it as well:

[Apnic](https://www.apnic.net/) provides the official(?) list of IPs for all countries. [Here](http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest) is the full list. By `wget -O- http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest | grep CN | grep ipv4 | awk -F '|' '{print $4"/"32-log($5)/log(2)}' | perl aggregate-cidr-addresses.pl > chinadns_chnroute.txt`, you get the latest list by yourself!<sup name="agg">[1](#aggfoot)</sup>

At the time of writing, the outcome is a 4512-line [file](chinadns_chnroute.txt), with entries like below:

```
103.43.208.0/22
103.43.220.0/22
103.43.224.0/22
103.43.232.0/22
103.43.240.0/22
```

This format is ready to be consumed by other scripts.

<b name="aggfoot">1</b> The aggregate-cidr-addresses.pl was borrowed from [here](https://gist.github.com/denji/17e30bddb9ce9e50294a). [↩](#agg)
