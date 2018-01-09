If libsodium blocks during the `make check` operation, or `dnscrypt-proxy` blocks at startup, it is very likely that your operating system is unable to safely generate numbers.

This can be confirmed with the following command:

```
cat /proc/sys/kernel/random/entropy_avail
```

If the command returns `0` or a very low number (`< 64`), and you are not running an obsolete kernel, this is very likely to be the case.

As a workaround, install the `rng-tools` package:

```
apt-get install rng-tools
```

And check the value of `/proc/sys/kernel/random/entropy_avail` again. If the value didn't go any higher, install haveged:

```
apt-get install haveged
```