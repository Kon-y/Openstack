# OpenStack構築手順書

controllerサーバにインストール

以下の手順書を参照する。

<https://docs.openstack.org/install-guide/environment-packages-rdo.html>

## OpenStackリポジトリを有効にする

以下のコマンドを実行する。

```bash
sudo yum install -y centos-release-openstack-stein
```

```console
===============================================================================================================================
 Package                                      Arch                 Version                          Repository            Size
===============================================================================================================================
Installing:
 centos-release-openstack-stein               noarch               1-1.el7.centos                   extras               5.3 k
Installing for dependencies:
 centos-release-ceph-nautilus                 noarch               1.1-6.el7.centos                 extras               4.8 k
 centos-release-qemu-ev                       noarch               1.0-4.el7.centos                 extras                11 k
 centos-release-storage-common                noarch               2-2.el7.centos                   extras               5.1 k
 centos-release-virt-common                   noarch               1-1.el7.centos                   extras               4.5 k

Transaction Summary
=====================
Install  1 Package (+4 Dependent packages)

Total download size: 31 k
Installed size: 24 k
Is this ok [y/d/N]:

▶[y]を入力する。

warning: /var/cache/yum/x86_64/7/extras/packages/centos-release-openstack-stein-1-1.el7.centos.noarch.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY
Public key for centos-release-openstack-stein-1-1.el7.centos.noarch.rpm is not installed
(1/5): centos-release-openstack-stein-1-1.el7.centos.noarch.rpm                                         | 5.3 kB  00:00:00
(2/5): centos-release-storage-common-2-2.el7.centos.noarch.rpm                                          | 5.1 kB  00:00:00
(3/5): centos-release-qemu-ev-1.0-4.el7.centos.noarch.rpm                                               |  11 kB  00:00:00
(4/5): centos-release-ceph-nautilus-1.1-6.el7.centos.noarch.rpm                                         | 4.8 kB  00:00:00
(5/5): centos-release-virt-common-1-1.el7.centos.noarch.rpm                                             | 4.5 kB  00:00:00
-------------------------------------------------------------------------------------------------------------------------------
Total                                                                                           92 kB/s |  31 kB  00:00:00
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Importing GPG key 0xF4A80EB5:
 Userid     : "CentOS-7 Key (CentOS 7 Official Signing Key) <security@centos.org>"
 Fingerprint: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
 Package    : centos-release-7-6.1810.2.el7.centos.x86_64 (installed)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Is this ok [y/N]:
```

再度「y」を入力する。
 └> 「Complete!」と出力されることを確認する。

## インストールを確定する

以下のコマンドを実行する。

```bash
sudo yum upgrade
```

```bash
Transaction Summary
===============================================================================================================================
Install  14 Packages (+6 Dependent packages)
Upgrade  57 Packages

Total download size: 183 M
Is this ok [y/d/N]:

▶「y」を入力する。

Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-Cloud
Importing GPG key 0x764429E6:
 Userid     : "CentOS Cloud SIG (http://wiki.centos.org/SpecialInterestGroup/Cloud) <security@centos.org>"
 Fingerprint: 736a f511 6d9c 40e2 af6b 074b f9b9 fee7 7644 29e6
 Package    : centos-release-openstack-stein-1-1.el7.centos.noarch (@extras)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-Cloud
```

再度「y」を入力する。
 └> Complete!と表示されることを確認する。

## OpenStackクライアントをインストールする

```bash
sudo yum install -y python-openstackclient
```

```console
Install  1 Package (+94 Dependent packages)

Total download size: 27 M
Installed size: 109 M
Is this ok [y/d/N]:

▶「y」を入力する。
 └> Complete!と表示されることを確認する。
```

openstack-selinuxOpenStackサービスのセキュリティポリシーを自動的に管理するためのパッケージをインストールする。

```bash
sudo yum install -y openstack-selinux
```

```console
Install  1 Package (+1 Dependent package)

Total download size: 222 k
Installed size: 299 k
Is this ok [y/d/N]:

「y」を入力する。
 └> Complete!と表示されることを確認する。
```

## 【SQLデータベース】コンポーネントをインストール

<https://docs.openstack.org/install-guide/environment-sql-database-rdo.html>

コンポーネントのインストールと構成

