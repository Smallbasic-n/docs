---
title: K3s エージェントの設定
weight: 2
---

このセクションでは、K3s エージェントを構成する方法を学習します。

- [ロギング](#logging)
- [クラスタ オプション](#cluster-options)
- [データ](#データ)
- [ノード](#ノード)
- [ランタイム](#runtime)
- [ネットワーキング](#networking)
- [カスタマイズされたフラグ](#customized-flags)
- [実験的](#実験的)
- [非推奨](#非推奨)
- [エージェントのノード ラベルとテイント](#node-labels-and-taints-for-agents)
- [K3s エージェント CLI ヘルプ](#k3s-agent-cli-help)

### ロギング

| | フラグ | デフォルト | 説明 |
| | ------------------------------------ | -------- | -------------------------------------------------- ------------------ |
| | `-v` 値 | 0 | ログ レベルの詳細度の数値 |
| | `--vmodule` 値 | 該当なし | ファイルフィルタリングされたロギングの FILE_PATTERN=LOG_LEVEL 設定のコンマ区切りリスト |
| | `--log 値、-l` 値 | 該当なし | ファイルにログ |
| | `--またlogtostderr` | 該当なし | 標準エラーとファイル (設定されている場合) にログを記録 |

### クラスタ オプション

| | フラグ | 環境変数 | 説明 |
| | -------------------------- | -------------------- | ------------------------------------ |
| | `--token 値, -t` 値 | `K3S_TOKEN` | 認証に使用するトークン |
| | --token-file 値 | `K3S_TOKEN_FILE` | 認証に使用するトークン ファイル |
| | `--server 値, -s` 値 | `K3S_URL` | 接続するサーバー |

### データ

| | フラグ | デフォルト | 説明 |
| | ---------------------------- | ---------------------- | -------------------- |
| | `--data-dir 値、-d` 値 | "/var/lib/rancher/k3s" | 状態を保持するフォルダー |
### ノード

| | フラグ | 環境変数 | 説明 |
| | --------------------------- | -------------------- | -------------------------------------------------- ----------------------------------------------- |
| | --node-name 値 | `K3S_NODE_NAME` | ノード名 |
| | `--with-node-id` | 該当なし | ノード名に id を追加 |
| | --node-label 値 | 該当なし | 一連のラベルを使用した kubelet の登録と開始 |
| | --node-taint 値 | 該当なし | taint のセットで kubelet を登録する |
| | `--protect-kernel-defaults` | 該当なし | カーネル チューニングの動作。 設定されている場合、カーネル調整可能変数が kubelet のデフォルトと異なる場合にエラーが発生します。 | |
| | `--selinux` | `K3S_SELINUX` | containerd で SELinux を有効にする |
| | `--lb-server-port` 値 | `K3S_LB_SERVER_PORT` | スーパーバイザ クライアント ロード バランサのローカル ポート。 スーパーバイザーと apiserver が同じ場所に配置されていない場合、このポートよりも 1 つ小さい追加のポートが apiserver クライアント ロード バランサーにも使用されます。 (デフォルト: 6444) |

### ランタイム

| | フラグ | デフォルト | 説明 |
| | ------------------------------------ | ---------------------------------- | -------------------------------------------------- ---------------- |
| | `--container-runtime-endpoint` 値 | 該当なし | 組み込みの containerd を無効にし、指定されたパスで CRI ソケットを使用します。 --docker とともに使用すると、cri-docker ソケット パスが設定されます。
| | --pause-image 値 | "docker.io/rancher/pause:3.1" | containerd または docker サンドボックス用にカスタマイズされた一時停止イメージ |
| | `--private-registry` 値 | "/etc/rancher/k3s/registries.yaml" | プライベート レジストリ構成ファイル |

### ネットワーキング

| | フラグ | 環境変数 | 説明 |
| | --------------------------- | -------------------- | ---------------------------------------------- |
| | `--node-ip 値, -i` 値 | 該当なし | ノードのアドバタイズする IP アドレス |
| | `--node-external-ip` 値 | 該当なし | ノードにアドバタイズする外部 IP アドレス |
| | `--resolv-conf` 値 | `K3S_RESOLV_CONF` | Kubelet resolv.conf ファイル |
| | `--flannel-iface` 値 | 該当なし | デフォルトのフランネル インターフェイスをオーバーライドする |
| | `--flannel-conf` 値 | 該当なし | デフォルトの flannel 設定ファイルをオーバーライド |
| | `--flannel-cni-conf` 値 | 該当なし | デフォルトの flannel cni 設定ファイルをオーバーライド |

### カスタマイズされたフラグ

| | フラグ | 説明 |
| | ------------------------ | -------------------------------------- |
| | `--kubelet-arg` 値 | kubelet プロセスのカスタマイズされたフラグ |
| | `--kube-proxy-arg` 値 | kube-proxy プロセス用にカスタマイズされたフラグ |

### 実験的

| | フラグ | 説明 |
| | ------------ | -------------------------------------- |
| | `--ルートレス` | ルートレスで実行 |
| | --docker | containerd | の代わりに cri-dockerd を使用してください。
| | `--prefer-bundled-bin` | ホスト バイナリよりもバンドルされたユーザー空間バイナリを優先する |

### 非推奨

| | フラグ | 環境変数 | 説明 |
| | ------------------------ | -------------------- | ---------------------------- |
| | --ノーフランネル 該当なし | `--flannel-backend=none` を使用 |
| | `--cluster-secret` 値 | `K3S_CLUSTER_SECRET` | --token を使用 |

### エージェントのノード ラベルとテイント

K3s エージェントは、kubelet にラベルとテイントを追加するオプション `--node-label` と `--node-taint` で構成できます。 2 つのオプションは、登録時にのみラベルやテイントを追加するため、これらは 1 回のみ追加でき、その後は K3s コマンドを実行して変更することはできません。

以下は、ラベルとテイントを追加する方法を示す例です。
```bash
     --node-label foo=bar \
     --node-label hello=world \
     --node-taint key1=value1:NoExecute
```

ノード登録後にノード ラベルとテイントを変更する場合は、「kubectl」を使用する必要があります。 [テイント](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/) と [ノード ラベル](https://kubernetes. io/docs/tasks/configure-pod-container/assign-pods-nodes/#add-a-label-to-a-node)

### K3s エージェント CLI ヘルプ

> オプションが以下の括弧内に表示されている場合、たとえば `[$K3S_URL]` は、そのオプションをその名前の環境変数として渡すことができることを意味します。
```bash
NAME:
   k3s agent - Run node agent

USAGE:
   k3s agent [OPTIONS]

OPTIONS:
   --config FILE, -c FILE                     (config) Load configuration from FILE (default: "/etc/rancher/k3s/config.yaml") [$K3S_CONFIG_FILE]
   --debug                                    (logging) Turn on debug logs [$K3S_DEBUG]
   -v value                                   (logging) Number for the log level verbosity (default: 0)
   --vmodule value                            (logging) Comma-separated list of FILE_PATTERN=LOG_LEVEL settings for file-filtered logging
   --log value, -l value                      (logging) Log to file
   --alsologtostderr                          (logging) Log to standard error as well as file (if set)
   --token value, -t value                    (cluster) Token to use for authentication [$K3S_TOKEN]
   --token-file value                         (cluster) Token file to use for authentication [$K3S_TOKEN_FILE]
   --server value, -s value                   (cluster) Server to connect to [$K3S_URL]
   --data-dir value, -d value                 (agent/data) Folder to hold state (default: "/var/lib/rancher/k3s")
   --node-name value                          (agent/node) Node name [$K3S_NODE_NAME]
   --with-node-id                             (agent/node) Append id to node name
   --node-label value                         (agent/node) Registering and starting kubelet with set of labels
   --node-taint value                         (agent/node) Registering kubelet with set of taints
   --image-credential-provider-bin-dir value  (agent/node) The path to the directory where credential provider plugin binaries are located (default: "/var/lib/rancher/credentialprovider/bin")
   --image-credential-provider-config value   (agent/node) The path to the credential provider plugin config file (default: "/var/lib/rancher/credentialprovider/config.yaml")
   --selinux                                  (agent/node) Enable SELinux in containerd [$K3S_SELINUX]
   --lb-server-port value                     (agent/node) Local port for supervisor client load-balancer. If the supervisor and apiserver are not colocated an additional port 1 less than this port will also be used for the apiserver client load-balancer. (default: 6444) [$K3S_LB_SERVER_PORT]
   --protect-kernel-defaults                  (agent/node) Kernel tuning behavior. If set, error if kernel tunables are different than kubelet defaults.
   --container-runtime-endpoint value         (agent/runtime) Disable embedded containerd and use the CRI socket at the given path; when used with --docker this sets the docker socket path
   --pause-image value                        (agent/runtime) Customized pause image for containerd or docker sandbox (default: "rancher/mirrored-pause:3.6")
   --snapshotter value                        (agent/runtime) Override default containerd snapshotter (default: "overlayfs")
   --private-registry value                   (agent/runtime) Private registry configuration file (default: "/etc/rancher/k3s/registries.yaml")
   --node-ip value, -i value                  (agent/networking) IPv4/IPv6 addresses to advertise for node
   --node-external-ip value                   (agent/networking) IPv4/IPv6 external IP addresses to advertise for node
   --resolv-conf value                        (agent/networking) Kubelet resolv.conf file [$K3S_RESOLV_CONF]
   --flannel-iface value                      (agent/networking) Override default flannel interface
   --flannel-conf value                       (agent/networking) Override default flannel config file
   --flannel-cni-conf value                   (agent/networking) Override default flannel cni config file
   --kubelet-arg value                        (agent/flags) Customized flag for kubelet process
   --kube-proxy-arg value                     (agent/flags) Customized flag for kube-proxy process
   --rootless                                 (experimental) Run rootless
   --prefer-bundled-bin                       (experimental) Prefer bundled userspace binaries over host binaries
   --docker                                   (agent/runtime) (experimental) Use cri-dockerd instead of containerd
```
