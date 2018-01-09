Occasionally, you may want to bypass DNSCrypt for specific zones.

For example, a private zone may be only served by an enterprise's internal DNS server.

A home router may also serve names within a local zone such as `.local`, `.localdomain` or `.lan` to reach devices whose IP addresses have been assigned by DHCP.

In these situations, you might want to bypass DNSCrypt and directly send unauthenticated, unencrypted queries to these local DNS servers, only for these specific zones.

This can be done with dnscrypt-proxy's `forwarding` module, that can be enabled from the configuration file:

```
Forward domains:"test private localdomain lan" to:"192.168.100.254"
```

What follows `domains:` is a set of zones that will *not* use DNSCrypt. Queries for these will be sent as regular DNS queries to one or more space-delimited local resolvers listed after the `to:` keyword.