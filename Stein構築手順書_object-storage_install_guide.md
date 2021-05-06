# object storage install guide

## networkを構成する

first node management interfaceを設定する

```bash
man-ipaddr=10.0.0.10
man-netkmask=24
man-defaultgw=10.0.0.1
```

Configure name resolution

```bash
cd /tmp
old_prefix="-$(date +%F.%s)"
cp -afv /etc/hosts{,${old_prefix}}
vi /etc/hosts
diff -u /etc/hosts{${old_prefix},}
```

second node management interfaceを設定する

```bash
man-ipaddr=10.0.0.10
man-netkmask=24
man-defaultgw=10.0.0.1
```

Configure name resolution

```bash
cd /tmp
old_prefix="-$(date +%F.%s)"
cp -afv /etc/hosts{,${old_prefix}}
vi /etc/hosts
diff -u /etc/hosts{${old_prefix},}
```
