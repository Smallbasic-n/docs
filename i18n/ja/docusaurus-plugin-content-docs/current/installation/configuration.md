---
title: "設定オプション"
weight: 20
---

このページでは、K3sを初めてセットアップする際に使用できるオプションを中心にご紹介します：

- [K3s インストールスクリプト](#configuration-with-install-script)
- [K3sバイナリ](#configuration-with-binary)です。
- [コンフィグレーションファイル](#configuration-file)

より高度なオプションについては、[このページ](../advanced/advanced.md)を参照してください。

## インストールスクリプトによる設定

[クイックスタートガイド](../quick-start/quick-start.md)にあるように、https://get.k3s.io にあるインストールスクリプトを使って、systemd と openrc ベースのシステムにサービスとして K3s をインストールすることができます。

INSTALL_K3S_EXEC`、`K3S_`環境変数、およびコマンドフラグを組み合わせて、インストールを設定することができます。

これを説明するために、以下のコマンドはすべて、flannelなしでサーバーを登録する場合とトークンで登録する場合の同じ動作になります：
```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--flannel-backend none --token 12345" sh -s -
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --flannel-backend none" K3S_TOKEN=12345 sh -s -
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server" sh -s - --flannel-backend none
curl -sfL https://get.k3s.io | K3S_TOKEN=12345 sh -s - server --flannel-backend none
curl -sfL https://get.k3s.io | sh -s - --flannel-backend none --token 12345
```

すべての環境変数の詳細については、「環境変数」（.../reference/env-variables.md）を参照してください。

バイナリで構成された ## 構成

インストールスクリプトは、K3sがサービスとして動作するように設定することを主な目的としています。 
スクリプトを使用しない場合は、[リリースページ](https://github.com/k3s-io/k3s/releases/latest)からバイナリをダウンロードし、パスに配置し、実行することでK3sを実行することができます。また、K3sをサービスとして有効にせずにインストールすることもできます：
```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_SKIP_ENABLE=true sh -
```

このようにK3Sを設定するには、`K3S_`環境変数を使用します：
```bash
K3S_KUBECONFIG_MODE="644" k3s server
```
もしくはコマンドフラグで:
```bash
k3s server --write-kubeconfig-mode 644
```

k3sエージェントもこのように設定することができます：
```bash
k3s agent --server https://k3s.example.com --token mypassword
```

K3sサーバーの設定については、「サーバー設定」(../reference/server-config.md)を参照してください。 
K3sエージェントの設定については、[エージェント設定](../reference/agent-config.md)を参照してください。 
また、`--help`フラグを使用すると、利用可能なすべてのオプションのリストを表示することができます。

:::info Matching Flags
サーバー/エージェントのインストール時に重要なフラグを一致させることが重要です。例えば、フラグを使用する場合
マスターノードで `--disable servicelb` または `--cluster-cidr=10.42.0.0/16` を設定し、他のサーバーノードでは設定しないようにすると、そのノードは参加に失敗します。この場合、以下のようなエラーが表示されます：
`failed to validate server configuration: critical configuration value mismatch.`
:::
## 設定ファイル

:::info Version Gate

[v1.19.1+k3s1](https://github.com/k3s-io/k3s/releases/tag/v1.19.1%2Bk3s1)から利用可能です。

:::

環境変数とCLI引数によるK3sの設定に加えて、K3sは設定ファイルを使用することもできます。

デフォルトでは、`/etc/rancher/k3s/config.yaml`にあるYAMLファイル内の値がインストール時に使用されます。

基本的な `server` の設定ファイルの例を以下に示します：
```yaml
write-kubeconfig-mode: "0644"
tls-san:
  - "foo.local"
node-label:
  - "foo=bar"
  - "something=amazing"
```

一般的に、CLI引数はそれぞれのYAMLキーに対応し、繰り返し使えるCLI引数はYAMLリストとして表現されます。

CLI引数のみを使用した同一の設定を以下に示し、これを実証します：
```bash
k3s server \
  --write-kubeconfig-mode "0644"    \
  --tls-san "foo.local"             \
  --node-label "foo=bar"            \
  --node-label "something=amazing"
```

また、設定ファイルとCLI引数の両方を使用することも可能です。このような場合、値は両方のソースからロードされますが、CLI引数が優先されます。node-label`のような繰り返し使用できる引数では、CLI引数はリストのすべての値を上書きします。

最後に、設定ファイルの場所はCLI引数 `--config FILE, -c FILE` または環境変数 `$K3S_CONFIG_FILE` によって変更することができます。

## すべてをまとめる

上記のオプションはすべて、1つの例にまとめることができます。

設定ファイル `config.yaml` は `/etc/rancher/k3s/config.yaml` に作成されます：

```yaml
token: "secret"
debug: true
```

そして、環境変数とフラグを組み合わせて、インストールスクリプトを実行します：
```bash
curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" INSTALL_K3S_EXEC="server" sh -s - --flannel-backend none
```

または、すでにK3s Binaryをインストールしている場合：
```bash
K3S_KUBECONFIG_MODE="644" k3s server --flannel-backend none
```
