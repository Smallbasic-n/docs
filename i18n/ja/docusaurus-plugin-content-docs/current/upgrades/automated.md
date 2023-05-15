---
title: "自動的なアップグレード"
weight: 20
---

### Overview

Rancher の system-upgrade-controller を使用して、K3s クラスターのアップグレードを管理できます。 これは、クラスターのアップグレードに対する Kubernetes ネイティブのアプローチです。 [カスタム リソース定義 (CRD)](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#custom-resources)、「計画」、および [ コントローラー](https://kubernetes.io/docs/concepts/architecture/controller/)。

計画では、アップグレードのポリシーと要件を定義します。 また、[ラベル セレクター](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) を介してアップグレードする必要があるノードも定義します。 K3s クラスターのアップグレードに適したデフォルトのプランについては、以下を参照してください。 より高度なプラン構成オプションについては、[CRD](https://github.com/rancher/system-upgrade-controller/blob/master/pkg/apis/upgrade.cattle.io/v1/types.go) を確認してください。 .

コントローラーは、プランを監視し、アップグレード [ジョブ](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/) を実行するノードを選択することで、アップグレードをスケジュールします。 ジョブが正常に完了すると、コントローラーはジョブが実行されたノードにラベルを付けます。

:::note 
起動されるアップグレード ジョブには、高度な特権が必要です。 次のように構成されています。
- ホスト `IPC`、`NET`、および `PID` 名前空間
- `CAP_SYS_BOOT` 機能
- ホスト ルートは、読み書きパーミッションで「/host」にマウントされます
:::


この方法でアップグレードを自動化するには、次の手順を実行する必要があります。

1. system-upgrade-controller をクラスターにインストールします
1. プランを構成する

:::note
Rancher が K3s クラスターを管理している場合、ユーザーは Rancher を使用して K3s クラスターをアップグレードできます。
- Rancher を使用してアップグレードする場合、以下の手順が自動的に実行されます。
- Rancher を使用してアップグレードしない場合は、以下の手順に従う必要があります。
:::

system-upgrade-controller の設計とアーキテクチャ、または K3s との統合の詳細については、次の Git リポジトリを参照してください。

- [システムアップグレードコントローラー](https://github.com/rancher/system-upgrade-controller)
- [k3s-upgrade](https://github.com/k3s-io/k3s-upgrade)


### system-upgrade-controller をインストールします
  system-upgrade-controller は、デプロイメントとしてクラスターにインストールできます。 デプロイには、サービス アカウント、clusterRoleBinding、および configmap が必要です。 これらのコンポーネントをインストールするには、次のコマンドを実行します。:
- ホスト `IPC`、`NET`、および `PID` 名前空間
- `CAP_SYS_BOOT` 機能
- ホスト ルートは、読み書きパーミッションで「/host」にマウントされます
```bash
kubectl apply -f https://github.com/rancher/system-upgrade-controller/releases/latest/download/system-upgrade-controller.yaml
```
コントローラーは前述の configmap を介して構成およびカスタマイズできますが、変更を適用するにはコントローラーを再デプロイする必要があります。


### プランを構成する
サーバー (マスター) ノードをアップグレードするためのプランと、エージェント (ワーカー) ノードをアップグレードするためのプランの少なくとも 2 つのプランを作成することをお勧めします。 ノード間のアップグレードのロールアウトを制御するために、必要に応じて追加の計画を作成できます。 プランが作成されると、コントローラーがそれらを選択し、クラスターのアップグレードを開始します。

次の 2 つの計画例では、クラスターを K3s v1.24.6+k3s1 にアップグレードします。
```yaml
# Server plan
apiVersion: upgrade.cattle.io/v1
kind: Plan
metadata:
  name: server-plan
  namespace: system-upgrade
spec:
  concurrency: 1
  cordon: true
  nodeSelector:
    matchExpressions:
    - key: node-role.kubernetes.io/master
      operator: In
      values:
      - "true"
  serviceAccountName: system-upgrade
  upgrade:
    image: rancher/k3s-upgrade
  version: v1.24.6+k3s1
---
# Agent plan
apiVersion: upgrade.cattle.io/v1
kind: Plan
metadata:
  name: agent-plan
  namespace: system-upgrade
spec:
  concurrency: 1
  cordon: true
  nodeSelector:
    matchExpressions:
    - key: node-role.kubernetes.io/master
      operator: DoesNotExist
  prepare:
    args:
    - prepare
    - server-plan
    image: rancher/k3s-upgrade
  serviceAccountName: system-upgrade
  upgrade:
    image: rancher/k3s-upgrade
  version: v1.24.6+k3s1
```

これらの計画に関して、重要な点がいくつかあります。

1) プランは、コントローラーがデプロイされたのと同じ名前空間で作成する必要があります。

2) 「concurrency」フィールドは、同時にアップグレードできるノードの数を示します。

3) サーバー プランは、「node-role.kubernetes.io/master」ラベルを持つノードを選択するラベル セレクターを指定することで、サーバー ノードをターゲットにします。 エージェント プランは、そのラベルのないノードを選択するラベル セレクターを指定することによって、エージェント ノードをターゲットにします。

4) エージェント プランの「準備」ステップにより、そのプランのアップグレード ジョブは、サーバー プランが完了するのを待ってから実行されます。

5) どちらのプランも「バージョン」フィールドが v1.24.6+k3s1 に設定されています。 または、`version` フィールドを省略して、`channel` フィールドを K3s のリリースに解決される URL に設定することもできます。 これにより、コントローラはその URL を監視し、新しいリリースに解決されるたびにクラスタをアップグレードします。 これは、[リリース チャンネル](manual.md#release-channels) でうまく機能します。 したがって、次のチャネルを使用して計画を構成し、クラスターが常に最新の安定した K3s リリースに自動的にアップグレードされるようにすることができます。
```yaml
apiVersion: upgrade.cattle.io/v1
kind: Plan
...
spec:
  ...
  channel: https://update.k3s.io/v1-release/channels/stable

```

前述のように、プランが作成されたことをコントローラが検出するとすぐに、アップグレードが開始されます。 計画を更新すると、コントローラは計画を再評価し、別のアップグレードが必要かどうかを判断します。

kubectl を介してプランとジョブを表示することで、アップグレードの進行状況を監視できます。
```bash
kubectl -n system-upgrade get plans -o yaml
kubectl -n system-upgrade get jobs -o yaml
```

