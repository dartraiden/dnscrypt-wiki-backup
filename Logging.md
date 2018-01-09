Uncover the DNS traffic originating from your network with the `QueryLog` extension, that can be enabled using the configuration file:

```
QueryLogFile /tmp/dns-queries.log
```

These log files are only written by the proxy on the local machine. They are not stored in "the cloud".

Sample output:

```
Tue Dec 20 17:15:32 2016        192.168.100.4   www.meetup.com.cdn.cloudflare.net       AAAA
Tue Dec 20 17:15:37 2016        192.168.100.252 logicnetcr.com  A
Tue Dec 20 17:15:57 2016        192.168.100.252 static.vnpt.vn  A
Tue Dec 20 17:16:23 2016        192.168.100.4   p33-btmmdns.icloud.com.akadns.net       A
Tue Dec 20 17:16:40 2016        192.168.100.252 xiqiart.com     A
Tue Dec 20 17:16:47 2016        192.168.100.252 bounce.e.brighton.com   A
Tue Dec 20 17:16:47 2016        192.168.100.252 bounce.e.brighton.com   MX
```

In order to rotate the log files, a HUP signal can be sent to the proxy. This will cause the plugins to be reloaded, and a new file to be created.