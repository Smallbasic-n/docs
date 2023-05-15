---
title: バックアップと復元
weight: 26
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

K3sのバックアップとリストアの方法は、どのタイプのデータストアを使用するかによって異なります。

- [外部データストアによるバックアップとリストア](#backup-and-restore-with-external-datastore)
- [組み込み型etcdデータストアによるバックアップとリストア](#backup-and-restore-with-embedded-etcd-datastore)

### 外部データストアによるバックアップとリストア

外部データストアを使用する場合、バックアップとリストア操作はK3sの外部で処理されます。データベース管理者は、外部データベースをバックアップするか、スナップショットまたはダンプから復元する必要があります。

定期的にスナップショットを取得するようにデータベースを構成することをお勧めします。

データベースのスナップショットを取得し、そこからデータベースを復元する方法の詳細については、データベースの公式ドキュメントを参照してください：

- [MySQL公式ドキュメント](https://dev.mysql.com/doc/refman/8.0/en/replication-snapshot-method.html)
- [PostgreSQL公式ドキュメント](https://www.postgresql.org/docs/8.3/backup-dump.html)
- [etcd公式ドキュメント](https://etcd.io/docs/latest/op-guide/recovery/)

### 組み込み型etcdデータストアによるバックアップとリストア
:::info Version Gate

[v1.19.1+k3s1](https://github.com/k3s-io/k3s/releases/tag/v1.19.1%2Bk3s1)から利用可能です。

:::

このセクションでは、K3sクラスタデータのバックアップを作成する方法と、バックアップからクラスタを復元する方法について説明します。

>**Note on Single-Server with embedded SQLite:** 現在、SQLiteのバックアップはサポートされていません。代わりに、`/var/lib/rancher/k3s/server`のコピーを作成し、K3sを削除してください。

#### スナップショットの作成

スナップショットはデフォルトで有効です。

スナップショットのディレクトリはデフォルトで `${data-dir}/server/db/snapshots` となる。data-dirのデフォルトは `/var/lib/rancher/k3s` で、`--data-dir`フラグを設定することで変更することができる。

スナップショット間隔や保持するスナップショット数を設定するには、[オプション](#options)を参照してください。

#### スナップショットからのクラスタの復元

K3sがバックアップからリストアされると、古いデータディレクトリは`${data-dir}/server/db/etcd-old/`に移動されます。その後、K3sは新しいデータディレクトリを作成し、1つのetcdメンバーを持つ新しいK3sクラスタでetcdを起動することによって、スナップショットを復元しようとします。

バックアップからクラスタをリストアするには
<Tabs>
<TabItem value="シングルサーバ">

K3sを`--cluster-reset`オプションで実行し、`--cluster-reset-restore-path`も指定する：
```bash
k3s server \
  --cluster-reset \
  --cluster-reset-restore-path=<PATH-TO-SNAPSHOT>
```

**結果:** ログに、K3sがフラグなしで再起動できる旨のメッセージが表示されます。再度k3sを起動すると正常に実行され、指定したスナップショットから復元されるはずです。
</TabItem>

<TabItem value="高可用">

この例では、`S1`、`S2`、`S3`の3つのサーバーがあります。スナップショットは`S1`に配置されています。

1. S1では、`--cluster-reset`オプションでK3sを起動し、`--cluster-reset-restore-path`も指定する：
    ```bash
    k3s server \
      --cluster-reset \
      --cluster-reset-restore-path=<PATH-TO-SNAPSHOT>
    ```

**結果:** ログに、K3sはフラグなしで再起動できる旨のメッセージが表示されました。

2. S2、S3で、K3sを停止します。その後、データディレクトリ `/var/lib/rancher/k3s/server/db/` を削除します：
    ```bash
    systemctl stop k3s
    rm -rf /var/lib/rancher/k3s/server/db/
    ```

3. S1では、再びK3を開始する：
    ```bash
    systemctl start k3s
    ```

4. S2、S3では、K3を再度起動し、復元したクラスタに参加する：
    ```bash
    systemctl start k3s
    ```

</TabItem>
</Tabs>

#### オプション

これらのオプションは、コマンドラインで渡すこともできますが、[設定ファイル](../installation/configuration.md#configuration-file )で渡すとより使いやすいかもしれません。

| オプション                            ｜説明
| -------------------------------------| --------------- |
| `--etcd-disable-snapshots`           ｜ 自動 etcd スナップショットを無効にする。
| `--etcd-snapshot-schedule-cron` value｜スナップショットの間隔をcronで指定します。
| `--etcd-snapshot-retention` value    ｜保持するスナップショット数（デフォルト：5）｜...
| `--etcd-snapshot-dir` value          ｜dbスナップショットを保存するディレクトリ。(デフォルトの場所: `${data-dir}/db/snapshots`) |.
| `--cluster-reset`                    | 全てのピアを消去し、新しいクラスタの唯一のメンバーとなる。これは環境変数 `[$K3S_CLUSTER_RESET]` で設定することもできる。
| `--cluster-reset-restore-path` value ｜リストアするスナップショットファイルへのパス。


#### S3 互換 API サポート

K3sは、S3互換のAPIを持つシステムへのetcdスナップショットの書き込みとシステムからのetcdスナップショットの復元をサポートしています。S3サポートは、オンデマンドおよびスケジュールされたスナップショットの両方で利用可能です。

以下の引数が `server` サブコマンドに追加されました。これらのフラグは `etcd-snapshot` サブコマンドにも存在しますが、冗長性を避けるために `--etcd-s3` 部分は削除されています。

| オプション                 ｜説明
| ---------------------------| --------------- |
| `--etcd-s3`                ｜ S3へのバックアップを有効にする。
| `--etcd-s3-endpoint`       | S3 エンドポイントのurl |
| `--etcd-s3-endpoint-ca`    ｜ S3エンドポイントに接続するためのS3カスタムCA証明書。
| `--etcd-s3-skip-ssl-verify`｜S3 SSL証明書の検証を無効にする｜?
| `--etcd-s3-access-key`     | S3アクセスキー
| `--etcd-s3-secret-key`      | S3シークレットキー
| `--etcd-s3-bucket`         ｜S3 バケット名｜。
| `--etcd-s3-region`          | S3リージョン/バケットロケーション（オプション）。
| `--etcd-s3-folder`          ｜S3 フォルダ｜

オンデマンドでetcdのスナップショットを実行し、S3に保存する場合：
```bash
k3s etcd-snapshot \
  --s3 \
  --s3-bucket=<S3-BUCKET-NAME> \
  --s3-access-key=<S3-ACCESS-KEY> \
  --s3-secret-key=<S3-SECRET-KEY>
```

S3からオンデマンドでetcdスナップショットのリストアを実行するには、まずK3sが実行されていないことを確認します。その後、以下のコマンドを実行します：
```bash
k3s server \
  --cluster-init \
  --cluster-reset \
  --etcd-s3 \
  --cluster-reset-restore-path=<SNAPSHOT-NAME> \
  --etcd-s3-bucket=<S3-BUCKET-NAME> \
  --etcd-s3-access-key=<S3-ACCESS-KEY> \
  --etcd-s3-secret-key=<S3-SECRET-KEY>
```

#### Etcd スナップショットおよびリストアサブコマンド

k3sは、etcdスナップショットを操作するためのサブコマンドのセットをサポートしています。

| サブコマンド |説明
| ----------- | --------------- |
| delete     ｜指定されたスナップショットを削除する。
| ls、list、l｜スナップショットを一覧表示する｜。
| prune      ｜設定した保持回数を超えるスナップショットを削除する｜。
| save       ｜etcdのスナップショットを即座にトリガーする。

:::note
save`サブコマンドは `k3s etcd-snapshot` と同じです。後者はいずれ非推奨となり、前者に取って代わられる予定です。
:::

これらのコマンドは、etcdスナップショットがローカルに保存されているか、S3互換のオブジェクトストアに保存されているかに関わらず、期待通りに実行されます。

etcd snapshotサブコマンドの追加情報については、`k3s etcd-snapshot` を実行してください。

S3からスナップショットを削除する。

```bash
k3s etcd-snapshot delete          \
  --s3                            \
  --s3-bucket=<S3-BUCKET-NAME>    \
  --s3-access-key=<S3-ACCESS-KEY> \
  --s3-secret-key=<S3-SECRET-KEY> \
  <SNAPSHOT-NAME>
```

デフォルトの保持ポリシー(5)でローカルスナップショットを刈り込みます。`prune` サブコマンドは、デフォルトの保持ポリシーを上書きするための追加フラグ `--snapshot-retention` を取ります。

```bash
k3s etcd-snapshot prune
```

```bash
k3s etcd-snapshot prune --snapshot-retention 10
```
