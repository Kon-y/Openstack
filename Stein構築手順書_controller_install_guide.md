# controller install guide

<https://docs.openstack.org/swift/stein/install/controller-install-rdo.html>

adminの資格情報を使用して管理者専用のCLIコマンドにアクセス

```bash
. admin-openrc
```

swiftユーザを作成する。

```bash
openstack user create --domain default --password-prompt swift
User Password:
Repeat User Password:
+-----------+----------------------------------+
| Field     | Value                            |
+-----------+----------------------------------+
| domain_id | default                          |
| enabled   | True                             |
| id        | d535e5cbd2b74ac7bfb97db9cced3ed6 |
| name      | swift                            |
+-----------+----------------------------------+
```

swiftユーザーにadminロールを追加する。

```bash
openstack role add --project service --user swift admin
```

swiftサービスエンティティを作成する。

```bash
openstack service create --name swift \
  --description "OpenStack Object Storage" object-store
```

Object StorageサービスAPIエンドポイントを作成する。

```bash
openstack endpoint create --region RegionOne \
  object-store public http://controller:8080/v1/AUTH_%\(project_id\)s
```
