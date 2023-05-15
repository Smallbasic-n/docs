---
title: "エアギャップインストール"
weight: 60
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

K3sは、2種類の方法でエアギャップ環境にインストールすることができます。エアギャップ環境とは、インターネットに直接接続されていない環境のことです。プライベートレジストリを導入してdocker.ioをミラーリングするか、小規模クラスタ用などのイメージを手動でデプロイすることができます。

## プライベートレジストリ方式

このドキュメントは、あなたがすでにエアギャップ環境でノードを作成し、bastionホスト上でDockerプライベートレジストリを設定していることを前提としています。

まだDockerのプライベートレジストリを設定していない場合は、公式ドキュメント[こちら](https://docs.docker.com/registry/deploying/#run-an-externally-accessible-registry)を参照してください。

### レジストリのYAMLを作成する

[プライベートレジストリの設定](private-registry.md)のガイドに従って、registry.yamlファイルを作成し設定します。

これが完了したら、以下の[K3sのインストール](#install-k3s)のセクションに進んでください。


## 手動でイメージを配置する方法

エアギャップ環境でノードを作成したことを想定しています。
この方法では、必要なイメージを各ノードに手動で配置する必要があり、プライベートレジストリを実行することが現実的でないエッジ展開に適しています。

### イメージディレクトリとK3sバイナリの準備
実行するK3sのバージョンの[releases](https://github.com/k3s-io/k3s/releases)ページから、あなたのアーキテクチャに対応したimages tarファイルを入手する。

このtarファイルを、例えば`images`ディレクトリに配置します：
```bash
sudo mkdir -p /var/lib/rancher/k3s/agent/images/
sudo cp ./k3s-airgap-images-$ARCH.tar /var/lib/rancher/k3s/agent/images/
```

k3sのバイナリを`/usr/local/bin/k3s`に配置し、実行可能であることを確認します。

次節の手順でK3sをインストールする。

## K3sのインストール

### 前提条件

- K3sをインストールする前に、上記の[Private Registry Method](#private-registry-method) または [Manually Deploy Images Method](#manually-deploy-images-method) を実行して、K3sのインストールに必要なイメージを事前に入手してください。
- airgapイメージを取得するために使用したものと同じバージョンで、[releases](https://github.com/k3s-io/k3s/releases)ページからK3sバイナリをダウンロードします。バイナリを各 air-gapped ノードの `/usr/local/bin` に置き、それが実行可能であることを確認します。
- K3sのインストールスクリプトを [get.k3s.io](https://get.k3s.io) でダウンロードします。インストールスクリプトを各Air-Gapノード上の任意の場所に置き、名前を `install.sh` とする。

環境変数 `INSTALL_K3S_SKIP_DOWNLOAD` を指定して K3s スクリプトを実行すると、K3s はローカルバージョンのスクリプトとバイナリを使用します。


### エアギャップ環境でのK3sのインストール

K3sは、以下のように1台または複数台のサーバーにインストールすることができます。

<Tabs>
<TabItem value="シングルサーバ設定" default>

1台のサーバーにK3sをインストールする場合、サーバーノードで以下の作業を行うだけです：
```bash
INSTALL_K3S_SKIP_DOWNLOAD=true ./install.sh
```

次に、オプションでエージェントを追加するために、各エージェントノードで以下の操作を行います。`myserver`をサーバーのIPまたは有効なDNSに置き換え、`mynodetoken`をサーバーのノードトークンに置き換えることに注意してください（通常は `/var/lib/rancher/k3s/server/node-token` ）。
```bash
INSTALL_K3S_SKIP_DOWNLOAD=true K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken ./install.sh
```

</TabItem>
<TabItem value="高可用設定" default>

[外部DBを利用した高可用構成](ha.md) または [組み込みDBを利用した高可用構成](ha-embedded.md) ガイドを参照してください。`INSTALL_K3S_SKIP_DOWNLOAD=true`を指定し、インストールスクリプトをcurl経由ではなくローカルで実行するように、インストールコマンドを調整します。また、`INSTALL_K3S_EXEC='args'`を利用して、k3sに任意の引数を与えることになります。

例えば、「外部DBを利用した高可用構成」ガイドのステップ2では、以下のように言及されています：
```bash
curl -sfL https://get.k3s.io | sh -s - server \
  --datastore-endpoint='mysql://username:password@tcp(hostname:3306)/database-name'
```

その代わりに、このような例を以下のように修正することになります：
```bash
INSTALL_K3S_SKIP_DOWNLOAD=true INSTALL_K3S_EXEC='server' K3S_DATASTORE_ENDPOINT='mysql://username:password@tcp(hostname:3306)/database-name' ./install.sh
```

</TabItem>
</Tabs>

>**注：*** K3sはさらに、kubeletsのための `--resolv-conf` フラグを提供し、エアギャップ・ネットワークにおけるDNSの構成に役立つかもしれません。

## アップグレード

### スクリプトのインストール方法

エアギャップ環境のアップグレードは、以下の手順で行うことができます：

1. アップグレードするK3sのバージョンの[releases](https://github.com/k3s-io/k3s/releases)ページから新しいair-gapイメージ(tarファイル)をダウンロードします。このtarファイルを各K3sの `/var/lib/rancher/k3s/agent/images/` ディレクトリに置く。
ノードを使用します。古い tar ファイルを削除する。
2. 各ノードの `/usr/local/bin` にある古い K3s バイナリをコピーして置き換える。https://get.k3s.io にあるインストールスクリプトをコピーする（前回のリリースから変更されている可能性があるため）。過去に行ったのと同じように、スクリプトを再度実行する。
を同じ環境変数で設定してください。
3. K3sサービスを再起動します（インストーラーによって自動的に再起動されない場合）。


### 自動アップグレードの方法

K3sは[自動アップグレード](../upgrades/automated.md)をサポートしています。エアギャップ環境でこれを有効にするには、必要なイメージがプライベートレジストリで利用可能であることを確認する必要があります。

アップグレードしようとするK3sのバージョンに対応するrancher/k3s-upgradeのバージョンが必要です。なお、Dockerイメージは`+`をサポートしていないため、imageタグはK3sリリースの`+`を`-`で置き換えています。

また、デプロイするsystem-upgrade-controllerのマニフェストYAMLに指定されているsystem-upgrade-controllerとkubectlのバージョンも必要です。system-upgrade-controllerの最新リリースを確認し[こちら](https://github.com/rancher/system-upgrade-controller/releases/latest)、system-upgrade-controller.yamlをダウンロードして、プライベートレジストリにプッシュする必要があるバージョンを決定します。例えば、system-upgrade-controller のリリース v0.4.0 では、これらのイメージはマニフェスト YAML で指定されています：
```
rancher/system-upgrade-controller:v0.4.0
rancher/kubectl:v0.17.0
```

必要なrancher/k3s-upgrade、rancher/system-upgrade-controller、rancher/kubectlイメージをプライベートレジストリに追加したら、 [automated upgrades](../upgrades/automated.md) ガイドに従ってください。