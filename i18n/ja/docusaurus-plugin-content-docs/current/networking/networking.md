---
title: "ネットワーキング"
weight: 35
---

このページでは、CoreDNS、Traefik Ingress コントローラー、および Klipper サービス ロード バランサーが K3 内でどのように機能するかについて説明します。

Flannel 構成オプションとバックエンドの選択、または独自の CNI のセットアップ方法の詳細については、[インストール ネットワーク オプション](../installation/network-options.md) ページを参照してください。

K3s 用にどのポートを開く必要があるかについては、[ネットワーク要件](../installation/requirements.md#networking) を参照してください。

## コアDNS

CoreDNS は、サーバーの起動時に自動的に展開されます。 これを無効にするには、クラスタ内のすべてのサーバーを `--disable=coredns` オプションで構成します。

CoreDNS をインストールしない場合は、クラスター DNS プロバイダーを自分でインストールする必要があります。

## Traefik イングレス コントローラー

[Traefik](https://traefik.io/) は、マイクロサービスを簡単にデプロイできるように作成された、最新の HTTP リバース プロキシおよびロード バランサーです。 アプリケーションの設計、展開、および実行中のネットワークの複雑さを簡素化します。

Traefik は、サーバーの起動時にデフォルトでデプロイされます。 詳細については、[Auto Deploying Manifests](../advanced/advanced.md#auto-deploying-manifests) を参照してください。 デフォルトの設定ファイルは「/var/lib/rancher/k3s/server/manifests/traefik.yaml」にあります。

Traefik イングレス コントローラーは、ホストのポート 80 および 443 を使用します (つまり、これらは HostPort または NodePort には使用できません)。

`traefik.yaml` ファイルは、手動で編集しないでください。k3s が再起動すると再び上書きされるためです。 代わりに、「/var/lib/rancher/k3s/server/manifests」に追加の「HelmChartConfig」マニフェストを作成することで、Traefik をカスタマイズできます。 詳細と例については、[HelmChartConfig によるパッケージ化されたコンポーネントのカスタマイズ](../helm/helm.md#customizing-packaged-components-with-helmchartconfig) を参照してください。 可能な構成値の詳細については、公式の [Traefik Helm 構成パラメーター](https://github.com/traefik/traefik-helm-chart/tree/master/traefik) を参照してください。

これを無効にするには、各サーバーを `--disable traefik` オプションで起動します。

Traefik が無効になっていない場合、K3s バージョン 1.20 以前では Traefik v1 がインストールされますが、K3s バージョン 1.21 以降では、v1 がまだ存在しない場合は Traefik v2 がインストールされます。

古い Traefik v1 インスタンスから移行するには、[Traefik ドキュメント](https://doc.traefik.io/traefik/migration/v1-to-v2/) と [移行ツール](https://github. com/traefik/traefik-migration-tool)。

## サービス ロード バランサ

K3s クラスターでは、任意のサービス ロード バランサー (LB) を使用できます。 デフォルトでは、K3s は、利用可能なホスト ポートを使用する [ServiceLB](https://github.com/k3s-io/klipper-lb) (以前の Klipper Load Balancer) と呼ばれるロード バランサーを提供します。

アップストリーム Kubernetes では、タイプ LoadBalancer のサービスを作成できますが、デフォルトのロード バランサーの実装が含まれていないため、これらのサービスはインストールされるまで「保留中」のままになります。 多くのホステッド サービスでは、外部ロード バランサーの実装を提供するために、Amazon EC2 や Microsoft Azure などのクラウド プロバイダーが必要です。 対照的に、K3s ServiceLB を使用すると、クラウド プロバイダーや追加構成なしで LoadBalancer サービスを使用できます。
### サービス LB の仕組み

ServiceLB コントローラーは、「spec.type」フィールドを「LoadBalancer」に設定して、Kubernetes [サービス](https://kubernetes.io/docs/concepts/services-networking/service/) を監視します。

LoadBalancer サービスごとに、[DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) が「kube-system」名前空間に作成されます。 この DaemonSet は、各ノードに「svc-」プレフィックスを持つ Pod を作成します。 これらの Pod は iptables を使用して、Pod の NodePort から Service の ClusterIP アドレスとポートにトラフィックを転送します。

外部 IP が構成されているノードで ServiceLB Pod が実行されている場合、ノードの外部 IP が Service の「status.loadBalancer.ingress」アドレス リストに取り込まれます。 それ以外の場合は、ノードの内部 IP が使用されます。

複数の LoadBalancer Service が作成される場合、Service ごとに個別の DaemonSet が作成されます。

異なるポートを使用する限り、同じノードで複数のサービスを公開できます。

ポート 80 でリッスンする LoadBalancer Service を作成しようとすると、ServiceLB はクラスター内でポート 80 の空きホストを見つけようとします。そのポートを持つホストが使用できない場合、LB は保留中のままになります。

＃＃＃ 使用法

K3s で [LoadBalancer タイプのサービス](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer) を作成します。

### ServiceLB ノード選択の制御

ノードが ServiceLB によって使用されないようにするには、ServiceLB Pod をホストする必要があるノードに次のラベルを追加します。 ラベル付けされていないノードはすべて、ServiceLB には使用されません。

```
svccontroller.k3s.cattle.io/enablelb
```

LoadBalancer のポッドをホストするノードの特定のサブセットを選択するには、ノードとサービスで一致するアノテーション値を設定します。 例えば：

1. ノード A とノード B に「svccontroller.k3s.cattle.io/lbpool=pool1」というラベルを付けます
2. ノード C とノード D に「svccontroller.k3s.cattle.io/lbpool=pool2」というラベルを付けます
3. ポート 443 に「svccontroller.k3s.cattle.io/lbpool=pool1」というラベルを付けて LoadBalancer Service を 1 つ作成します。 このサービスの DaemonSet は、Pod をノード A とノード B にのみデプロイします。
4. ポート 443 に「svccontroller.k3s.cattle.io/lbpool=pool2」というラベルを付けて別の LoadBalancer Service を作成します。 DaemonSet は Pod のみをノード C とノード D にデプロイします。
### サービス LB の無効化

組み込み LB を無効にするには、クラスター内のすべてのサーバーを「--disable=servicelb」オプションで構成します。

これは、MetalLB などの別の LB を実行する場合に必要です。

## ホスト名のないノード

Linode などの一部のクラウド プロバイダーは、ホスト名として「localhost」を使用してマシンを作成しますが、ホスト名がまったく設定されていない場合もあります。 これにより、ドメイン名の解決で問題が発生する可能性があります。 「--node-name」フラグまたは「K3S_NODE_NAME」環境変数を使用して K3s を実行すると、ノード名が渡されてこの問題が解決されます。