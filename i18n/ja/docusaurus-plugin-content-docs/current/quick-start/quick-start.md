---
title: "クイックスタートガイド"
weight: 10
---

このガイドは、デフォルト オプションを使用してクラスターをすばやく起動するのに役立ちます。 [インストール セクション](../installation/installation.md) では、K3 のセットアップ方法について詳しく説明しています。

K3s コンポーネントがどのように連携するかについては、[アーキテクチャ セクション](../architecture/architecture.md) を参照してください。

:::note
Kubernetes は初めてですか? 公式の Kubernetes ドキュメントには、[ここ](https://kubernetes.io/docs/tutorials/kubernetes-basics/) の基本を概説する優れたチュートリアルが既にいくつかあります。
:::

インストールスクリプト
--------------
K3s は、systemd または openrc ベースのシステムにサービスとしてインストールするための便利な方法であるインストール スクリプトを提供します。 このスクリプトは https://get.k3s.io で入手できます。 この方法を使用して K3s をインストールするには、次を実行します。
```bash
curl -sfL https://get.k3s.io | sh -
```

このインストールを実行した後:

* K3s サービスは、ノードの再起動後、またはプロセスがクラッシュまたは強制終了された場合に自動的に再起動するように構成されます
* `kubectl`、`crictl`、`ctr`、`k3s-killall.sh`、`k3s-uninstall.sh` などの追加ユーティリティがインストールされます。
* [kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) ファイルが `/etc/rancher/k3s/k3s.yaml` に書き込まれ、kubectl がインストールされます by K3s は自動的にそれを使用します

単一ノード サーバーのインストールは、ワークロード ポッドをホストするために必要なすべてのデータストア、コントロール プレーン、kubelet、およびコンテナー ランタイム コンポーネントを含む、完全に機能する Kubernetes クラスターです。 サーバーやエージェントのノードを追加する必要はありませんが、クラスターに容量や冗長性を追加するために必要になる場合があります。

追加のエージェント ノードをインストールしてクラスターに追加するには、「K3S_URL」および「K3S_TOKEN」環境変数を使用してインストール スクリプトを実行します。 エージェントに参加する方法を示す例を次に示します。
```bash
curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken sh -
```
「K3S_URL」パラメーターを設定すると、インストーラーは K3s をサーバーではなくエージェントとして構成します。 K3s エージェントは、指定された URL でリッスンしている K3s サーバーに登録します。 `K3S_TOKEN` に使用する値は、サーバー ノードの `/var/lib/rancher/k3s/server/node-token` に保存されます。

:::note
各マシンには一意のホスト名が必要です。 マシンに一意のホスト名がない場合は、「K3S_NODE_NAME」環境変数を渡し、各ノードに有効で一意のホスト名の値を指定してください。
:::