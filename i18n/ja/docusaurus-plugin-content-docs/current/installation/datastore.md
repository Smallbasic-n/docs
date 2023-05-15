---
title: "クラスタデータストアオプション"
weight: 50
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

etcd以外のデータストアを使用してKubernetesを実行する機能は、K3sを他のKubernetesディストリビューションと区別しています。この機能は、Kubernetesの運用者に柔軟性を提供します。利用可能なデータストアのオプションにより、ユースケースに最適なデータストアを選択することができます。例えば、以下のような場合です：

例えば、以下のような場合です。 * チームにetcdの運用ノウハウがない場合、MySQLやPostgreSQLのようなエンタープライズグレードのSQLデータベースを選択することができます。
* CI/CD 環境でシンプルで短命なクラスタを実行する必要がある場合、組み込みの SQLite データベースを使用することができます。
* Kubernetesをエッジに展開し、高可用性のソリューションが必要だが、エッジでデータベースを管理する運用上のオーバーヘッドを許容できない場合、embedded etcdの上に構築されたK3sの組み込みHAデータストアを使用することができます。

K3sは以下のデータストアオプションをサポートしています：

* Embedded [SQLite](https://www.sqlite.org/index.html)
* [PostgreSQL](https://www.postgresql.org/) (バージョン 10.7、11.5、および 14.2 に対して認証済み)
* [MySQL](https://www.mysql.com/) (バージョン 5.7 および 8.0 に対して認証済み)
* [MariaDB](https://mariadb.org/) (バージョン 10.6.8 に対して認証済み)
* [etcd](https://etcd.io/) (バージョン 3.5.4 に対して認証済み)
* 高可用性のための組み込み型etcd

### 外部データストアの構成パラメーター
PostgreSQL、MySQL、etcdのような外部データストアを使用したい場合は、K3sがそれに接続する方法を知っているように、`datastore-endpoint`パラメータを設定する必要があります。また、接続の認証と暗号化を設定するためのパラメータを指定することもできます。以下の表はこれらのパラメータをまとめたもので、CLIフラグまたは環境変数として渡すことができます。

  CLI Flag | Environment Variable | Description
  ------------|-------------|------------------
 `--datastore-endpoint` | `K3S_DATASTORE_ENDPOINT` | PostgreSQL, MySQL, etcdの接続文字列を指定する。データストアへの接続を記述するための文字列です。この文字列の構造は各バックエンドに固有であり、以下に詳細を示します。
 `--datastore-cafile` | `K3S_DATASTORE_CAFILE` | データストアとの通信を安全にするためのTLS認証局（CA）ファイルです。データストアがカスタム認証局によって署名された証明書を使用してTLS上のリクエストを提供する場合、K3sクライアントが証明書を適切に検証できるように、このパラメータを使用してそのCAを指定することができます。|                              
|  `--datastore-certfile` | `K3S_DATASTORE_CERTFILE` | データストアへのクライアント証明書ベースの認証に使用するTLS証明書ファイルです。この機能を使用するには、データストアがクライアント証明書ベースの認証をサポートするように設定されている必要があります。このパラメータを指定する場合、`datastore-keyfile`パラメータも指定する必要があります。 |     
|  `--datastore-keyfile` | `K3S_DATASTORE_KEYFILE` | データストアへのクライアント証明書による認証に使用するTLSキーファイルです。詳細は、前の `datastore-certfile` パラメータを参照してください。 |

ベストプラクティスとして、これらのパラメータをコマンドライン引数ではなく環境変数として設定し、データベースの認証情報またはその他の機密情報がプロセス情報の一部として公開されないようにすることをお勧めします。

### データストアエンドポイントの形式と機能
前述のように、`datastore-endpoint`パラメータに渡される値の形式は、データストアのバックエンドに依存します。以下では、サポートされている各外部データストアのこのフォーマットと機能の詳細を説明します。
<Tabs>
<TabItem value="PostgreSQL">
  最も一般的な形式として、PostgreSQLのdatastore-endpointパラメータは以下のような形式を持ちます：

  `postgres://username:password@hostname:port/database-name`

  より高度な設定パラメータが利用可能です。これらの詳細については、https://godoc.org/github.com/lib/pq を参照してください。

  データベース名を指定し、それが存在しない場合、サーバーはそれを作成しようとします。

  エンドポイントとして `postgres://` だけを指定した場合、K3s は次のことを試みます：

  - ユーザー名とパスワードとして `postgres` を使用して localhost に接続する。
  - `kubernetes`という名前のデータベースを作成する。 

</TabItem>
<TabItem value="MySQL / MariaDB">

  最も一般的な形式では、MySQL と MariaDB の `datastore-endpoint` パラメータは次のような形式をとります：

  `mysql://username:password@tcp(hostname:3306)/database-name` です。

  より高度な設定パラメータが利用可能です。これらの詳細については、https://github.com/go-sql-driver/mysql#dsn-data-source-name を参照してください。

  K3sの[既知の問題](https://github.com/k3s-io/k3s/issues/1093)により、`tls`パラメータを設定できないことに注意してください。TLS通信はサポートされていますが、例えばこのパラメータに「skip-verify」を設定して、K3sに証明書の検証をスキップさせることはできません。

  データベース名を指定し、それが存在しない場合、サーバーはそのデータベースを作成しようとします。

  エンドポイントとして `mysql://` だけを指定した場合、K3s は以下のことを試みます：

  - `root` ユーザーとパスワードなしで `/var/run/mysqld/mysqld.sock` にある MySQL ソケットに接続する。
  - `kubernetes`という名前でデータベースを作成する。

</TabItem>

<TabItem value="etcd">

  最も一般的な形として、etcdの`datastore-endpoint`パラメータは以下のような形式を持ちます：

  `https://etcd-host-1:2379,https://etcd-host-2:2379,https://etcd-host-3:2379`

  上記は、典型的な3ノードetcdクラスタを想定しています。このパラメータには、カンマで区切られたetcdのURLをもう1つ指定することができます。
</TabItem>
</Tabs>