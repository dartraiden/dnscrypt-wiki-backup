`dnscrypt-proxy` can block domain names, IP addresses and patterns.

Queries matching these blacklists are handled directly by the proxy and do not require to be forwarded to upstream resolvers.

Contrary to other systems, responses to blacklisted queries do not contain fake IP addresses, but use the standard `REFUSED` DNS error code.

The blacklist handling code is very fast and can seamlessly handle huge sets of names and IP addresses.

Enabling this feature can be achieved by uncommenting and tweaking one of these lines in the configuration file:

```
# BlackList domains:/etc/dnscrypt-blacklist-domains.txt

## Block responses whose IP addresses match IPs stored in that file:
# BlackList ips:/etc/dnscrypt-blacklist-ips.txt

## Block both domain names and IP addresses:
# BlackList domains:/etc/dnscrypt-blacklist-domains.txt ips:/etc/dnscrypt-blacklist-ips.txt

## Same as the above + log the blocked queries in a file.
## The log file can be prefixed with ltsv: (ex: ltsv:/tmp/log.txt) in order to
## store logs using the LTSV format.
# BlackList domains:/etc/dnscrypt-blacklist-domains.txt logfile:/var/log/dnscrypt-blocked.log
# BlackList ips:/etc/dnscrypt-blacklist-ips.txt logfile:/var/log/dnscrypt-blocked.log
# BlackList domains:/etc/dnscrypt-blacklist-domains.txt ips:/etc/dnscrypt-blacklist-ips.txt logfile:/var/log/dnscrypt-blocked.log
```

In this example, up to three files are involved:

* `/etc/dnscrypt-blacklist-domains.txt` is a file containing rules to match (not only domain-) names against.
* `/etc/dnscrypt-blacklist-ips.txt` is a file containing IP addresses and prefixes to block. Queries will still be sent, and responses matching these IPs will be forwarded unmodified to clients, but with the response will have the `REFUSED` error code.
* `/var/log/dnscrypt-blocked.log` is an optional file to record the queries having matched one of the previous rules.

# IP rules set

The set of IP addresses to block should be presented as one rule per line, with optional comments starting with a `#` sign:

```
# This IP belongs to a spammer
10.1.4.99
# These two, too
172.16.144.8
172.16.188.4
```

Whole subnets can also be blocked, using wildcards:

```
# Block 172.16.*
172.16.*
```

IPv6 addresses are also supported:

```
[fdb2:cd9e:c9f8:861a:c1ab:94e3:ef95:1ec0]
```

# Names rules set

Names can be matched by suffix, prefix, or can be patterns found anywhere.

```
# This blocks example.com as well as www.example.com and anything within the example.com domain
*.example.com

# Actually, in that very common case, the wildcard is not even necessary:
example.com

# This blocks anything starting with "ads." or "tracker."
ads.*
tracker.*

# This blocks anything containing the "sex" substring
*sex*
```

These blacklists can be automatically constructed from freely available lists of names and IP addresses known to serve malware, ads and spam.

Try to avoid overlapping patterns, such as `*.example.com` and `ads.example.com`; in this example, the later is unnecessary.

# Preventing leaks of local queries

It is very common for applications to send queries that should remain private, but are actually sent to 3rd party resolvers.

These queries are typically sent to the default local domain configured on a given host, or to hardcoded nonexistent domains such as `.local` or `.localdomain`. These queries can leak a *lot* of private information and will never resolve to anything on a 3rd party resolver.

`dnscrypt-proxy`'s filtering module can be used to prevent these queries from going out.

Use the logging module to see the private domain names used by your devices, and block them with the filtering module.

# Log output

Sample log output:

```
Tue Dec 20 18:56:55 2016        192.168.100.57  ads.flurry.com  ads.*
Tue Dec 20 18:56:55 2016        192.168.100.57  ssl.google-analytics.com        *google-analytics*
Tue Dec 20 18:58:53 2016        192.168.100.4   local   *local
Tue Dec 20 18:58:53 2016        192.168.100.4   local   *local
Tue Dec 20 19:10:14 2016        192.168.100.4   www.google-analytics.com        *google-analytics*
Tue Dec 20 19:10:14 2016        192.168.100.4   www.google-analytics.com        *google-analytics*
Tue Dec 20 19:18:32 2016        192.168.100.4   www.google-analytics.com        *google-analytics*
Tue Dec 20 21:39:22 2016        192.168.100.9   dm.linternaute.com      *dm.linternaute.com
Tue Dec 20 21:39:36 2016        192.168.100.9   prof.estat.com  *prof.estat.com
Tue Dec 20 21:40:23 2016        192.168.100.9   dm.linternaute.com      *dm.linternaute.com
Tue Dec 20 21:52:08 2016        192.168.100.13  ads.20minutes.fr        ads.*
```

This includes the time stamp, the source IP address, the query name or IP, as well as the longest matching pattern.

# Rules sets

Ready-to-use rules sets are available: [Blacklists](../blob/master/Blacklists.md).