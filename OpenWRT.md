# Troubleshooting

## i did follow the openWRT guide and is not working

**Q: does your /etc/config/dnscrypt-proxy look like this ?**

```
config dnscrypt-proxy

    option address '127.0.0.1'

    option port    '5353'

    option resolver        'cisco'

    option resolvers_list  '/usr/share/dnscrypt-proxy/dnscrypt-resolvers.csv'

  # option ephemeral_keys '1'
```

A: yes it does

take a look at that csv file because most likely there is no cisco in the file

## dnscrypt-proxy does not auto start

**Q1:did you follow the wiki and put */etc/rc.local*, above the line "*exit 0*" ?**

```
sleep 10
```
```
/etc/init.d/dnscrypt-proxy start
```

A1: yes i did

**Q2: do you have a adblock script ?**

A2: yes i do

if you have a adblocker script it needs to be above the adblocker script

```
# Put your custom commands here that should be executed once
```
```
# the system init finished. By default this file does nothing.
```
```
sleep 10
```
```
/etc/init.d/dnscrypt-proxy start
```
```
/root/adblocker.sh
```
```
exit 0
```