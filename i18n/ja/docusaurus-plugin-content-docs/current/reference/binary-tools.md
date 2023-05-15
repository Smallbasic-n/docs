---
title: K3s バイナリツール
weight: 1
---

K3s バイナリには、クラスターの管理に役立つ多くの追加ツールが含まれています。

コマンド | 説明
------|------------------
`k3s サーバー`| K3s 管理サーバーを実行します。これにより、API サーバー、コントローラー マネージャー、スケジューラーなどの Kubernetes コントロール プレーン コンポーネントも起動します。
`k3s エージェント`| K3s ノード エージェントを実行します。 これにより、K3s がワーカー ノードとして実行され、Kubernetes ノード サービス「kubelet」と「kube-proxy」が起動されます。
`k3s kubectl`| 組み込みの [kubectl](https://kubernetes.io/docs/reference/kubectl) CLI を実行します。 `KUBECONFIG` 環境変数が設定されていない場合、K3s サーバー ノードの起動時に `/etc/rancher/k3s/k3s.yaml` で作成された構成ファイルを自動的に使用しようとします。
`k3s crictl`| 埋め込まれた [crictl](https://github.com/kubernetes-sigs/cri-tools/blob/master/docs/crictl.md) を実行します。 これは、Kubernetes のコンテナー ランタイム インターフェイス (CRI) と対話するための CLI です。 デバッグに役立ちます。
`k3s ctr`| 埋め込まれた [ctr](https://github.com/projectatomic/containerd/blob/master/docs/cli.md) を実行します。 これは、K3s で使用されるコンテナー デーモンである containerd の CLI です。 デバッグに役立ちます。
`k3s etcd-スナップショット` | K3s クラスター データのオンデマンド バックアップを実行し、S3 にアップロードします。 詳細については、[バックアップと復元](../backup-restore/backup-restore.md#backup-and-restore-with-embedded-etcd-datastore) を参照してください。
`k3s secrets-encrypt` | シークレットをクラスターに保存するときに、シークレットを暗号化するように K3 を構成します。 詳細については、[シークレットの暗号化](../security/secrets-encryption.md) を参照してください。
`k3s 証明書` | 証明書管理。 詳細については、[証明書のローテーション](../advanced/advanced.md#certificate-rotation) を参照してください。
`k3s 完成` | k3s のシェル補完スクリプトを生成する
`k3s ヘルプ`| コマンドのリストまたは 1 つのコマンドのヘルプを表示します