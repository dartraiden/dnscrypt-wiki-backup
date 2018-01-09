In order to compile the example plugins, it is necessary to add `libtool` to the `options` list in the `PKGBUILD` file as follows:

```
options=(libtool)
```

Then, the package can be built with the `makepkg` command.

`dnscrypt-proxy` can also be installed in two alternative ways:

* By installing the stable version of the package as follows:

```
sudo pacman -S dnscrypt-proxy
```

* By installing the git version of the package (unstable, not guaranteed to work, be secure or compile) as follows:

```
yaourt -S dnscrypt-proxy-git
```

There is a difference between these two packages: the git version in AUR is ready to be used with the default plugins and the example script to generate the domains blacklist. If you prefer the stable version, use this [github](https://github.com/FixedTorres/dnscrypt-proxy) repository configured for the default plugins, that also includes the script to generate the domains blacklist.

Once done and with the `options=(libtool)` option, plugins are located in the `/usr/lib/dnscrypt-proxy/` directory.