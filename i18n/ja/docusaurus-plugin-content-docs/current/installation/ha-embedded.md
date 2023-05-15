---
title: "組み込みDBでの高可用"
weight: 40
---

:::info Veresion Gate
[v1.19.5+k3s1](https://github.com/k3s-io/k3s/releases/tag/v1.19.5%2Bk3s1) の時点で完全対応。 
[v1.19.1+k3s1]での実験的なサポート(https://github.com/k3s-io/k3s/releases/tag/v1.19.1%2Bk3s1)
:::

:::note Notice: Deprecated Dqlite
K3s v1.19.1リリースでは、Embedded etcdが実験的なDqliteを置き換えた。これは、破格の変更です。実験的Dqliteから埋め込みetcdへのアップグレードはサポートされていないことに注意してください。アップグレードを試みた場合、それは成功せず、データは失われるでしょう。
:::

:::caution
SD カードで動作する Raspberry Pis などの低速なディスクでは、組み込み型 etcd (HA) の性能に問題がある場合があります。
:::

## 新しいクラスタ
このモードでK3sを実行するには、サーバーノードの数が奇数である必要があります。3つのノードで始めることをお勧めします。

まず、クラスタリングを有効にするために `cluster-init` フラグと、追加のサーバーをクラスタに参加させるための共有秘密として使用されるトークンを指定してサーバーノードを起動します。
```bash
curl -sfL https://get.k3s.io | K3S_TOKEN=SECRET sh -s - server --cluster-init
```

1台目のサーバーを起動したら、2台目と3台目のサーバーを共有秘密を使ってクラスタに参加させます：
```bash
curl -sfL https://get.k3s.io | K3S_TOKEN=SECRET sh -s - server --server https://<ip or hostname of server1>:6443
```

2台目と3台目のサーバーがクラスタの一部になったことを確認します：

```bash
$ kubectl get nodes
NAME        STATUS   ROLES                       AGE   VERSION
server1     Ready    control-plane,etcd,master   28m   vX.Y.Z
server2     Ready    control-plane,etcd,master   13m   vX.Y.Z
```

これで、可用性の高いコントロールプレーンができました。正常にクラスタ化されたサーバは、`--server`引数で、追加のサーバとワーカーノードを参加させるために使用できます。追加のワーカーノードをクラスタに参加させるのは、単一のサーバクラスタと同じ手順に従います。

すべてのサーバーノードで同じでなければならない設定フラグがいくつかあります：         

* ネットワーク関連のフラグです： ネットワーク関連フラグ： `--cluster-dns`, `--cluster-domain`, `--cluster-cidr`, `--service-cidr`
* 特定のコンポーネントのデプロイを制御するフラグです： `disable-helm-controller`, `--disable-kube-proxy`, `--disable-network-policy` および `--disable` に渡されるすべてのコンポーネント。
* 機能関連フラグ セキュリティー暗号化`

## 既存クラスタ
デフォルトの埋め込みSQLiteデータベースを使用している既存のクラスタがある場合、`--cluster-init`フラグを指定してK3sサーバを再起動するだけで、etcdに変換できます。一度そうすれば、上記のように追加のインスタンスを追加することができるようになります。

etcdデータストアがディスク上に見つかった場合、そのノードはすでに初期化されているか、クラスタに参加しているため、データストアの引数（`--cluster-init`, `--server`, `--datastore-endpoint`, etc）は無視されます。

>**重要:** K3s v1.22.2 およびそれ以降のバージョンでは、SQLite から etcd への移行をサポートしています。古いバージョンでは、既存のサーバーに `--cluster-init` を追加すると、新しい空のデータストアが作成されます。

