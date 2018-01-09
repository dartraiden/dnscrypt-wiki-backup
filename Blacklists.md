Ready-to-use rules sets that can be enabled in the dnscrypt-proxy configuration file.

# Sample blacklists of domain names

Can be enabled with:

```
BLackList domains:/usr/local/etc/dnscrypt-blacklist-domains.txt
```

or, to log blocked queries:

```
BLackList domains:/usr/local/etc/dnscrypt-blacklist-domains.txt logfile:/tmp/dnscrypt-blocked.log
```

* The [MyBase](https://download.dnscrypt.org/blacklists/domains/mybase.txt) set is a multi-purpose list updated daily from public sources, in order to block common malware, ads and trackers. It's automatically generated from a python script available in the [dnscrypt-proxy contrib directory](https://github.com/jedisct1/dnscrypt-proxy/tree/master/contrib).