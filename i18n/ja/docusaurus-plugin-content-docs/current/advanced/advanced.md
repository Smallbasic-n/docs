---
title: "詳細なオプションと構成"
weight: 45
aliases:
  - /k3s/latest/en/running/
  - /k3s/latest/en/configuration/
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

このセクションでは、K3sの実行と管理のさまざまな方法を説明する高度な情報と、K3sを使用するためにホストOSを準備するために必要な手順について説明します。

## 証明書の交換

### 自動的な交換
デフォルトでは、k3sの証明書は12カ月で失効します。

もし証明書が失効した、もしくは失効まで90日ほどになった時、k3sが再起動したときに証明書が交換されます。

### 手動で交換

手動で証明書を交換するには、`k3s certificate rotate`サブコマンドを使います。:

```bash
# K3sを停止します。
systemctl stop k3s
# 証明書を交換します。
k3s certificate rotate
# k3sを起動します。
systemctl start k3s
```

証明書を個別もしくはすべてを証明書の名前を指定して交換することもできます。:

```bash
k3s certificate rotate --service <SERVICE>,<SERVICE>
```

次の証明書は交換されます。: `admin`, `api-server`, `controller-manager`, `scheduler`, `k3s-controller`, `k3s-server`, `cloud-controller`, `etcd`, `auth-proxy`, `kubelet`, `kube-proxy`.


## マニュフェストの自動デプロイ

`/var/lib/rancher/k3s/server/manifests`で見つかったファイルは自動的に`kubectl apply`を実行したときのようにKubernetesにデプロイされます。これは、初めて起動したときか、そのファイルが変更された時に読み込まれます。マニュフェストを削除しても、自動的にクラスタから削除されるわけではありません。

Helmチャートをデプロイすることに関する情報は、[Helm.](../helm/helm.md)をご覧ください。

## HTTPプロキシ設定

もしあなたがk3sをこのような環境で動かしているならば、外部との接続はHTTPプロキシを通してのみになり、あなたはk3sのsystemdサービスにプロキシ設定をする必要があります。それらのプロキシ設定はk3sと組み込まれたcontainerdとkubeletで使用されます。

k3sインストールスクリプトは自動的に現在のシェルに`HTTP_PROXY`, `HTTPS_PROXY`, `NO_PROXY`、また`CONTAINERD_HTTP_PROXY`, `CONTAINERD_HTTPS_PROXY`, `CONTAINERD_NO_PROXY`変数がある場合、あなたのsystemdサービスにこの変数が書き込まれます。通常は次のファイルに書き込まれます。:
- `/etc/systemd/system/k3s.service.env`
- `/etc/systemd/system/k3s-agent.service.env`

もちろん、あなたはこれらのファイルを編集し、プロキシを設定することも可能です。

k3sは自動的に内部のPodとServiceのIPレンジを設定し、クラスタのDNSドメインを`NO_PROXY`エントリに設定します。Kubernetesノード自身が使用するIPアドレス範囲（ノードのパブリックIPとプライベートIP）が`NO_PROXY`リストに含まれていること、またはプロキシを通してノードに到達できることを確認する必要があります。

```
HTTP_PROXY=http://your-proxy.example.com:8888
HTTPS_PROXY=http://your-proxy.example.com:8888
NO_PROXY=127.0.0.0/8,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16
```

もしあなたがプロキシ設定をcontainerdのみに適用し、k3sとkubeletに影響を与えたくない場合、あなたは`CONTAINERD_`プレフィックスがついた変数を利用することができます。:

```
CONTAINERD_HTTP_PROXY=http://your-proxy.example.com:8888
CONTAINERD_HTTPS_PROXY=http://your-proxy.example.com:8888
CONTAINERD_NO_PROXY=127.0.0.0/8,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16
```

## Dockerをコンテナランタイムとして利用する

