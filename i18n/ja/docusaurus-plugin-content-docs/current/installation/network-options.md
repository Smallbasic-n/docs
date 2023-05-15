---
title: "ネットワークオプション"
weight: 25
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

このページでは、Flannel の構成または交換、および IPv6 の構成を含む、K3s ネットワーク構成オプションについて説明します。

> **注:** CoreDNS、Traefik、およびサービス LB に関する情報については、[ネットワーク](../networking/networking.md) ページを参照してください。

## フランネルのオプション

[Flannel](https://github.com/flannel-io/flannel/blob/master/README.md) は、Kubernetes Container Network Interface (CNI) を実装するレイヤー 3 ネットワーク ファブリックの軽量プロバイダーです。 これは、一般に CNI プラグインと呼ばれるものです。

* Flannel オプションはサーバー ノードでのみ設定でき、クラスター内のすべてのサーバーで同一である必要があります。
* Flannel のデフォルトのバックエンドは「vxlan」です。 暗号化を有効にするには、「wireguard-native」バックエンドを使用します。
※最近のバージョンのUbuntuでRasperry Piでvxlanを使う場合は【追加準備】(../advanced/advanced.md#raspberry-pi)が必要です。
* Flannel バックエンドとして「wireguard-native」を使用すると、一部の Linux ディストリビューションで追加のモジュールが必要になる場合があります。 詳しくは【WireGuardインストールガイド】(https://www.wireguard.com/install/)をご覧ください。
   WireGuard のインストール手順により、オペレーティング システムに適切なカーネル モジュールが確実にインストールされます。
   WireGuard Flannel バックエンドを使用する前に、WireGuard カーネル モジュールがすべてのノード (サーバーとエージェントの両方) で利用可能であることを確認する必要があります。


   CLI フラグと値 | 説明
   --------------------|----------------
  `--flannel-ipv6-masq` | マスカレード ルールを IPv6 トラフィックに適用します (IPv4 のデフォルト)。 デュアルスタックまたは IPv6 のみのクラスターにのみ適用されます。 `none` 以外の Flannel バックエンドと互換性があります。 | |
  `--flannel-external-ip` | 内部 IP ではなく、ノードの外部 IP アドレスを Flannel トラフィックの宛先として使用します。 ノードで --node-external-ip が設定されている場合にのみ適用されます。 | |
  `--flannel-backend=vxlan` | VXLAN を使用してパケットをカプセル化します。 Raspberry Pi に追加のカーネル モジュールが必要になる場合があります。 | |
  `--flannel-backend=host-gw` | IP ルートを使用して、ノード IP 経由でサブネットをポッドします。 クラスター内のすべてのノード間の直接レイヤー 2 接続が必要です。 | |
  `--flannel-backend=wireguard-native` | WireGuard を使用して、ネットワーク トラフィックをカプセル化および暗号化します。 追加のカーネル モジュールが必要になる場合があります。 | |
  `--flannel-backend=ipsec` | `swanctl` バイナリ経由で strongSwan IPSec を使用して、ネットワーク トラフィックを暗号化します。 (非推奨。v1.27.0 で削除されます) |
  `--flannel-backend=wireguard` | `wg` バイナリ経由で WireGuard を使用して、ネットワーク トラフィックを暗号化します。 追加のカーネル モジュールと構成が必要になる場合があります。 (非推奨。v1.26.0 で削除されます) |
  `--flannel-backend=none` | Flannel を完全に無効にします。 | |

:::info Version Gate

K3s には、2022-12 リリース (v1.26.0+k3s1、v1.25.5+k3s1、v1.24.9+k3s1、v1.23.15+k3s1) から、strongSwan の「swanctl」および「charon」バイナリが含まれなくなりました。 「ipsec」バックエンドを使用する場合は、これらのリリースにアップグレードまたはインストールする前に、ノードに正しいパッケージをインストールしてください。

:::

### 「wireguard」または「ipsec」から「wireguard-native」への移行

従来の「wireguard」バックエンドでは、ホストに「wg」ツールをインストールする必要があります。 このバックエンドは K3s v1.26 で削除され、カーネルと直接インターフェースする「wireguard-native」バックエンドが優先されます。

従来の「ipsec」バックエンドでは、ホストに「swanctl」および「charon」バイナリをインストールする必要があります。 このバックエンドは K3s v1.27 で削除され、「wireguard-native」バックエンドが優先されます。

ユーザーはできるだけ早く新しいバックエンドに移行することをお勧めします。 移行には、ノードが新しい構成を作成するまでの短いダウンタイムが必要です。 次の 2 つの手順に従う必要があります。

1. すべてのサーバー ノードで K3s 構成を更新します。 構成ファイルを使用する場合、「/etc/rancher/k3s/config.yaml」には、「flannel-backend: wireguard」または「flannel-backend: ipsec」ではなく、「flannel-backend: wireguard-native」を含める必要があります。 systemd ユニットで CLI フラグを介して K3 を設定している場合は、同等のフラグを変更する必要があります。
2. サーバーから始めて、すべてのノードを再起動します。

## カスタム CNI

K3s を `--flannel-backend=none` で起動し、選択した CNI をインストールします。 ほとんどの CNI プラグインには独自のネットワーク ポリシー エンジンが付属しているため、競合を避けるために「--disable-network-policy」も設定することをお勧めします。 Canal と Calico では IP 転送を有効にする必要があります。 以下の手順を参照してください。
<Tabs>
<TabItem value="Canal" default>

[Project Calico Docs](https://docs.tigera.io/calico/) Web サイトにアクセスします。 手順に従って Canal をインストールします。 `container_settings` セクションで IP 転送が許可されるように Canal YAML を変更します。次に例を示します。
```yaml
"container_settings": {
  "allow_ip_forwarding": true
}
```

Canal YAML を適用します。

ホストで次のコマンドを実行して、設定が適用されていることを確認します。
```bash
cat /etc/cni/net.d/10-canal.conflist
```

IP 転送が true に設定されていることがわかります。
</TabItem>
<TabItem value="Calico" default>

[Calico CNI プラグイン ガイド](https://docs.tigera.io/calico/latest/reference/configure-cni-plugins) に従ってください。 `container_settings` セクションで IP 転送が許可されるように、Calico YAML を変更します。次に例を示します。
```yaml
"container_settings": {
  "allow_ip_forwarding": true
}
```

Calico YAML を適用します。

ホストで次のコマンドを実行して、設定が適用されていることを確認します。
```bash
cat /etc/cni/net.d/10-calico.conflist
```

IP 転送が true に設定されていることがわかります。

</TabItem>
</Tabs>

## コントロール プレーンの出力セレクターの設定

K3s エージェントとサーバーは、ノード間の Websocket トンネルを維持します。これは、コントロール プレーン (apiserver) とエージェント (kubelet および containerd) コンポーネント間の双方向通信をカプセル化するために使用されます。
これにより、エージェントは、kubelet およびコンテナーのランタイム ストリーミング ポートを着信接続に公開せずに動作し、エージェントを無効にして動作しているときにコントロール プレーンがクラスター サービスに接続できるようになります。
この機能は、他の Kubernetes ディストリビューションで一般的に使用される [Konnectivity](https://kubernetes.io/docs/tasks/extend-kubernetes/setup-konnectivity/) サービスと同等であり、apiserver の egress セレクター構成を介して管理されます。

egress セレクター モードは、`--egress-selector-mode` フラグを介してサーバー上で構成でき、4 つのモードを提供します。
* `disabled`: apiserver はエージェント トンネルを使用して kubelets またはクラスター エンドポイントと通信しません。
   このモードでは、サーバーが kubelet、CNI、および kube-proxy を実行し、エージェントに直接接続する必要があります。そうしないと、apiserver がサービス エンドポイントにアクセスしたり、「kubectl exec」および「kubectl logs」を実行したりできなくなります。
* `agent` (デフォルト): apiserver はエージェント トンネルを使用して kubelets と通信します。
   このモードでは、サーバーで kubelet、CNI、および kube-proxy も実行する必要があります。そうしないと、apiserver がサービス エンドポイントにアクセスできなくなります。
* `pod`: apiserver は、エージェント トンネルを使用して kubelets およびサービス エンドポイントと通信し、ノードを監視してエンドポイント接続を正しいエージェントにルーティングします。
   **注**: 独自の IPAM を使用し、ノードの PodCIDR 割り当てを尊重しない CNI を使用している場合、これは機能しません。 これらの CNI では、代わりに「cluster」または「agent」を使用する必要があります。
* `cluster`: apiserver はエージェント トンネルを使用して kubelets およびサービス エンドポイントと通信し、エンドポイントを監視することでエンドポイント接続を正しいエージェントにルーティングします。

## デュアル スタック インストール

:::info Version Gate

デュアルスタック ネットワーキングは、K3s v1.21 以降でサポートされています。

:::

クラスターを最初に作成するときに、デュアルスタック ネットワークを構成する必要があります。 IPv4 専用として開始された後は、既存のクラスターで有効にすることはできません。

K3s でデュアル スタックを有効にするには、すべてのサーバー ノードで有効なデュアル スタックの「cluster-cidr」と「service-cidr」を提供する必要があります。 これは有効な設定の例です:
```
--cluster-cidr=10.42.0.0/16,2001:cafe:42:0::/56 --service-cidr=10.43.0.0/16,2001:cafe:42:1::/112
```

有効な「cluster-cidr」と「service-cidr」の値を設定できますが、上記のマスクを使用することをお勧めします。 `cluster-cidr` マスクを変更する場合は、`node-cidr-mask-size-ipv4` および `node-cidr-mask-size-ipv6` の値も変更して、ノードあたりの計画されたポッドとノードの合計に一致させる必要があります。 カウント。 サポートされている最大の「service-cidr」マスクは、IPv4 では /12、IPv6 では /112 です。 パブリック クラウドにデプロイする場合は、ipv6 トラフィックを許可することを忘れないでください。

カスタム CNI プラグイン、つまり Flannel 以外の CNI プラグインを使用している場合は、追加の構成が必要になる場合があります。 プラグインのデュアルスタック ドキュメントを参照して、ネットワーク ポリシーを有効にできるかどうかを確認してください。

> **警告:** Kubernetes 1.24 および 1.25 には、デュアル スタック環境があり、クラスター トラフィックにプライマリ ネットワーク インターフェイスを使用していない場合、ノードの IPv6 アドレスを無視するバグが含まれています。 このバグを回避するには、K3s サーバーとエージェントの両方に次のフラグを追加します。
```
--kubelet-arg=node-ip=0.0.0.0"  # If you want to prioritize IPv6 traffic, use "::" instead of "0.0.0.0".
```

## シングルスタック IPv6 インストール

:::info Version Gate

シングルスタック IPv6 クラスター (IPv4 を使用しないクラスター) は、K3s v1.22 以降でサポートされています。

:::

> **警告:** IPv6 デフォルト ルートがルーター アドバタイズメント (RA) によって設定されている場合、sysctl `net.ipv6.conf.all.accept_ra=2` を設定する必要があります。 それ以外の場合、ノードは期限切れになるとデフォルト ルートをドロップします。 RA を受け入れると、[中間者攻撃] のリスクが高まる可能性があることに注意してください (https://github.com/kubernetes/kubernetes/issues/91507)。

## 分散ハイブリッドまたはマルチクラウド クラスタ

K3s クラスターは、異なるプライベート ネットワークを使用し、直接接続されていないノード (異なるパブリック クラウド内のノードなど) にデプロイできます。 これを実現するために、K3s は VPN メッシュになるトンネルのメッシュを設定します。 これらのノードには、到達可能な IP (パブリック IP など) が割り当てられている必要があります。 サーバー トラフィックは Websocket トンネルを使用し、データプレーン トラフィックはワイヤーガード トンネルを使用します。

このタイプの展開を有効にするには、サーバーに次のパラメーターを追加する必要があります。
```bash
--node-external-ip=<SERVER_EXTERNAL_IP> --flannel-backend=wireguard-native --flannel-external-ip
```
エージェントでは:
```bash
--node-external-ip=<AGENT_EXTERNAL_IP>
```

ここで、「SERVER_EXTERNAL_IP」はサーバー ノードに到達できる IP であり、「AGENT_EXTERNAL_IP」はエージェント/ワーカー ノードに到達できる IP です。 エージェント/ワーカーの「K3S_URL」構成パラメーターは、接続できるように「SERVER_EXTERNAL_IP」を使用する必要があることに注意してください。 [Networking Requirements](../installation/requirements.md#networking) を確認し、リストされたポートへのアクセスを内部アドレスと外部アドレスの両方で許可することを忘れないでください。

`SERVER_EXTERNAL_IP` と `AGENT_EXTERNAL_IP` の両方に接続が必要で、通常はパブリック IP です。

> **警告:** 外部接続がより多くのホップを必要とするため、ノード間の遅延が増加します。 これにより、ネットワークのパフォーマンスが低下し、レイテンシが高すぎる場合はクラスターの正常性にも影響を与える可能性があります。

> **警告:** 組み込みの etcd は、通信に外部 IP を使用しません。 組み込み etcd を使用する場合。 すべてのサーバー ノードは、プライベート IP を介して相互に到達可能である必要があります。

