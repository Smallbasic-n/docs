---
title: シークレットの暗号化
weight: 26
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# シークレット暗号化構成

K3s は、保存時のシークレット暗号化の有効化をサポートしています。 サーバーを最初に起動するときに、フラグ `--secrets-encryption` を渡すと、次のことが自動的に行われます。

- AES-CBC キーを生成する
- 生成されたキーを使用して暗号化構成ファイルを生成します
- 構成を暗号化プロバイダー構成として KubeAPI に渡します
:::tip 

Secrets-encryption は、再起動せずに既存のサーバーで有効にすることはできません。
`curl -sfL https://get.k3s.io | を使用します。 sh -s - server --secrets-encryption` スクリプトからインストールする場合、または [構成オプション](../installation/configuration.md#configuration-with-install-script) で説明されているその他の方法。
:::

暗号化構成ファイルの例:
```json
{
  "kind": "EncryptionConfiguration",
  "apiVersion": "apiserver.config.k8s.io/v1",
  "resources": [
    {
      "resources": [
        "secrets"
      ],
      "providers": [
        {
          "aescbc": {
            "keys": [
              {
                "name": "aescbckey",
                "secret": "xxxxxxxxxxxxxxxxxxx"
              }
            ]
          }
        },
        {
          "identity": {}
        }
      ]
    }
  ]
}
```

## シークレット暗号化ツール
:::info Version Gate
[v1.21.8+k3s1](https://github.com/k3s-io/k3s/releases/tag/v1.21.8%2Bk3s1)で利用可能
:::

K3s にはユーティリティ ツール「secrets-encrypt」が含まれており、次の項目を自動制御できます。

- 秘密の暗号化の無効化/有効化
- 新しい暗号鍵の追加
- 暗号鍵のローテーションと削除
- シークレットの再暗号化

:::cauction
暗号化キーをローテーションするための適切な手順に従わないと、クラスターが永久に破損したままになる可能性があります。 慎重に進んでください。
:::
### 暗号鍵のローテーション

<Tabs>
<TabItem value="Single-Server" default>

単一サーバー クラスタでシークレット暗号化キーをローテーションするには:

- フラグ `--secrets-encryption` を指定して K3s サーバーを起動します
:::note 
暗号化なしで K3 を起動し、後で有効にすることは、現在サポートされていません。
:::

1. 準備

    ```bash
    k3s secrets-encrypt prepare
    ```

2. K3s サーバーを強制終了し、同じ引数で再起動します。 K3s をサービスとして実行している場合:
    ```bash
    # If using systemd
    systemctl restart k3s
    # If using openrc
    rc-service k3s restart
    ```

3. 交換

    ```bash
    k3s secrets-encrypt rotate
    ```

4. 同じ引数で K3s サーバーを強制終了して再起動します
5. 再暗号化
    :::info
    K3s will reencrypt ~5 secrets per second.  
    Clusters with large # of secrets can take several minutes to reencrypt.
    ::: 
    ```bash
    k3s secrets-encrypt reencrypt
    ``` 


</TabItem>
<TabItem value="高可用" default>

手順は、組み込み DB クラスターと外部 DB クラスターの両方で同じです。

HA セットアップでシークレット暗号化キーをローテーションするには:
:::note Notes

- 暗号化なしで K3 を起動し、後で有効にすることは、現在サポートされていません。
- 必須ではありませんが、「secrets-encrypt」コマンドを実行するサーバー ノードを 1 つ選択することをお勧めします。
:::

1. `--secrets-encryption` フラグを指定して 3 つの K3s サーバーをすべて起動します。 簡潔にするために、サーバーは S1、S2、S3 と呼ばれます。

2. S1 で準備する
    ```bash
    k3s secrets-encrypt prepare
    ```

3. S1 を強制終了し、同じ引数で再起動します。 K3s をサービスとして実行している場合:
    ```bash
    # If using systemd
    systemctl restart k3s
    # If using openrc
    rc-service k3s restart
    ```

4. S1 が起動したら、S2 と S3 を強制終了して再起動します。

5. S1上で交換
    ```bash
    k3s secrets-encrypt rotate
    ```

6. S1 を強制終了し、同じ引数で再起動します。
7. S1 が起動したら、S2 と S3 を強制終了して再起動します。

8. S1 で再暗号化する
    :::info
K3 は 1 秒あたり最大 5 つのシークレットを再暗号化します。
     シークレットの数が多いクラスターは、再暗号化に数分かかる場合があります。
    :::
    ```bash
    k3s secrets-encrypt reencrypt
    ```

9. S1 を強制終了し、同じ引数で再起動します。
10. S1 が起動したら、S2 と S3 を強制終了して再起動します。

</TabItem>
</Tabs>

### シークレット暗号化の無効化/有効化
<Tabs>
<TabItem value="Single-Server" default>

`--secrets-encryption` フラグを指定してサーバーを起動した後、シークレットの暗号化を無効にすることができます。

単一ノード クラスタでシークレットの暗号化を無効にするには:

1. 無効にする
    ```bash
    k3s secrets-encrypt disable
    ```

2. K3s サーバーを強制終了し、同じ引数で再起動します。 K3s をサービスとして実行している場合:
    ```bash
    # If using systemd
    systemctl restart k3s
    # If using openrc
    rc-service k3s restart
    ```

3. フラグを付けて再暗号化する
    ```bash
    k3s secrets-encrypt reencrypt --force --skip
    ```

単一ノード クラスタでシークレットの暗号化を再度有効にするには:

1. 有効にする
    ```bash
    k3s secrets-encrypt enable
    ```

2. K3s サーバーを強制終了し、同じ引数で再起動します。

3. フラグを付けて再暗号化する
    ```bash
    k3s secrets-encrypt reencrypt --force --skip
    ```

</TabItem>
<TabItem value="High-Availability" default>

「--secrets-encryption」フラグを使用して HA クラスターを起動した後、シークレットの暗号化を無効にすることができます。

：：：ノート
必須ではありませんが、「secrets-encrypt」コマンドを実行するサーバー ノードを 1 つ選択することをお勧めします。
:::

簡潔にするために、このガイドで使用する 3 つのサーバーを S1、S2、S3 と呼びます。

HA クラスターでシークレットの暗号化を無効にするには:

1. S1で無効にする
    ```bash
    k3s secrets-encrypt disable
    ```

2. S1 を強制終了し、同じ引数で再起動します。 K3s をサービスとして実行している場合:
    ```bash
    # If using systemd
    systemctl restart k3s
    # If using openrc
    rc-service k3s restart
    ```

3. S1 が起動したら、S2 と S3 を強制終了して再起動します。


4. S1 のフラグを使用して再暗号化する
    ```bash
    k3s secrets-encrypt reencrypt --force --skip
    ```

HA クラスターでシークレットの暗号化を再度有効にするには:

1. S1 で有効にする
    ```bash
    k3s secrets-encrypt enable
    ```

2. S1 を強制終了し、同じ引数で再起動します。
3. S1 が起動したら、S2 と S3 を強制終了して再起動します。

4. S1 のフラグを使用して再暗号化する
    ```bash
    k3s secrets-encrypt reencrypt --force --skip
    ```

</TabItem>
</Tabs>

### シークレット暗号化ステータス
secrets-encrypt ツールには、ノードでのシークレット暗号化の現在のステータスに関する情報を表示する「status」コマンドが含まれています。

単一サーバー ノードでのコマンドの例:
```bash
$ k3s secrets-encrypt status
Encryption Status: Enabled
Current Rotation Stage: start
Server Encryption Hashes: All hashes match

Active  Key Type  Name
------  --------  ----
 *      AES-CBC   aescbckey

```

キーをローテーションした後、サーバーを再起動する前の、HA クラスターでの別の例:
```bash
$ k3s secrets-encrypt status
Encryption Status: Enabled
Current Rotation Stage: rotate
Server Encryption Hashes: hash does not match between node-1 and node-2

Active  Key Type  Name
------  --------  ----
 *      AES-CBC   aescbckey-2021-12-10T22:54:38Z
        AES-CBC   aescbckey

```

各セクションの詳細は次のとおりです。

- __Encryption Status__: ノードでシークレットの暗号化が無効になっているか有効になっているかが表示されます
- __Current Rotation Stage__: ノードの現在のローテーション ステージを示します。
   ステージは次のとおりです: `start`、`prepare`、`rotate`、`reencrypt_request`、`reencrypt_active`、`reencrypt_finished`
- __サーバー暗号化ハッシュ__: HA クラスターに役立ちます。これは、すべてのサーバーがローカル ファイルと同じ段階にあるかどうかを示します。 これは、次の段階に進む前にサーバーの再起動が必要かどうかを識別するために使用できます。 上記の HA の例では、ノード 1 とノード 2 は異なるハッシュを持ち、現在同じ暗号化構成を持っていないことを示しています。 サーバーを再起動すると、構成が同期されます。
- __Key Table__: ノードで見つかったシークレット暗号化キーに関する情報を要約します。
   * __Active__: "*" は、シークレットの暗号化に現在使用されているキーがあれば、それを示します。 アクティブなキーは、新しいシークレットを暗号化するために Kubernetes によって使用されます。
   * __Key Type__: このツールを使用するすべてのキーは「AES-CBC」タイプです。 詳細については [こちら] (https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/#providers) を参照してください。
   * __Name__: 暗号化キーの名前。