```bash
sudo yum install -y mariadb mariadb-server python2-PyMySQL

sudo su -

cat <<__CONF__ | tee /etc/my.cnf.d/openstack.cnf
[mysqld]
bind-address = 172.31.0.129 ## -> bind-address キーをコントローラーノードの管理IPアドレスに設定する。

default-storage-engine = innodb
innodb_file_per_table = on
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8
__CONF__

dbサービスの有効化及び自動起動設定

systemctl enable mariadb.service
systemctl start mariadb.service
systemctl status mariadb.service
  ## -> Active: active (running)になっていることを確認する。
```

```bash
mysql_secure_installation
```

```bash
NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): enter入力する。

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] Yを入力する。
New password:
Re-enter new password:
  ## -> ... Success!が出力されることを確認する。

By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] Yを入力する。
　## -> ... Success!が表示されることを確認する。

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] Yを入力する。
  ## -> ... Success!が出力されることを確認する。

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] Yを入力する。
  ## -> [Dropping test database],[Removing privileges on test database]ともに... Success!が出力されることを確認する。

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] Yを入力する。
  ## ->  ... Success!が出力されることを確認する。

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

## メッセージキューのインストール

<https://docs.openstack.org/install-guide/environment-messaging-rdo.html>

```bash
yum install -y rabbitmq-server
```

メッセージキューサービスの有効化及び自動起動設定

```bash
systemctl enable rabbitmq-server.service
systemctl start rabbitmq-server.service
systemctl status rabbitmq-server.service
  ## -> Active: active (running)になっていることを確認する。
```

ユーザーを追加

```bash
rabbitmqctl add_user openstack RABBIT_PASS
  ## -> 「Creating user "openstack"」が出力されることを確認する。
```

openstackユーザーの構成、書き込み、および読み取りアクセス許可

```bash
rabbitmqctl set_permissions openstack ".*" ".*" ".*"
  ## -> Setting permissions for user "openstack" in vhost "/"が出力されることを確認する。
```

## Memcached

~~~
<https://docs.openstack.org/install-guide/environment-memcached-rdo.html>

コンポーネントのインストールと構成
~~~

```bash
yum install -y memcached python-memcached
```

```bash
cd /tmp
old_prefix="-$(date +%F.%s)"
cp -afv /etc/sysconfig/memcached{,${old_prefix}}
vi /etc/sysconfig/memcached
diff -u /etc/sysconfig/memcached{${old_prefix},}
  ## -> 変更箇所が差分として表示されることを確認する。
```

memcachedの有効化及び自動起動設定

```bash
systemctl enable memcached.service
systemctl start memcached.service
systemctl status memcached.service
  ## -> Active: active (running)と表示されていることを確認する。
```

## etcd

~~~
<https://docs.openstack.org/install-guide/environment-etcd-rdo.html>

コンポーネントのインストールと構成
~~~

```bash
yum -y install etcd
```

configファイルの編集

```bash
old_prefix="-$(date +%F.%s)"
cp -afv /etc/etcd/etcd.conf{,${old_prefix}}
vi /etc/etcd/etcd.conf "+[Member]"
```

例のように設定する。

```bash
  #[Member]
  ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
  ETCD_LISTEN_PEER_URLS="http://10.0.0.11:2380"
  ETCD_LISTEN_CLIENT_URLS="http://10.0.0.11:2379"
  ETCD_NAME="controller"
  #[Clustering]
  ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.0.0.11:2380"
  ETCD_ADVERTISE_CLIENT_URLS="http://10.0.0.11:2379"
  ETCD_INITIAL_CLUSTER="controller=http://10.0.0.11:2380"
  ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster-01"
  ETCD_INITIAL_CLUSTER_STATE="new"
```

```bash
diff -u /etc/etcd/etcd.conf{${old_prefix},}
  ## -> 変更箇所が差分として表示されることを確認する。
```

etcdサービスの有効化と自動起動設定

```bash
systemctl enable etcd
systemctl start etcd
systemctl status etcd
  ## -> Active: active (running)と表示されていることを確認する。
```

## openstackサービスをインストール

~~~
サービス間の関係図
<https://docs.openstack.org/install-guide/get-started-conceptual-architecture.html>

アーキテクチャ
<https://docs.openstack.org/install-guide/overview.html>
Controller     * 1 cpu*2:Mem*8gb storage*1oogb nic*2(f-nw,b-nw)
Compute        * 1 cpu*4:Mem*8gb storage*1oogb nic*2(f-nw,b-nw)
Block Storage  * 1 cpu*1:Mem*4gb storage*1oogb(+/dev/sdb,+/dev/sdc) nic*1(b-nw)
Object Storage * 2 cpu*1:Mem*4gb storage*1oogb(+/dev/sdb,+/dev/sdc) nic*1(b-nw)
~~~