k3sはデフォルトで[containerd](https://containerd.io/)をコンテナランタイムとして利用しています。
Kubernets 1.24までは、kubeletはdockershimを内蔵し、コンポーネントはdockerdと通信できます。
k3s 1.24以降は[cri-dockerd](https://github.com/Mirantis/cri-dockerd)により、Dockerコンテナランタイムを引き続き使用しながら、以前のK3sのリリースからシームレスにアップグレードすることができます。

containerdの代わりにDockerを使う方法:

1. DockerをK3sノードにインストールします。Rancherの[Docker installation scripts](https://github.com/rancher/install-docker)をDockerのインストールにも使えます。:

    ```bash
    curl https://releases.rancher.com/install-docker/20.10.sh | sh
    ```
2. k3sを`--docker`オプションを使ってインストールします。:

    ```bash
    curl -sfL https://get.k3s.io | sh -s - --docker
    ```

3. クラスタが利用可能ということを確認します。:

    ```bash
    $ sudo k3s kubectl get pods --all-namespaces
    NAMESPACE     NAME                                     READY   STATUS      RESTARTS   AGE
    kube-system   local-path-provisioner-6d59f47c7-lncxn   1/1     Running     0          51s
    kube-system   metrics-server-7566d596c8-9tnck          1/1     Running     0          51s
    kube-system   helm-install-traefik-mbkn9               0/1     Completed   1          51s
    kube-system   coredns-8655855d6-rtbnb                  1/1     Running     0          51s
    kube-system   svclb-traefik-jbmvl                      2/2     Running     0          43s
    kube-system   traefik-758cd5fc85-2wz97                 1/1     Running     0          43s
    ```

4. dockerコンテナが利用可能ということを確認します。:

    ```bash
    $ sudo docker ps
    CONTAINER ID        IMAGE                     COMMAND                  CREATED              STATUS              PORTS               NAMES
    3e4d34729602        897ce3c5fc8f              "entry"                  About a minute ago   Up About a minute                       k8s_lb-port-443_svclb-traefik-jbmvl_kube-system_d46f10c6-073f-4c7e-8d7a-8e7ac18f9cb0_0
    bffdc9d7a65f        rancher/klipper-lb        "entry"                  About a minute ago   Up About a minute                       k8s_lb-port-80_svclb-traefik-jbmvl_kube-system_d46f10c6-073f-4c7e-8d7a-8e7ac18f9cb0_0
    436b85c5e38d        rancher/library-traefik   "/traefik --configfi…"   About a minute ago   Up About a minute                       k8s_traefik_traefik-758cd5fc85-2wz97_kube-system_07abe831-ffd6-4206-bfa1-7c9ca4fb39e7_0
    de8fded06188        rancher/pause:3.1         "/pause"                 About a minute ago   Up About a minute                       k8s_POD_svclb-traefik-jbmvl_kube-system_d46f10c6-073f-4c7e-8d7a-8e7ac18f9cb0_0
    7c6a30aeeb2f        rancher/pause:3.1         "/pause"                 About a minute ago   Up About a minute                       k8s_POD_traefik-758cd5fc85-2wz97_kube-system_07abe831-ffd6-4206-bfa1-7c9ca4fb39e7_0
    ae6c58cab4a7        9d12f9848b99              "local-path-provisio…"   About a minute ago   Up About a minute                       k8s_local-path-provisioner_local-path-provisioner-6d59f47c7-lncxn_kube-system_2dbd22bf-6ad9-4bea-a73d-620c90a6c1c1_0
    be1450e1a11e        9dd718864ce6              "/metrics-server"        About a minute ago   Up About a minute                       k8s_metrics-server_metrics-server-7566d596c8-9tnck_kube-system_031e74b5-e9ef-47ef-a88d-fbf3f726cbc6_0
    4454d14e4d3f        c4d3d16fe508              "/coredns -conf /etc…"   About a minute ago   Up About a minute                       k8s_coredns_coredns-8655855d6-rtbnb_kube-system_d05725df-4fb1-410a-8e82-2b1c8278a6a1_0
    c3675b87f96c        rancher/pause:3.1         "/pause"                 About a minute ago   Up About a minute                       k8s_POD_coredns-8655855d6-rtbnb_kube-system_d05725df-4fb1-410a-8e82-2b1c8278a6a1_0
    4b1fddbe6ca6        rancher/pause:3.1         "/pause"                 About a minute ago   Up About a minute                       k8s_POD_local-path-provisioner-6d59f47c7-lncxn_kube-system_2dbd22bf-6ad9-4bea-a73d-620c90a6c1c1_0
    64d3517d4a95        rancher/pause:3.1         "/pause"
    ```

## etcdctlを利用する

etcdctlはCLIでetcd serverを利用するものを持っています。k3sはetcdctlを持っていません。

もしあなたがetcdctlをk3sの埋め込まれたetcdで使いたい場合、[official documentation](https://etcd.io/docs/latest/install/)を利用し、etcdctlをインストールすることもできます。

```bash
ETCD_VERSION="v3.5.5"
ETCD_URL="https://github.com/etcd-io/etcd/releases/download/${ETCD_VERSION}/etcd-${ETCD_VERSION}-linux-amd64.tar.gz"
curl -sL ${ETCD_URL} | sudo tar -zxv --strip-components=1 -C /usr/local/bin
```
その後、etcdctlを使用して、K3sが管理する証明書と鍵を認証に使用するよう設定することができます。

```bash
sudo etcdctl version \
  --cacert=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt \
  --cert=/var/lib/rancher/k3s/server/tls/etcd/client.crt \
  --key=/var/lib/rancher/k3s/server/tls/etcd/client.key
```

## containerdを設定

k3sはcontainerd用のconfig.tomlを`/var/lib/rancher/k3s/agent/etc/containerd/config.toml`に生成します。

高度なカスタマイズをするためにあなたは`config.toml.tmpl`ファイルを同じディレクトリに作成できます。

`config.toml.tmpl`はGoのテンプレートファイルの構造に従い、`config.Node`構造体はテンプレートの基準に沿ったものでなければなりません。[このフォルダ](https://github.com/k3s-io/k3s/blob/master/pkg/agent/templates)を見て、LinuxとWindowsの構造体をカスタマイズしたコンフィグレーションファイルwの例を見てください。

## NVIDIA コンテナランタイム

k3sが起動したとき、k3sは自動的に認識しNVIDIAコンテナランタイムを設定します。
1. このnvidiaコンテナパッケージレポジトリをそのノードに展開します。:
    https://nvidia.github.io/libnvidia-container/
1. nvidiaコンテナパッケージをインストールします。次のようにします。:
   `apt install -y nvidia-container-runtime cuda-drivers-fabricmanager-515 nvidia-headless-515-server`
1. K3sをインストールするか、既にインストールされているならそれを再起動します。:
    `curl -ksL get.k3s.io | sh -`
1. nvidiaコンテナランタイムがk3sで見つかったことを確認します。:
    `grep nvidia /var/lib/rancher/k3s/agent/etc/containerd/config.toml`

これにより、どのランタイムの実行ファイルが見つかったかに応じて、`nvidia` および/または `nvidia-実験的` ランタイムが自動的に containerd 設定に追加されます。
あなたはRuntimeClass定義をあなたのクラスタに追加する必要があり、また、Pod仕様に `runtimeClassName: nvidia` を設定し、適切なランタイムを明示的に要求するPodをデプロイします。:
```yaml
apiVersion: node.k8s.io/v1
kind: RuntimeClass
metadata:
  name: nvidia
handler: nvidia
---
apiVersion: v1
kind: Pod
metadata:
  name: nbody-gpu-benchmark
  namespace: default
spec:
  restartPolicy: OnFailure
  runtimeClassName: nvidia
  containers:
  - name: cuda-container
    image: nvcr.io/nvidia/k8s/cuda-sample:nbody
    args: ["nbody", "-gpu", "-benchmark"]
    resources:
      limits:
        nvidia.com/gpu: 1
    env:
    - name: NVIDIA_VISIBLE_DEVICES
      value: all
    - name: NVIDIA_DRIVER_CAPABILITIES
      value: all
```

なお、NVIDIA Container Runtimeは、[NVIDIA Device Plugin](https://github.com/NVIDIA/k8s-device-plugin/)や[GPU Feature Discovery](https://github.com/NVIDIA/gpu-feature-discovery/)でも頻繁に使用されます。この場合は、上記のようにPod Specsに `runtimeClassName: nvidia`を含めるように修正して、別途インストールする必要があります。

## Agentlessサーバを実行する (実験的)
> **警告:** この機能は実験的です。

`disable-agent` フラグで起動すると、サーバーは kubelet、コンテナランタイム、または CNI を実行しません。また、クラスタに Node リソースを登録しないので、`kubectl get nodes` の出力にも表示されません。
kubeletをホストしていないため、Podを実行したり、組み込みetcdコントローラやシステムアップグレードコントローラなど、クラスタノードの列挙に依存するオペレータによって管理したりすることはできません。

エージェントレスサーバーの実行は、エージェントやワークロードによる検出から制御プレーンノードを隠したい場合に有利ですが、クラスタオペレータのサポートがないために管理オーバーヘッドが増加する代償を払うことになるかもしれません。

## Rootlessサーバを実行 (実験的)
> **警告:** この機能は実験的です。

ルートレスモードでは、非特権ユーザーとしてK3sサーバーを実行することで、コンテナ脱走攻撃の可能性からホスト上の本当のルートを保護することができます。

ルートレスKubernetesの詳細については、https://rootlesscontaine.rs/ を参照してください。

### Rootlessモードでの既知の問題

* **ポート**

  ルートレスで実行する場合、新しいネットワーク・ネームスペースが作成されます。 これは、K3sインスタンスがホストからかなり切り離されたネットワークで実行されていることを意味します。
  ホストからK3sで実行されるサービスにアクセスする唯一の方法は、K3sネットワークネームスペースへのポートフォワードをセットアップすることです。
  ルートレスK3sは、6443と1024以下のサービスポートを10000のオフセットで自動的にホストにバインドするコントローラを含んでいます。

  例えば、ポート80のサービスは、ホスト上で10080になりますが、8080はオフセットなしで8080になります。現在、LoadBalancerサービスのみが自動的にバインドされます。

* **Cgroups**

  Cgroup v1およびHybrid v1/v2はサポートされておらず、純粋なCgroup v2のみがサポートされています。ルートレスで実行中にCgroupが見つからないためにK3sの起動に失敗した場合、ノードがハイブリッドモードで、「見つからない」Cgroupがまだv1コントローラにバインドされている可能性があります。

* **マルチノード/マルチプロセスクラスタ**

  複数ノードのルートレスクラスター、または同一ノード上の複数のルートレスk3sプロセスは、現在サポートされていません。詳しくは[#6488](https://github.com/k3s-io/k3s/issues/6488#issuecomment-1314998091)を参照してください。

### Rootlessサーバを実行
* cgroup v2 delegation を有効にします。https://rootlesscontaine.rs/getting-started/common/cgroup2/ を参照してください。
  このステップは必須です。適切なcgroupsが委譲されていないと、ルートレスkubeletは起動に失敗してしまうからです。

* `https://github.com/k3s-io/k3s/blob/<VERSION>/k3s-rootless.service`](https://github.com/k3s-io/k3s/blob/master/k3s-rootless.service) から `k3s-rootless.service` をダウンロードする。
  k3s-rootless.service`と`k3s`のバージョンは必ず同じものを使用してください。

* k3s-rootless.service` を `~/.config/systemd/user/k3s-rootless.service` へインストールします。
  このファイルをシステム全体のサービス（`/etc/systemd/...`）としてインストールすることはサポートされていません。
  k3s` バイナリのパスによっては、ファイルの `ExecStart=/usr/local/bin/k3s ...` 行を修正する必要があるかもしれません。

* `systemctl --user daemon-reload`を実行

* `systemctl --user enable --now k3s-rootless`を実行

* `KUBECONFIG=~/.kube/k3s.yaml kubectl get pods -A`を実行し,podが安定していることを確認します。

> **注意:** `k3s server --rootless`をターミナル上で実行しないでください。ターミナルセッションではcgroup v2は許可されていません。
> どうしてもターミナルで試したい場合は、`systemd-run --user -p Delegate=yes --tty k3s server --rooless`でsystemdスコープに包むことができるようになります。

### 高度なrootless設定

ルートレスK3sでは、ホストとユーザーネットワークの名前空間間の通信に[rootlesskit](https://github.com/rootless-containers/rootlesskit)と[slirp4netns](https://github.com/rootless-containers/slirp4netns)を使用しています。
rootlesskitとslirp4netnsで使用する設定の一部は、環境変数で設定することができます。これらを設定するには、k3s-rootless systemd unit の `Environment` フィールドに追加するのが最も良い方法です。

| 変数                                 | デフォルト    | 詳細
|--------------------------------------|--------------|------------
| `K3S_ROOTLESS_MTU`                   | 1500         | slirp4netns 仮想インターフェイスの MTU を設定します。
| `K3S_ROOTLESS_CIDR`                  | 10.41.0.0/16 | slirp4netns の仮想インターフェイスで使用する CIDR を設定します。
| `K3S_ROOTLESS_ENABLE_IPV6`           | autotedected | slirp4netns の IPv6 サポートを有効にします。指定しない場合は、K3sがデュアルスタック動作に設定されている場合、自動的に有効になります。
| `K3S_ROOTLESS_PORT_DRIVER`           | ビルトイン    | `builtin`または`slirp4netns`のどちらかのルートレスポートドライバを選択します。builtinの方が高速ですが、インバウンドパケットの元のソースアドレスをマスカレードします。
| `K3S_ROOTLESS_DISABLE_HOST_LOOPBACK` | true         | ゲートウェイインターフェースを経由したホストのループバックアドレスへのアクセスを有効にするかどうかを制御します。セキュリティ上の理由から、変更しないことをお勧めします。

### Rootlessでのトラブルシューティング

* `systemctl --user status k3s-rootless`はデーモンの状態を確認します。
* `journalctl --user -f -u k3s-rootless`はデーモンのログを確認します。
* https://rootlesscontaine.rs/ も見てください。

## ノードのLabelとTaint

K3sエージェントは、kubeletにラベルとテイントを追加するオプション `--node-label` と `--node-taint` を設定することができます。この2つのオプションは、[登録時に](../reference/agent-config.md#node-labels-and-taints-for-agents)ラベルやテイントを追加するだけなので、ノードが最初にクラスタに参加するときにのみ設定できます。

Kubernetesの現在のすべてのバージョンは、`kubernetes.io`と`k8s.io`のプレフィックスを持つほとんどのラベル、特に`kubernetes.io/role`ラベルを含むラベルでノードを登録することを制限します。許可されないラベルを持つノードを起動しようとすると、K3sは起動に失敗します。Kubernetesの作者が述べている通りです：

> ノードは、独自のロールラベルを主張することは許可されていません。ノードの役割は通常、特権ノードやコントロールプレーンタイプのノードを識別するために使用され、ノードがそのプールに自分自身をラベル付けすることを許可すると、侵害されたノードがより高い特権資格情報へのアクセスを与えるワークロード（コントロールプレーンデーモンセットなど）をトリビア的に引き付けることができます。

詳しくは、[SIG-Auth KEP 279](https://github.com/kubernetes/enhancements/blob/master/keps/sig-auth/279-limit-node-access/README.md#proposal)を参照してください。

ノード登録後にノードラベルやテイントを変更したり、予約ラベルを追加したりしたい場合は、`kubectl`を使用する必要があります。[Taint](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/)や[ノードのLabel](https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes/#add-a-label-to-a-node)の追加方法についてはKubernetes公式ドキュメントを参照してください。

## インストールスクリプトでサービスを開始

インストールスクリプトは、OSがsystemdまたはopenrcを使用しているかどうかを自動検出し、インストールプロセスの一部としてサービスを有効にして開始します。
* openrcを使用している場合、ログは`/var/log/k3s.log`に作成されます。
* systemd で実行する場合、ログは `/var/log/syslog` に作成され、`journalctl -u k3s` （エージェントでは `journalctl -u k3s-agent` ）で閲覧できます。

インストールスクリプトで自動起動とサービス有効化を無効にする例:

```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_SKIP_START=true INSTALL_K3S_SKIP_ENABLE=true sh -
```

## 追加のOSの準備

### 古いiptablesのバージョン

いくつかの一般的なLinuxディストリビューションは、重複したルールの蓄積を引き起こすバグを含むiptablesのバージョンを出荷しており、ノードのパフォーマンスと安定性に悪影響を及ぼします。この問題の影響を受けているかどうかを判断する方法については、[#3117](https://github.com/k3s-io/k3s/issues/3117)を参照してください。

K3sには、正常に機能するiptablesのバージョン（v1.8.8）が含まれています。K3sを起動する際に `--prefer-bundled-bin` オプションを指定するか、オペレーティングシステムから iptables/nftables パッケージをアンインストールすれば、K3sにバンドル版のiptablesを使用するように指示できます。

:::info Version Gate

`---prefer-bundled-bin`フラグは、2022-12年のリリース（v1.26.0+k3s1, v1.25.5+k3s1, v1.24.9+k3s1, v1.23.15+k3s1 ）から利用可能です。

:::

### Red Hat Enterprise Linux / CentOS

firewalldを無効化しておくほうがいいです。:
```bash
systemctl disable firewalld --now
```

もし、nm-cloud-setupが有効なら、無効にする必要があり、ノードを再起動する必要もあります。:
```bash
systemctl disable nm-cloud-setup.service nm-cloud-setup.timer
reboot
```

### Raspberry Pi

Raspberry Pi OSはDebianベースのため、iptablesのバージョンが古い場合があります。workarounds](#old-iptables-versions)を参照してください。

標準的なRaspberry Pi OSのインストールでは、`cgroups`を有効にして起動しません。**K3S** は systemd サービスを開始するために `cgroups` を必要とします。cgroups` は、`cgroup_memory=1 cgroup_enable=memory` を `/boot/cmdline.txt` に追加することで有効にすることができます。

cmdline.txtの例:
```
console=serial0,115200 console=tty1 root=PARTUUID=58b06195-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait cgroup_memory=1 cgroup_enable=memory
```

Ubuntu 21.10より、Raspberry Piのvxlanサポートは、別のカーネルモジュールに移行されました。
```bash
sudo apt install linux-modules-extra-raspi
```

## k3sをDocker内で実行

DockerでK3を動かすには、いくつかの方法があります。:

<Tabs>
<TabItem value='K3d' default>

[k3d](https://github.com/k3d-io/k3d)は、Docker上でK3sを簡単に実行するために設計されたユーティリティです。

これはMacOSの[brew](https://brew.sh/)で利用可能です。:

```bash
brew install k3d
```

</TabItem>
<TabItem value="Docker Compose">

K3sのリポジトリにある`docker-compose.yml`は、DockerからK3sを実行する方法の[例](https://github.com/k3s-io/k3s/blob/master/docker-compose.yml)として機能します。このレポにある`docker-compose`を実行するために、実行します：

```bash
docker-compose up --scale agent=3
# kubeconfig is written to current dir

kubectl --kubeconfig kubeconfig.yaml get node

NAME           STATUS   ROLES    AGE   VERSION
497278a2d6a2   Ready    <none>   11s   v1.13.2-k3s2
d54c8b17c055   Ready    <none>   11s   v1.13.2-k3s2
db7a5a5a5bdd   Ready    <none>   12s   v1.13.2-k3s2
```

DockerでAgentのみを起動させたい場合は、`docker-compose up agent`を使用します。.

</TabItem>
<TabItem value="Docker">

Dockerを使うならば, K3sサーバーとエージェントを動作させるためのイメージ`rancher/k3s`も用意されています。
Using the `docker run` command:

```bash
sudo docker run \
  -d --tmpfs /run \
  --tmpfs /var/run \
  -e K3S_URL=${SERVER_URL} \
  -e K3S_TOKEN=${NODE_TOKEN} \
  --privileged rancher/k3s:vX.Y.Z
```
</TabItem>
</Tabs>

## SELinux サポート

:::info Version Gate

v1.19.4+k3s1から有効です。

:::

SELinuxがデフォルトで有効になっているシステム（CentOSなど）にK3sをインストールする場合、適切なSELinuxポリシーがインストールされていることを確認する必要があります。

<Tabs>
<TabItem value="自動的なインストール" default>

[インストールスクリプト](../installation/configuration.md#configuration-with-install-script)は、エアギャップによるインストールを行わない場合、互換性のあるシステムであればRancher RPMリポジトリからSELinux RPMを自動インストールします。`INSTALL_K3S_SKIP_SELINUX_RPM=true`を設定することで自動インストールをスキップすることができます。

</TabItem>

<TabItem value="手動でインストール" default>

必要なポリシーは、以下のコマンドでインストールすることができます。:
```bash
yum install -y container-selinux selinux-policy-base
yum install -y https://rpm.rancher.io/k3s/latest/common/centos/7/noarch/k3s-selinux-0.2-1.el7_8.noarch.rpm
```

インストールスクリプトを失敗させるのではなく、警告をログに残すようにするには、次の環境変数を設定します： `INSTALL_K3S_SELINUX_WARN=true`です。
</TabItem>
</Tabs>

### SELinux Enforcementを有効化させる

SELinuxを活用するには、K3sサーバーとエージェントを起動する際に`--selinux`フラグを指定します。
  
このオプションは、K3sの[設定ファイル](#)で指定することも可能です。

```
selinux: true
```

SELinuxの下でカスタム`--data-dir`を使用することはサポートされていません。それをカスタマイズするには、おそらく独自のカスタムポリシーを書く必要があります。ContainerRuntimes用のSELinuxポリシーファイルを含む[containers/container-selinux](https://github.com/containers/container-selinux)リポジトリや、K3s用のSELinuxポリシーを含む[rancher/k3s-selinux](https://github.com/rancher/k3s-selinux)リポジトリを参照するとよいでしょう。

## eStargzのLazy Pullingを可能にする。 (実験的)

### lazy pullingとeStargzって何？

コンテナのライフサイクルにおいて、イメージのプルアップは時間のかかるステップの1つとして知られています。
[Harter,et al.](https://www.usenix.org/conference/fast16/technical-sessions/presentation/harter)によると、

> コンテナ起動時間の76％がパッケージの取り出しに費やされるが、そのうちの6.4％しかデータが読み込まれない。

この問題に対処するため、k3s 実験的はイメージコンテンツの*lazy pull*をサポートしています。
これは、イメージ全体が引き出される前にk3sがコンテナを開始することを可能にします。
代わりに、コンテンツの必要なチャンク（個々のファイルなど）がオンデマンドでフェッチされます。
特に大きなイメージの場合、このテクニックはコンテナ起動のレイテンシーを短縮することができます。

遅延プルを有効にするには、ターゲットイメージを [*eStargz*](https://github.com/containerd/stargz-snapshotter/blob/main/docs/stargz-estargz.md) のようにフォーマットする必要があります。
これは、OCI 代替でありながら、100% OCI 互換の遅延プル用画像フォーマットです。
この互換性により、eStargzは標準的なコンテナレジストリ（例：ghcr.io）にプッシュすることができ、eStargzに依存しないランタイムでも*実行可能*なものとなっています。

eStargzは、[Google CRFSプロジェクトが提案するstargzフォーマット](https://github.com/google/crfs)をベースに開発されていますが、コンテンツの検証やパフォーマンスの最適化など、実用的な機能を備えています。
遅延プルやeStargzの詳細については、[Stargz Snapshotterプロジェクトリポジトリ](https://github.com/containerd/stargz-snapshotter)を参照してください。

### eStargzの遅延プルのためにk3sを構成する。

以下に示すように、k3sサーバおよびエージェントでは、`--snapshotter=stargz`オプションが必要です。

```bash
k3s server --snapshotter=stargz
```

この設定により、eStargz 形式のイメージに対して遅延プルを実行することができます。
次の例のPodマニフェストでは、eStargzフォーマットの`node:13.13.0`イメージ（`ghcr.io/stargz-containers/node:13.13.0-esgz`）を使用しています。
stargz snapshotterが有効な場合、K3sはこのイメージに対して遅延プルを実行します。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nodejs
spec:
  containers:
  - name: nodejs-estargz
    image: ghcr.io/stargz-containers/node:13.13.0-esgz
    command: ["node"]
    args:
    - -e
    - var http = require('http');
      http.createServer(function(req, res) {
        res.writeHead(200);
        res.end('Hello World!\n');
      }).listen(80);
    ports:
    - containerPort: 80
```

## ログソースの追加

K3s用の[Rancher logging](https://rancher.com/docs/rancher/v2.6/en/logging/helm-chart-options/)は、Rancherを使用せずにインストールすることができます。その際は、以下の手順で実行してください：

```bash
helm repo add rancher-charts https://charts.rancher.io
helm repo update
helm install --create-namespace -n cattle-logging-system rancher-logging-crd rancher-charts/rancher-logging-crd
helm install --create-namespace -n cattle-logging-system rancher-logging --set additionalLoggingSources.k3s.enabled=true rancher-charts/rancher-logging
```

## ネットワークポリシーログの追加

ネットワークポリシーによってドロップされたパケットをログに記録することができます。パケットはiptablesのNFLOGアクションに送られ、ブロックしたネットワークポリシーを含むパケットの詳細が表示されます。

NFLOGをログエントリに変換するには、ulogd2をインストールし、`[log1]`を`group=100`で読み出すように設定します。その後、ulogd2サービスを再起動すると、新しい設定がコミットされます。

NFLOGアクションにヒットするパケットは、tcpdumpを使用して読み取ることもできます：
```bash
tcpdump -ni nflog:100
```
ただし、この場合、パケットをブロックしたネットワークポリシーは表示されないことに注意してください。


ネットワークポリシーのルールによってパケットがブロックされると、ログメッセージが `/var/log/ulog/syslogemu.log` に表示されます。トラフィックが多い場合、ロギングファイルが非常に速く成長する可能性があります。これを抑制するには、該当するネットワークポリシーに以下の注釈を付けて、iptablesのパラメータ「limit」「limit-burst」を適切に設定します：
```bash
* kube-router.io/netpol-nflog-limit=<LIMIT-VALUE>
* kube-router.io.io/netpol-nflog-limit-burst=<LIMIT-BURST-VALUE>
```

デフォルトの値は `limit=10/minute` と `limit-burst=10` です。これらのフィールドのフォーマットと可能な値に関する詳細については、iptablesのマニュアルを確認してください。
