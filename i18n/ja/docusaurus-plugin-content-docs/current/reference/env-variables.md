---
title: 環境変数
weight: 3
---

[クイック スタート ガイド](../quick-start/quick-start.md) に記載されているように、https://get.k3s.io で入手できるインストール スクリプトを使用して、systemd に K3s をサービスとしてインストールできます。 そしてopenrcベースのシステム。

このコマンドの最も単純な形式は次のとおりです。

```バッシュ
カール -sfL https://get.k3s.io | シ -
```

この方法を使用して K3s をインストールする場合、次の環境変数を使用してインストールを構成できます。

| | 環境変数 | 説明 |
|----------------------------------|------------------- --------------------------|
| | `INSTALL_K3S_SKIP_DOWNLOAD` | true に設定すると、K3s ハッシュまたはバイナリはダウンロードされません。 | |
| | `INSTALL_K3S_SYMLINK` | コマンドがパスにまだ存在しない場合、既定では、kubectl、crictl、および ctr バイナリのシンボリック リンクが作成されます。 'skip' に設定すると、シンボリック リンクは作成されず、'force' は上書きします。 | |
| | `INSTALL_K3S_SKIP_ENABLE` | true に設定すると、K3s サービスが有効化または開始されません。 | |
| | `INSTALL_K3S_SKIP_START` | true に設定すると、K3s サービスは開始されません。 | |
| | `INSTALL_K3S_VERSION` | Github からダウンロードする K3s のバージョン。 指定されていない場合は、安定したチャンネルからのダウンロードを試みます。 | |
| | `INSTALL_K3S_BIN_DIR` | K3s バイナリ、リンク、およびアンインストール スクリプトをインストールするディレクトリ、またはデフォルトとして `/usr/local/bin` を使用するディレクトリ。 | |
| | `INSTALL_K3S_BIN_DIR_READ_ONLY` | true に設定すると、ファイルは `INSTALL_K3S_BIN_DIR` に書き込まれず、強制的に `INSTALL_K3S_SKIP_DOWNLOAD=true` に設定されます。 | |
| | `INSTALL_K3S_SYSTEMD_DIR` | systemd サービスと環境ファイルをインストールするディレクトリ、またはデフォルトとして `/etc/systemd/system` を使用します。 | |
| | `INSTALL_K3S_EXEC` | サービスで K3 を起動するために使用するフラグ付きのコマンド。 コマンドが指定されておらず、「K3S_URL」が設定されている場合、デフォルトで「エージェント」になります。 「K3S_URL」が設定されていない場合、デフォルトで「サーバー」になります。 ヘルプについては、[この例](../installation/configuration.md#configuration-with-install-script) を参照してください。
| | `INSTALL_K3S_NAME` | 作成する systemd サービスの名前。k3s をサーバーとして実行している場合はデフォルトで「k3s」になり、k3s をエージェントとして実行している場合は「k3s-agent」になります。 指定した場合、名前の前に「k3s-」が付きます。 | |
| | `INSTALL_K3S_TYPE` | 作成する systemd サービスのタイプ。指定しない場合、デフォルトで K3s exec コマンドが使用されます。 | |
| | `INSTALL_K3S_SELINUX_WARN` | true に設定すると、k3s-selinux ポリシーが見つからない場合に続行されます。 | |
| | `INSTALL_K3S_SKIP_SELINUX_RPM` | true に設定すると、k3s RPM の自動インストールがスキップされます。 | |
| | `INSTALL_K3S_CHANNEL_URL` | K3s ダウンロード URL を取得するためのチャネル URL。 デフォルトは https://update.k3s.io/v1-release/channels です。 | |
| | `INSTALL_K3S_CHANNEL` | K3s ダウンロード URL の取得に使用するチャネル。 デフォルトは「安定」です。 オプションには、「stable」、「latest」、「testing」があります。 | |

この例は、前述の環境変数をオプションとして配置する場所を示しています (パイプの後):

```バッシュ
カール -sfL https://get.k3s.io | INSTALL_K3S_CHANNEL=最新の sh -
```

`K3S_` で始まる環境変数は、systemd および openrc サービスが使用するために保持されます。

exec コマンドを明示的に設定せずに「K3S_URL」を設定すると、コマンドはデフォルトで「agent」になります。

エージェントを実行するときは、`K3S_TOKEN` も設定する必要があります。