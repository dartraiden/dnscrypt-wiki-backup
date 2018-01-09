Using plugins that are included by default, `dnscrypt-proxy` can make your online experience faster.

# Direct AAAA responses

Most applications unconditionally attempt to retrieve IPv6 addresses in addition to IPv4 addresses for every resolved name.

This happens even on hosts that do not have IPv6 connectivity. As a result, a significant amount of DNS queries are actually useless, and unnecessarily increase the latency of many operations.

When the `BlockIPv6` option is turned on in the `dnscrypt-proxy` configuration file, the proxy directly responds with "there are no IPv6 addresses for that name" to all requests about IPv6 addresses (`AAAA`). These queries do not need to wait any more for a response from the upstream resolver, that would eventually be discarded.

It is recommended to enable it unless your network actually has IPv6 connectivity:

`BlockIPv6 yes`

# Local DNS cache

The proxy can also cache the responses to the most frequent and recent queries. This avoids having to repeatedly send very common requests to the resolver, and have to wait for (likely) the same answer every time.

Although web browsers and operating systems such as MacOS and Windows have a built-in DNS cache, a shared cache remains useful for applications implementing their own DNS resolution code, and for multiple devices sharing the same network.

In order to enable the cache, the following line has to be present in the configuration file:

`LocalCache on`