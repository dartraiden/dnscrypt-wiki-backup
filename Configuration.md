A sample configuration file named `dnscrypt-proxy.conf` is present in the source distribution, and should also be available after installation, usually in `/etc` or `/usr/local/etc`. On Windows, that sample configuration file sits in the same directory as the proxy itself.

A copy of that file can also be viewed online: [dnscrypt-proxy.conf](https://raw.githubusercontent.com/jedisct1/dnscrypt-proxy/master/dnscrypt-proxy.conf).

# Choosing a resolver (all platforms)

A mandatory change to make to that configuration file is the `ResolverName` property. This sets the name of the DNS resolver the proxy is going to communicate with.

A list of free and public DNSCrypt-enabled resolvers and the features they support can be viewed online: [public DNSCrypt-enabled resolvers](https://dnscrypt.org/dnscrypt-resolvers.html).

`dnscrypt-proxy` itself reads that list from the `dnscrypt-resolvers.csv` file whose latest version can be downloaded here: [dnscrypt-resolvers.csv](https://download.dnscrypt.org/dnscrypt-proxy/dnscrypt-resolvers.csv) or from Github: [dnscrypt-resolvers.csv](https://raw.githubusercontent.com/jedisct1/dnscrypt-proxy/master/dnscrypt-resolvers.csv).

The first column is the resolver name. Pick one and use its name (for example `dnscrypt.org-fr`) as the value for the `ResolverName` property.

The source distribution of `dnscrypt-proxy` includes a script called `dnscrypt-update-resolvers.sh` that can be used to update the local copy of the CSV file.

# Configuring the local address (all platforms)

**Note:** If you want to run dnscrypt-proxy as a systemd service, the socket configuration cannot be done using the configuration file. See [dnscrypt-proxy and systemd](../master/Configuration.md#dnscrypt-proxy-and-systemd) below.

Another parameter in the configuration file you may want to comment out and change is `LocalAddress`. This is the address the proxy will listen to. With a few rare exceptions, the port has to remain 53. By default, it only listens to `127.0.0.1`, so it can only be reached from the local host.

If you are running this on a router, or you want to run a single proxy for all the devices on your network, this can be changed to `0.0.0.0:53` to listen to all IP addresses. But double check your firewall rules to make sure that the proxy can only be reached from the local network.

IPv6 addresses can also be specified, within brackets. For example: `[::1]:53`.

# Testing the current configuration (all platforms)

At this point, you may want to start the service and check that it works as expected.

On Windows, you need to open an elevated command prompt. This is important if you want to use the standard ports, which is probably what you want. On Windows 8.1 and Windows 10, press the Windows key + the X key and select "Windows Command Prompt (Admin)" or "Windows PowerShell (Admin)".

On Unix, you need to be logged as `root`, or (depending on the operating system/distribution) prepend `sudo` to the following commands.

Run `dnscrypt-proxy` followed by the path to the configuration file:

```
dnscrypt-proxy /usr/local/etc/dnscrypt-proxy.conf
```

On Windows, prepending `.\` may be required if the proxy is in the current directory. The sample configuration file also resides in the directory created after extracting the Windows archive:

```
.\dnscrypt-proxy.exe dnscrypt-proxy.conf
```

If you get the message:

```
[ERROR] Unable to bind (UDP) [Address already in use [WSAEADDRINUSE ]]
```

chances are that you tried to start the proxy from a limited (non-elevated) command prompt. Or, on Unix, as a non-root user.

Unless the `Daemonize` option was changed to `yes`, the command should not return. This is good, and means that the proxy is running.

If this is actually the case, send your first query to the proxy to check that the whole chain works as expected. `dnscrypt-proxy` includes the `hostip` command. A tiny utility that can be used to send DNS queries to arbitrary resolvers.

```
hostip -r 127.0.0.1 example.com
```

On Windows, typing `.\hostip.exe` may be required instead of `hostip`.

Replace `127.0.0.1` with the IP address of the DNSCrypt proxy if you changed this parameter. This should print the IP addresses of the `example.com` host.

Congratulation, you sent your first encrypted, authenticated query using the DNSCrypt protocol!

To confirm that the response came from the resolver you chose and not from your ISP:

```
hostip -r 127.0.0.1 resolver.dnscrypt.org
```

This special query (`resolver.dnscrypt.org`) returns the IP address of the resolver you are likely to be using (and it works with or without DNSCrypt). It should match the IP of the resolver you chose.

If everything appears to work as expected, shut down the proxy by hitting `Control`+`C`. This should get you back to the interactive command line prompt.

# Making the DNSCrypt proxy service permanent

Before running the service for real, maybe review the configuration file again. There might be a couple extra things you may want to change, or extra features you may want to enable.

Once you are happy with the current configuration file, and you confirmed that it works as expected, the time has come to make the service run permanently.

## Installing the service on Windows

The proxy can run as a standard Windows service. In order to install that service, type the following command:

```
.\dnscrypt-proxy.exe --install-with-config-file=dnscrypt-proxy.conf
```

The `hostip` commands previously described should working under this new configuration, even if you reboot the system.

If this is actually the case, change your system DNS settings to `127.0.0.1` or whatever IP address you chose to assign to the proxy, and you are all set!

## Uninstalling the service on Windows

In order to stop the proxy and remove the service (such as to reinstall it right after, with a different configuration), change your system DNS settings to what they used to be, and type:

```
.\dnscrypt-proxy.exe --uninstall
```

## IPv6 DNS addresses on Windows

Do you have IPv6 connectivity? No problem!

In the preferences pane for a given network connection, Windows has IPv6-specific settings under "Internet Protocol Version 6 (TCP/IPv6)".

The DNS server addresses have to be IPv6 addresses, so enter the IPv6 representation of `127.0.0.1`:

```
::FFFF:127.0.0.1
```

Done. You can now enjoy the DNSCrypt proxy both for IPv4 and IPv6 connections.

# Sharing the proxy with other devices on the local network

By default, only the local machine running the proxy can use it.

However, it can be convenient to make it accessible from any device on the local network. For example, smartphones, tablets and smart light bulbs can leverage a single machine running `dnscrypt-proxy` as a DNS resolver.

In order to do so, set `LocalAddress` to `0.0.0.0:53`.

And use the external IP address of the machine in the DNS settings of all the devices you want to protect.

## dnscrypt-proxy and systemd

The proxy can also be started by systemd, and incoming connections can be handled by its socket activation mechanism.

Install the service with:

```
systemctl enable dnscrypt-proxy
```

and start it with:

```
systemctl start dnscrypt-proxy
```

To do a sanity check on the installation, run

```
lsof -i -n
```

and make sure the process is indeed listening on the desired address and port.

**Note**: If you do not run `lsof` as root, it will only print ports opened by the the user who ran the command.

On most distributions, the systemd socket file is configured to listen to `127.0.0.1:53` by default. In order to change this:

```
sed 's/127.0.0.1:53/<new ip and port>/g' \
    /usr/lib/systemd/system/dnscrypt-proxy.socket \
  > /etc/systemd/system/dnscrypt-proxy.socket

systemctl daemon-reload
```