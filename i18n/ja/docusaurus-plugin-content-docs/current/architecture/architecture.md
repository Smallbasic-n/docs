---
title: アーキテクチャ
weight: 1
---

import ThemedImage from '@theme/ThemedImage';
import useBaseUrl from '@docusaurus/useBaseUrl';

このページでは、高可用性K3sサーバクラスタのアーキテクチャと、シングルノードサーバクラスタとの違いについて説明します。

また、エージェントノードがK3sサーバに登録される方法についても説明します。

* サーバーノードは `k3s server` コマンドを実行するホストと定義され、コントロールプレーンとデータストアのコンポーネントはK3sによって管理されます。
* エージェントノードは `k3s agent` コマンドを実行するホストとして定義され、データストアやコントロールプレーンのコンポーネントはありません。
* サーバーとエージェントの両方が、kubelet、コンテナランタイム、CNIを実行します。エージェントレスサーバーの実行に関する詳細については、[Advanced Options](../advanced/advanced.md#running-agentless-servers-experimental) ドキュメントを参照してください。

このページでは、以下のトピックを扱います：

- [データベースを組み込んだ単一サーバーの設定](#single-server-setup-with-an-embedded-db)
- [外部データベースによる高可用性K3sサーバー](#high-availability-k3s-server-with-an-external-db)
- [エージェントノード用登録アドレスの固定化](#fixed-registration-address for-agent-nodes)
- [エージェントノード登録の仕組み](#how-agent-node-registration-works)
- [自動的にデプロイされるマニフェスト](#automatically-deployed-manifests)

### 組み込みDBによるシングルサーバーのセットアップ

次の図は、SQLiteデータベースを組み込んだシングルノードのK3sサーバーを持つクラスタの例を示しています。

この構成では、各エージェントノードは同じサーバーノードに登録されています。K3sユーザーは、サーバーノード上のK3s APIを呼び出すことで、Kubernetesリソースを操作することができます。
<ThemedImage
  alt="シングルサーバによるK3sアーキテクチャ"
  sources={{
    light: useBaseUrl('/img/k3s-architecture-single-server.svg'),
    dark: useBaseUrl('/img/k3s-architecture-single-server-dark.svg'),
  }}
/>


### 外部DBを利用した高可用性K3sサーバ

シングルサーバークラスターは様々なユースケースに対応できますが、Kubernetesコントロールプレーンのアップタイムが重要な環境では、HA構成でK3sを実行することができます。HA K3sクラスタは、以下のもので構成されています：

* Kubernetes APIを提供し、他のコントロールプレーンサービスを実行する2つ以上の**サーバーノード**で構成されます。
* 外部データストア**（シングルサーバーで使用されるSQLiteデータストアの組み込みとは異なります。）

<ThemedImage
  alt="高可用性サーバーを搭載したK3sアーキテクチャ"
  sources={{
    light: useBaseUrl('/img/k3s-architecture-ha-server.svg'),
    dark: useBaseUrl('/img/k3s-architecture-ha-server-dark.svg'),
  }}
/>

### エージェントノードの登録アドレスが固定される

高可用性サーバー構成では、下図に示すように、各ノードも固定登録アドレスを使用してKubernetes APIに登録する必要があります。

登録後、エージェントノードはサーバーノードの1つに直接接続を確立します。
<ThemedImage
  alt="エージェント登録HA"
  sources={{
    light: useBaseUrl('/img/k3s-production-setup.svg'),
    dark: useBaseUrl('/img/k3s-production-setup-dark.svg'),
  }}
/>

### エージェントノード登録のしくみ

エージェントノードは `k3s agent` プロセスによって開始されるウェブソケット接続で登録され、接続はエージェントプロセスの一部として実行されるクライアント側のロードバランサによって維持されます。

エージェントは、ノードクラスタの秘密情報と、`/etc/rancher/node/password`に格納されているノード用のランダムに生成されたパスワードを使ってサーバーに登録します。サーバーは個々のノードのパスワードをKubernetesシークレットとして保存し、それ以降の試行は同じパスワードを使用する必要があります。ノードパスワードの秘密は `kube-system` 名前空間に保存され、テンプレート `<host>.node-password.k3s` を使用した名前になっています。

注意：K3s v1.20.2以前のサーバーは、ディスク上の `/var/lib/rancher/k3s/server/cred/node-passwd` にパスワードを保存します。

エージェントの `/etc/rancher/node` ディレクトリが削除された場合、パスワードファイルはエージェント用に再作成されるか、サーバーからエントリが削除される必要があります。

ノードIDは、`--with-node-id`フラグを使用してK3sサーバーまたはエージェントを起動することにより、ホスト名に追加することができます。

### 自動的にデプロイされるマニフェスト

ディレクトリパス `/var/lib/rancher/k3s/server/manifests` にある [manifests](https://github.com/k3s-io/k3s/tree/master/manifests) は、ビルド時に K3s バイナリにバンドルされています。 これらは実行時に[k3s-io/helm-controller.](https://github.com/k3s-io/helm-controller#helm-controller)によってインストールされます。