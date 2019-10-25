# OpenStack構築手順書

以下の手順書を参照する。

<https://docs.openstack.org/install-guide/environment-packages-rdo.html>

## OpenStackリポジトリを有効にする

以下のコマンドを実行する。

```bash
sudo yum install centos-release-openstack-stein
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
sudo yum install python-openstackclient
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
sudo yum install openstack-selinux
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

パッケージをインストールする。

```bash
sudo yum install mariadb mariadb-server python2-PyMySQL
```
