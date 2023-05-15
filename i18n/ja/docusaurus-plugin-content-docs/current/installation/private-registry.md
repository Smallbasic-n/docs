---
title: "プライベートレジストリ設定"
weight: 55
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

Containerd は、プライベート レジストリに接続し、それらを使用してノード上のプライベート イメージをプルするように構成できます。

起動時に、K3s は「registries.yaml」ファイルが「/etc/rancher/k3s/」に存在するかどうかを確認し、ファイルで定義されているレジストリを使用するように containerd に指示します。 プライベート レジストリを使用する場合は、レジストリを使用する各ノードでルートとしてこのファイルを作成する必要があります。

サーバーノードはデフォルトでスケジュール可能であることに注意してください。 サーバーノードを汚染しておらず、それらでワークロードを実行する場合は、各サーバーにも「registries.yaml」ファイルを作成してください。

containerd の構成を使用して、TLS 接続および認証を有効にするレジストリを使用してプライベート レジストリに接続できます。 次のセクションでは、「registries.yaml」ファイルについて説明し、K3s でプライベート レジストリ構成を使用するさまざまな例を示します。

## レジストリ設定ファイル

このファイルは、次の 2 つの主要なセクションで構成されています。

- mirror
- config

### ミラー

Mirrors は、プライベート レジストリの名前とエンドポイントを定義するディレクティブです。次に例を示します。
```
mirrors:
  mycustomreg.com:
    endpoint:
      - "https://mycustomreg.com:5000"
```

各ミラーには、名前と一連のエンドポイントが必要です。 レジストリからイメージをプルする場合、containerd はこれらのエンドポイント URL を 1 つずつ試し、最初に機能する URL を使用します。

#### 書き換え

各ミラーには、一連の書き換えを含めることができます。 書き換えにより、正規表現に基づいて画像のタグを変更できます。 これは、ミラー レジストリの組織/プロジェクト構造が上流のものと異なる場合に役立ちます。

たとえば、次の構成は、イメージ `docker.io/rancher/coredns-coredns:1.6.3` を `registry.example.com:5000/mirrorproject/rancher-images/coredns-coredns:1.6.3` から透過的にプルします。 :
```
mirrors:
  docker.io:
    endpoint:
      - "https://registry.example.com:5000"
    rewrite:
      "^rancher/(.*)": "mirrorproject/rancher-images/$1"
```

イメージは引き続き元の名前で保存されるため、イメージがミラーリングからプルされた場合でも、「crictl image ls」はノードで利用可能な「docker.io/rancher/coredns-coredns:1.6.3」を示します。 別の名前のレジストリ。

### 構成

「configs」セクションでは、各ミラーの TLS と資格情報の構成を定義します。 ミラーごとに、「auth」および/または「tls」を定義できます。

`tls` 部分は次のもので構成されます。

| ディレクティブ | 説明 |
-----------------------|------------------------ -------------------------------------------------- ------------|
| `cert_file` | レジストリでの認証に使用されるクライアント証明書のパス |
| `key_file` | レジストリでの認証に使用されるクライアント キー パス |
| `ca_file` | レジストリのサーバー証明書ファイルを検証するために使用する CA 証明書パスを定義します |
| `insecure_skip_verify` | レジストリの TLS 検証をスキップするかどうかを定義するブール値 |

「auth」部分は、ユーザー名/パスワードまたは認証トークンのいずれかで構成されます。

| ディレクティブ | 説明 |
-----------|------------------------------------ ----------------------|
| `username` | プライベート レジストリの基本認証のユーザー名 |
| `password` | プライベート レジストリの基本認証のユーザー パスワード |
| `auth` | プライベート レジストリ基本認証の認証トークン |

以下は、さまざまなモードでプライベート レジストリを使用する基本的な例です。

### TLS あり

以下は、TLS を使用する場合に各ノードで「/etc/rancher/k3s/registries.yaml」を構成する方法を示す例です。
<Tabs>
<TabItem value="認証あり">

```yaml
mirrors:
  docker.io:
    endpoint:
      - "https://mycustomreg.com:5000"
configs:
  "mycustomreg:5000":
    auth:
      username: xxxxxx # レジストリのユーザ名
      password: xxxxxx # レジストリのパスワード
    tls:
      cert_file: # certファイル
      key_file:  # keyファイル
      ca_file:   # caファイル
```

</TabItem>
<TabItem value="認証なし">

```yaml
mirrors:
  docker.io:
    endpoint:
      - "https://mycustomreg.com:5000"
configs:
  "mycustomreg:5000":
    tls:
      cert_file: # certファイル
      key_file:  # keyファイル
      ca_file:   # caファイル
```
</TabItem>
</Tabs>

### TLS なし

以下は、TLS を_使用しない_場合に各ノードで `/etc/rancher/k3s/registries.yaml` を構成する方法を示す例です。
<Tabs>
<TabItem value="認証あり">

```yaml
mirrors:
  docker.io:
    endpoint:
      - "http://mycustomreg.com:5000"
configs:
  "mycustomreg:5000":
    auth:
      username: xxxxxx # レジストリのユーザ名
      password: xxxxxx # レジストリのパスワード
```

</TabItem>
<TabItem value="認証なし">

```yaml
mirrors:
  docker.io:
    endpoint:
      - "http://mycustomreg.com:5000"
```
</TabItem>
</Tabs>

> TLS 通信がない場合は、エンドポイントに「http://」を指定する必要があります。指定しないと、デフォルトで https になります。
 
レジストリの変更を有効にするには、各ノードで K3 を再起動する必要があります。

## プライベート レジストリにイメージを追加する

まず、使用しているリリースの GitHub から k3s-images.txt ファイルを取得します。
k3s-images.txt ファイルにリストされている K3s イメージを docker.io からプルします。

例: `docker pull docker.io/rancher/coredns-coredns:1.6.3`

次に、イメージをプライベート レジストリに再タグ付けします。

例: `docker tag coredns-coredns:1.6.3 mycustomreg:5000/coredns-coredns`

最後に、イメージをプライベート レジストリにプッシュします。

例: `docker push mycustomreg.com:5000/coredns-coredns`