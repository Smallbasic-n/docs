---
title: Helm
weight: 42
---

Helmは、Kubernetesのパッケージ管理ツールとして選択されています。Helmチャートは、KubernetesのYAMLマニフェストドキュメントのテンプレート構文を提供します。Helmを使えば、静的なファイルを使うだけでなく、設定可能なデプロイメントを作成することができます。独自のデプロイメントカタログの作成については、[https://helm.sh/docs/intro/quickstart/](https://helm.sh/docs/intro/quickstart/)のドキュメントをチェックしてみてください。

K3sは、Helmコマンドラインツールで使用するために特別な設定を必要としません。ただ、[cluster access](../cluster-access/cluster-access.md) のセクションにあるように、kubeconfigを適切にセットアップしていることを確認してください。K3sには、従来のKubernetesリソースマニフェストとHelmチャートの両方をより簡単にデプロイするための追加機能があり、[rancher/helm-release CRD.](#using-the-helm-crd) があります。

このセクションでは、以下のトピックについて説明します：

- [マニフェストとHelmチャートの自動展開](#automatically-deploying-manifests and-helm-charts)
- [Helm CRDを使う](#using-the-helm-crd)
- [HelmChartConfigを使ったパッケージコンポーネントのカスタマイズ](#customizing-packaged-components-with-helmchartconfig)
- [Helm v2からの移行](#migrating-from-helm-v2)

### マニフェストと Helm チャートの自動展開

`var/lib/rancher/k3s/server/manifests` で見つかった Kubernetes マニフェストは、`kubectl apply` と同様の方法で K3s に自動的にデプロイされます。この方法でデプロイされたマニフェストはAddOnカスタムリソースとして管理され、`kubectl get addon -A`を実行することで表示することができます。CoreDNS、Local-Storage、Traefikなど、パッケージ化されたコンポーネントのAddOnを見つけることができます。AddOnsはdeploy controllerによって自動的に作成され、manifestsディレクトリのファイル名に基づいて命名されます。

また、AddOnsとしてHelmチャートをデプロイすることも可能です。K3sには、HelmChart Custom Resource Definition (CRD)を使用してHelmチャートを管理する[Helm Controller](https://github.com/k3s-io/helm-controller/)があります。

### Helm CRDの使用方法

[HelmChartリソース定義](https://github.com/k3s-io/helm-controller#helm-controller)は、通常`helm`コマンドラインツールに渡すオプションのほとんどをキャプチャします。ここでは、デフォルトのチャートリポジトリからGrafanaをデプロイして、デフォルトのチャート値の一部を上書きする例を示します。HelmChartリソース自体は`kube-system`ネームスペースにありますが、チャートのリソースは`monitoring`ネームスペースにデプロイされることに注意してください。
```yaml
apiVersion: helm.cattle.io/v1
kind: HelmChart
metadata:
  name: grafana
  namespace: kube-system
spec:
  chart: stable/grafana
  targetNamespace: monitoring
  set:
    adminPassword: "NotVerySafePassword"
  valuesContent: |-
    image:
      tag: master
    env:
      GF_EXPLORE_ENABLED: true
    adminUser: admin
    sidecar:
      datasources:
        enabled: true
```

#### HelmChart フィールド定義

| フィールド｜デフォルト｜説明｜Helmの引数／フラグに相当するもの。
|-------|---------|-------------|-------------------------------|

| spec.chart｜｜Helm リポジトリ内のチャート名、またはチャートアーカイブ (.tgz) への完全な HTTPS URL｜CHART |.
| spec.targetNamespace｜default｜Helm Chart target namespace｜--namespace`｜｜.
| spec.version｜｜Helm Chart バージョン（リポジトリからインストールする場合）｜--version
| spec.repo｜｜Helm ChartのリポジトリURL｜`--repo`｜｜。

| spec.helmVersion | v3 | 使用するHelmのバージョン（`v2`または`v3`）｜｜spec.helmVersion
| spec.bootstrap｜ False｜このチャートがクラスタのブートストラップ（Cloud Controller Managerなど）に必要な場合は、Trueに設定します｜｜｜。
| spec.set｜｜Chart の単純なデフォルト値を上書きする。これらはvaluesContentで設定されたオプションより優先される。| `--set` / `--set-string` |...
| spec.jobImage｜｜ ヘルムチャートのインストール時に使用する画像を指定します。例：rancher/klipper-helm:v0.3.0 . | |
| spec.timeout｜300｜Helm 操作のタイムアウト（秒）｜--timeout`｜｜.
| spec.failurePolicy｜reinstall｜`abort` に設定すると、Helmの操作は中断され、オペレータによる手動操作が保留されます。| |
| spec.valuesContent｜｜ YAMLファイルの内容で複雑なデフォルトのチャート値を上書きする｜--values`｜｜。
| spec.chartContent｜｜Base64 エンコードされたチャートアーカイブ .tgz - spec.chart｜CHART｜ をオーバーライドします。

`var/lib/rancher/k3s/server/static/`に置かれたコンテンツは、クラスタ内からKubernetes APIServerを介して匿名でアクセスすることができます。このURLは `spec.chart` フィールドの特殊変数 `%{KUBERNETES_API}%` を使ってテンプレート化することができる。例えば、パッケージ化されたTraefikコンポーネントは、`https://%{KUBERNETES_API}%/static/charts/traefik-12.0.000.tgz`からそのチャートをロードします。

:::note
name`フィールドはHelmChartの命名規則に従わなければなりません。詳しくは[こちら](https://helm.sh/docs/chart_best_practices/conventions/#chart-names)を参照してください
:::

>**ファイル命名要件に関するお知らせ:** `HelmChart`と`HelmChartConfig`のマニフェストファイル名は、Kubernetesオブジェクト[命名制限](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)に準拠する必要があります。Helm Controllerはオブジェクトを作成するためにファイル名を使用するため、ファイル名も制限に沿う必要があります。関連するエラーは、k3s-serverのログで確認することができます。以下の例は、アンダースコアの使用によって発生したエラーです：
```
level=error msg="Failed to process config: failed to process 
/var/lib/rancher/k3s/server/manifests/k3s_ingress_daemonset.yaml: 
Addon.k3s.cattle.io \"k3s_ingress_daemonset\" is invalid: metadata.name: 
Invalid value: \"k3s_ingress_daemonset\": a lowercase RFC 1123 subdomain 
must consist of lower case alphanumeric characters, '-' or '.', and must 
start and end with an alphanumeric character (e.g. 'example.com', regex 
used for validation is '[a-z0-9]([-a-z0-9]*[a-z0-9])?(\\.[a-z0-9]
([-a-z0-9]*[a-z0-9])?)*')"
```

### HelmChartConfigによるパッケージコンポーネントのカスタマイズ

:::info Version Gate

[v1.19.1+k3s1](https://github.com/k3s-io/k3s/releases/tag/v1.19.1%2Bk3s1)の時点で利用可能です。

:::

HelmChartとしてデプロイされるパッケージコンポーネント（Traefikなど）の値を上書きできるように、K3sはHelmChartConfigリソースによるデプロイのカスタマイズをサポートしています。HelmChartConfig リソースは対応する HelmChart の名前と名前空間と一致しなければならず、追加の値ファイルとして `helm` コマンドに渡される `valuesContent` の提供をサポートする。

:::note
HelmChart `spec.set` の値は、HelmChart と HelmChartConfig `spec.valuesContent` の設定をオーバーライドします。
:::

例えば、パッケージ化されたTraefikのイングレス設定をカスタマイズするには、`/var/lib/rancher/k3s/server/manifests/traefik-config.yaml`というファイルを作り、以下の内容で入力することができます：
```yaml
apiVersion: helm.cattle.io/v1
kind: HelmChartConfig
metadata:
  name: traefik
  namespace: kube-system
spec:
  valuesContent: |-
    image:
      name: traefik
      tag: v2.8.5
    forwardedHeaders:
      enabled: true
      trustedIPs:
        - 10.0.0.0/8
    ssl:
      enabled: true
      permanentRedirect: false
```

### Helm v2 からの移行

:::info Version Gate
[v1.17.0+k3s.1](https://github.com/k3s-io/k3s/releases/tag/v1.17.0%2Bk3s.1) より、Helm v3がサポートされ、デフォルトで使用されます。
:::

K3sはHelm v2、Helm v3のどちらにも対応しています。Helm v3に移行する場合は、Helm社のブログ記事[こちら](https://helm.sh/blog/migrate-from-helm-v2-to-helm-v3/)で、プラグインを使ってうまく移行する方法を説明しています。より詳しい情報は、Helm 3の公式ドキュメント[こちら](https://helm.sh/docs/)を参照してください。[クラスタアクセス](../cluster-access/cluster-access.md)のセクションに従ってkubeconfigを適切に設定したことを確認してください。

:::note
Helm 3ではTillerと`helm init`コマンドは不要になりました。詳しくは公式ドキュメントを参照してください。
:::