# Precompiled packages

Most BSD systems and Linux distributions provide precompiled packages for `dnscrypt-proxy`. However, these might be incomplete or obsolete.

Always up-to-date, [precompiled binaries of dnscrypt-proxy for Linux](https://github.com/jedisct1/dnscrypt-proxy-installer) are available. However, official packages shipped with distributions are recommended for better integration with the rest of the system.

The present documentation is valid for version `1.9.1` and beyond. For older versions, please refer to the provided `man` pages.

# Download and integrity check

`dnscrypt-proxy` can be downloaded here: [dnscrypt-proxy download](https://download.dnscrypt.org/dnscrypt-proxy/)

Signatures can be verified with the [Minisign](https://jedisct1.github.io/minisign/) tool:

```
minisign -VP RWQf6LRCGA9i53mlYecO4IzT51TGPpvWucNSCh1CBM0QTaLn73Y7GFO3 -m dnscrypt-proxy-1.9.5.tar.bz2
```

# Installation on Windows

Download and extract `dnscrypt-proxy-win32-full-*.zip` (32-bit version) or `dnscrypt-proxy-win64-full-*.zip` (64-bit version) from the [dnscrypt-proxy download](https://download.dnscrypt.org/dnscrypt-proxy/) location.

The resulting folder includes all you need to run the service; you can jump to the configuration part right in.

# Installation on MacOS

The easiest way to install `dnscrypt-proxy` is by using the [Homebrew](https://brew.sh/) package manager:

```
brew install dnscrypt-proxy
```

Or, if you need access to features that utilize plugins (e.g. ephemeral keys, local caching, etc.):

```
brew install dnscrypt-proxy --with-plugins
```

If you don't have Homebrew or don't need/want a compiler on your system, installing the [DNSCrypt OSXClient](https://github.com/alterstep/dnscrypt-osxclient) is a simple alternative to using `dnscrypt-proxy` directly.

# Installation on Linux

* [Instructions for ArchLinux](../master/Installation-on-Archlinux.md)
* [PRNG issues on Scaleway C1, Digital Ocean, and routers/embedded systems](../master/Running-dnscrypt-proxy-on-a-Linux-with-an-unseeded-RNG.md)

# Installation from source

The daemon is known to work on recent versions of MacOS, OpenBSD, Bitrig, NetBSD, Dragonfly BSD, FreeBSD, Linux, iOS (requires a jailbroken device), Android (requires a rooted device), Windows and more.

The proxy requires the [libsodium](https://libsodium.org/) crypto library. Precompiled packages are available for most operating systems, as `libsodium` and/or `libsodium-dev` (the later, if available, is required in order to compile the proxy from source).

The [ldns](https://www.nlnetlabs.nl/projects/ldns/) DNS library optional, but highly recommended. Precompiled packages are available for most operating systems as `ldns`, `libldns`, `ldns-dev` or `libldns-dev` (the `-dev` package, if available, is required in order to compile the proxy from source). ldns is required by some plugins.

If these library are compiled from source on a Linux system, the `/sbin/ldconfig` command must be issued after the installation, so that the system knows about the newly installed libraries.

On Fedora, RHEL and CentOS, you may need to add `/usr/local/lib` to the paths the dynamic linker is going to look at. Before issuing `ldconfig`, type (as `root`):

```
echo /usr/local/lib > /etc/ld.so.conf.d/usr_local_lib.conf
```

In order to support the `systemd` socket activation system (optional), install `pkg-config` and `libsystemd-dev` as well.

Now, download the latest `dnscrypt-proxy` version and extract it:

```
bunzip2 -cd dnscrypt-proxy-*.tar.bz2 | tar xvf -
cd dnscrypt-proxy-*
```

Compile and install it using the standard procedure:

```
./configure && make
```

On Linux, if you really need to use systemd and its socket activation system, configure and compile `dnscrypt-proxy` with this command line instead:

```
./configure --with-systemd && make
```

Finally, as `root`:

```
make install
```

The proxy will be installed as `/usr/local/sbin/dnscrypt-proxy` by default.

Having a dedicated system user, with no privileges and with an empty home directory, is highly recommended. For extra security, `dnscrypt-proxy` can `chroot()` to this user's home directory and drop root privileges for this user's uid as soon as possible.