---
title: K3s サーバ設定
weight: 1
---

このセクションでは、K3s サーバーを構成する方法を学習します。

- [よく使うオプション](#よく使うオプション)
   - [データベース](#データベース)
   - [クラスタ オプション](#cluster-options)
   - [クライアント オプション](#client-options)
- [エージェント オプション](#agent-options)
   - [エージェント ノード](#agent-nodes)
   - [エージェント ランタイム](#agent-runtime)
   - [エージェント ネットワーク](#agent-networking)
- [高度なオプション](#advanced-options)
   - [ロギング](#logging)
   - [リスナー](#listeners)
   - [データ](#データ)
   - [ネットワーキング](#networking)
   - [ストレージ クラス](#ストレージ クラス)
   - [Kubernetes コンポーネント](#kubernetes-components)
   - [Kubernetes プロセスのカスタマイズされたフラグ](#customized-flags-for-kubernetes-processes)
   - [実験的オプション](#experimental-options)
   - [非推奨オプション](#deprecated-options)
- [K3s サーバー CLI ヘルプ](#k3s-server-cli-help)

## よく使われるオプション

### データベース

| | フラグ | 環境変数 | 説明 |
| | -------------------------------------- | -------------------------- | -------------------------------------------------- -------------------------------------------------- ---- |
| | `--datastore-endpoint` 値 | `K3S_DATASTORE_ENDPOINT` | etcd、Mysql、Postgres、または Sqlite (デフォルト) のデータ ソース名を指定 |
| | `--datastore-cafile` 値 | `K3S_DATASTORE_CAFILE` | データストアのバックエンド通信を保護するために使用される TLS 認証局ファイル |
| | `--datastore-certfile` 値 | `K3S_DATASTORE_CERTFILE` | データストアのバックエンド通信を保護するために使用される TLS 証明書ファイル |
| | `--datastore-keyfile` 値 | `K3S_DATASTORE_KEYFILE` | データストアのバックエンド通信を保護するために使用される TLS キー ファイル |
| | `--etcd-expose-metrics` | 該当なし | etcd メトリクスをクライアント インターフェイスに公開する (デフォルト: false) |
| | `--etcd-disable-snapshots` | 該当なし | 自動 etcd スナップショットを無効にする |
| | `--etcd-snapshot-name` 値 | 該当なし | etcd スナップショットのベース名を設定します。 デフォルト: etcd-snapshot-`<unix-timestamp>` (デフォルト:"etcd-snapshot") |
| | `--etcd-snapshot-schedule-cron` 値 | 該当なし | cron 仕様のスナップショット間隔時間。 例えば。 5 時間ごと '0 */5 _ \* _' (デフォルト: "0 */12 \* \* \*") |
| | `--etcd-snapshot-retention` 値 | 該当なし | 保持するスナップショットの数 (デフォルト: 5) |
| | `--etcd-snapshot-dir` 値 | 該当なし | db スナップショットを保存するディレクトリ (デフォルト: ${data-dir}/db/snapshots) |
| | `--etcd-s3` | 該当なし | S3 へのバックアップを有効にする |
| | `--etcd-s3-endpoint` 値 | 該当なし | S3 エンドポイント URL (デフォルト: "s3.amazonaws.com") |
| | `--etcd-s3-endpoint-ca` 値 | 該当なし | S3 エンドポイントに接続するための S3 カスタム CA 証明書 |
| | `--etcd-s3-skip-ssl-verify` | 該当なし | S3 SSL 証明書の検証を無効にします |
| | `--etcd-s3-access-key` 値 | `AWS_ACCESS_KEY_ID` | S3 アクセス キー |
| | `--etcd-s3-secret-key` 値 | `AWS_SECRET_ACCESS_KEY` | S3 秘密鍵 |
| | `--etcd-s3-bucket` 値 | 該当なし | S3 バケット名 |
| | `--etcd-s3-region` 値 | 該当なし | S3 リージョン / バケットの場所 (オプション) (デフォルト: "us-east-1") |
| | `--etcd-s3-folder` 値 | 該当なし | S3 フォルダー |
| | `--etcd-s3-insecure` | HTTPS 経由の S3 を無効にします |
| | `--etcd-s3-timeout` 値 | S3 タイムアウト (デフォルト: 5m0s) |

### クラスタ オプション

| | フラグ | 環境変数 | 説明 |
| | ------------------------- | -------------------- | -------------------------------------------------- -------- |
| | `--token` 値、`-t` 値 | `K3S_TOKEN` | サーバーまたはエージェントをクラスターに参加させるために使用される共有シークレット |
| | --token-file 値 | `K3S_TOKEN_FILE` | cluster-secret/token | を含むファイル
| | `--agent-token` 値 |`K3S_AGENT_TOKEN` | サーバーではなく、エージェントをクラスタに参加させるために使用される共有シークレット
| | `--agent-token-file` 値 |`K3S_AGENT_TOKEN_FILE` | エージェントの秘密を含むファイル
| | `--server` 値 | `K3S_URL` | クラスタに参加するために使用される、接続先のサーバー
| | --cluster-init | `K3S_CLUSTER_INIT` | 組み込みの Etcd を使用して新しいクラスターを初期化する
| | `--cluster-reset` | `K3S_CLUSTER_RESET` | すべてのピアを忘れて、新しいクラスターの唯一のメンバーになる

### クライアントオプション

| | フラグ | 環境変数 | 説明 |
|-----|----------------------|-------------|
| | `--write-kubeconfig 値、-o` 値 | `K3S_KUBECONFIG_OUTPUT` | 管理クライアントの kubeconfig をこのファイルに書き込みます |
| | `--write-kubeconfig-mode` 値 | `K3S_KUBECONFIG_MODE` | この [モード] で kubeconfig を書き込みます。(https://en.wikipedia.org/wiki/Chmod) kubeconfig ファイルへの書き込みを許可するオプションは、K3s クラスターを Rancher にインポートできるようにするのに役立ちます。 値の例は 644 です。

## エージェント オプション

サーバーにはエージェント プロセスが組み込まれているため、K3s エージェント オプションはサーバー オプションとして使用できます。

### エージェントノード

| | フラグ | 環境変数 | 説明 |
|-----|----------------------|-------------|
| | --node-name 値 | `K3S_NODE_NAME` | ノード名 |
| | `--with-node-id` | 該当なし | ノード名に id を追加 | (エージェント/ノード)
| | --node-label 値 | 該当なし | 一連のラベルを使用した kubelet の登録と開始 |
| | --node-taint 値 | 該当なし | taint のセットで kubelet を登録する |
| | `--image-credential-provider-bin-dir` 値 | 該当なし | 資格情報プロバイダー プラグイン バイナリが配置されているディレクトリへのパス (デフォルト: "/var/lib/rancher/credentialprovider/bin") |
| | `--image-credential-provider-config` 値 | 該当なし | 資格情報プロバイダー プラグイン構成ファイルへのパス (デフォルト: "/var/lib/rancher/credentialprovider/config.yaml") |
| | `--selinux` | `K3S_SELINUX` | containerd で SELinux を有効にする |
| | `--lb-server-port` 値 | `K3S_LB_SERVER_PORT` | スーパーバイザ クライアント ロード バランサのローカル ポート。 スーパーバイザーと apiserver が同じ場所に配置されていない場合、このポートよりも 1 つ小さい追加のポートが apiserver クライアント ロード バランサーにも使用されます。 (デフォルト: 6444) |
| | `--protect-kernel-defaults` | 該当なし | カーネル チューニングの動作。 設定されている場合、カーネル調整可能変数が kubelet のデフォルトと異なる場合にエラーが発生します。 | |

### エージェント ランタイム

| | フラグ | デフォルト | 説明 |
| | ------------------------------------ | ---------------------------------- | -------------------------------------------------- ---------------- |
| | `--container-runtime-endpoint` 値 | 該当なし | 組み込みの containerd を無効にし、指定されたパスで CRI ソケットを使用します。 --docker とともに使用すると、cri-docker ソケット パスが設定されます。
| | --pause-image 値 | "docker.io/rancher/pause:3.1" | containerd または Docker サンドボックス用にカスタマイズされた一時停止イメージ |
| | `--snapshotter` 値 | "overlayfs" | デフォルトの containerd スナップショットをオーバーライド |
| | `--private-registry` 値 | "/etc/rancher/k3s/registries.yaml" | プライベート レジストリ構成ファイル |
| | `system-default-registry` 値 | すべてのシステム イメージにプライベート レジストリを使用 |
### エージェントネットワーキング

サーバーにはエージェントプロセスが埋め込まれているため、エージェントオプションがあります

| | フラグ | 環境変数 | 説明 |
| | --------------------------- | -------------------- | ---------------------------------------------- |
| | `--node-ip 値, -i` 値 | 該当なし | ノードのアドバタイズする IP アドレス |
| | `--node-external-ip` 値 | 該当なし | ノードにアドバタイズする外部 IP アドレス |
| | `--resolv-conf` 値 | `K3S_RESOLV_CONF` | Kubelet resolv.conf ファイル |
| | `--flannel-iface` 値 | 該当なし | デフォルトのフランネル インターフェイスをオーバーライドする |
| | `--flannel-conf` 値 | 該当なし | デフォルトの flannel 設定ファイルをオーバーライド |
| | `--flannel-cni-conf` 値 | 該当なし | デフォルトの flannel cni 設定ファイルをオーバーライド |


＃＃ 高度なオプション

### ロギング

| | フラグ | デフォルト | 説明 |
| | ------------------------------------ | -------- | -------------------------------------------------- ------------------------------- |
| | --debug | 該当なし | デバッグ ログを有効にする |
| | `-v` 値 | 0 | ログ レベルの詳細度の数値 |
| | `--vmodule` 値 | 該当なし | ファイルフィルタリングされたロギングの FILE_PATTERN=LOG_LEVEL 設定のコンマ区切りリスト |
| | `--log 値、-l` 値 | 該当なし | ファイルにログ |
| | `--またlogtostderr` | 該当なし | 標準エラーとファイル (設定されている場合) にログを記録 |

### リスナー

| | フラグ | デフォルト | 説明 |
| | --------------------------- | ------------------------ | -------------------------------------------------- ---------------------------------------------- |
| | `--bind-address` 値 | 0.0.0.0 | k3s バインド アドレス |
| | `--https-listen-port` 値 | 6443 | HTTPS リッスン ポート |
| | `--advertise-address` 値 | ノード外部 ip/ノード ip | apiserver がクラスターのメンバーにアドバタイズするために使用する IPv4 アドレス |
| | `--advertise-port` 値 | リッスン ポート/0 | apiserver がクラスターのメンバーにアドバタイズするために使用するポート |
| | `--tls-san` 値 | 該当なし | TLS 証明書のサブジェクト代替名として追加のホスト名または IPv4/IPv6 アドレスを追加します。

＃＃＃ データ

| | フラグ | デフォルト | 説明 |
| | ---------------------------- | -------------------------------------------------- ---------- | -------------------- |
| | `--data-dir 値、-d` 値 | `/var/lib/rancher/k3s` または `${HOME}/.rancher/k3s` root でない場合 | 状態を保持するフォルダー |

### 秘密の暗号化

| | フラグ | デフォルト | 説明 |
| | ---------------------- | -------- | -------------------------------- |
| | `--secrets-encryption` | 偽 | 保管時のシークレット暗号化を有効にする |
### ネットワーキング

| | フラグ | デフォルト | 説明 |
| | --------------------------------- | --------------- | -------------------------------------------------- -------------------------------------------- |
| | `--cluster-cidr` 値 | "10.42.0.0/16" | ポッド IP に使用する IPv4/IPv6 ネットワーク CIDR |
| | --service-cidr 値 | "10.43.0.0/16" | サービス IP に使用する IPv4/IPv6 ネットワーク CIDR |
| | `--service-node-port-range` 値 | "30000-32767" | NodePort の可視性を備えたサービス用に予約するポート範囲 |
| | `--cluster-dns` 値 | "10.43.0.10" | coredns サービスの IPv4 クラスター IP。 service-cidr 範囲内にある必要があります |
| | `--cluster-domain` 値 | "cluster.local" | クラスター ドメイン |
| | `--flannel-backend` 値 | "vxlan" | 'none'、'vxlan'、'ipsec' (非推奨)、'host-gw'、'wireguard-native'、または 'wireguard' (非推奨) のいずれか |
| | `--flannel-ipv6-masq` | "該当なし" | ポッドの IPv6 マスカレードを有効にする |
| | `--flannel-external-ip` | "該当なし" | Flannel トラフィックにノードの外部 IP アドレスを使用する |
| | `--servicelb-namespace` 値 | "kube システム" | servicelb コンポーネントのポッドの名前空間 |
| | `--egress-selector-mode` 値 | "エージェント" | 次のいずれかである必要があります。 <ul><li>disabled: apiserver はノードとの通信にエージェント トンネルを使用しません。 サーバーがエージェントを実行し、エージェント上の kubelet に直接接続する必要があります。そうしないと、apiserver が機能してサービス エンドポイントにアクセスしたり、kubectl exec および kubectl ログを実行したりできなくなります。</li><li>agent: apiserver はエージェント トンネルを使用します。 ノードと通信します。 ノードは、ループバック アドレスからのトンネル接続を許可します。 サーバーもエージェントを実行する必要があります。そうしないと、apiserver がサービス エンドポイントにアクセスできなくなります。 k3s の歴史的なデフォルト。</li><li> pod: apiserver はエージェント トンネルを使用してノードおよびサービス エンドポイントと通信し、ノードを監視してエンドポイント接続を正しいエージェントにルーティングします。 ノードは、ループバック アドレス、またはノードに割り当てられた CIDR からのトンネル接続を許可します。</li><li> クラスター: apiserver は、エージェント トンネルを使用してノードおよびサービス エンドポイントと通信し、エンドポイントを監視することによってエンドポイント接続を正しいエージェントにルーティングします。 ノードは、ループバック アドレスまたは構成されたクラスター CIDR 範囲からのトンネル接続を許可します。</li></ul> |


### ストレージ クラス

| | フラグ | 説明 |
| | ------------------------------------ | -------------------------------------------------- ------------ |
| | `--default-local-storage-path` 値 | ローカル プロビジョナー ストレージ クラスのデフォルトのローカル ストレージ パス |

### Kubernetes コンポーネント

| | フラグ | 説明 |
| | ---------------------------- | -------------------------------------------------- -------------------------------------------------- ---------------------------------------------- |
| | --disable 値 | パッケージ化されたコンポーネントを展開せず、展開されたコンポーネントを削除しないでください (有効な項目: coredns、servicelb、traefik、local-storage、metrics-server) |
| | --disable-scheduler | Kubernetes のデフォルト スケジューラを無効にする |
| | --disable-cloud-controller` | k3s のデフォルトのクラウド コントローラー マネージャーを無効にする |
| | `--disable-kube-proxy` | 実行中の kube-proxy を無効にする |
| | `--disable-network-policy` | k3s のデフォルトのネットワーク ポリシー コントローラーを無効にする |
| | --disable-helm-controller` | Helm コントローラーを無効にする |

### Kubernetes プロセス用にカスタマイズされたフラグ

| | フラグ | 説明 |
| | ----------------------------------------------- | -------------------------------------------------- -------- |
| | `--etcd-arg` 値 | etcd プロセスのカスタマイズされたフラグ |
| | `--kube-apiserver-arg` 値 | kube-apiserver プロセスのカスタマイズされたフラグ |
| | `--kube-scheduler-arg` 値 | kube-scheduler プロセスのカスタマイズされたフラグ |
| | `--kube-controller-manager-arg` 値 | kube-controller-manager プロセスのカスタマイズされたフラグ |
| | `--kube-cloud-controller-manager-arg` 値 | kube-cloud-controller-manager プロセスのカスタマイズされたフラグ |
| | `--kubelet-arg` 値 | kubelet プロセスのカスタマイズされたフラグ |
| | `--kube-proxy-arg` 値 | kube-proxy プロセス用にカスタマイズされたフラグ |

### 実験的オプション

| | フラグ | 説明 |
| | ---------------------- | -------------------------------------------- |
| | `--ルートレス` | ルートレスで実行 |
| | --enable-pprof | スーパーバイザ ポートで pprof エンドポイントを有効にする |
| | --docker | containerd | の代わりに cri-dockerd を使用してください。
| | `--prefer-bundled-bin` | ホスト バイナリよりもバンドルされたユーザー空間バイナリを優先する |

### 非推奨のオプション

| | フラグ | 環境変数 | 説明 |
| | ---------------------------------------------------- | -------------------- | -------------------------------------------------- -------------------------------------------------- -------- |
| | --ノーフランネル 該当なし | --flannel-backend=none | を使用します。
| | --no-deploy 値 | 該当なし | パッケージ化されたコンポーネントをデプロイしない (有効な項目: coredns、servicelb、traefik、local-storage、metrics-server) |
| | `--cluster-secret` 値 | `K3S_CLUSTER_SECRET` | --token | を使用します。
| | `--flannel-backend` ワイヤーガード | 該当なし | --flannel-backend=wireguard-native | を使用します。
| | `--flannel-backend` value=option1=value | 該当なし | --flannel-conf を使用して、バックエンド構成で flannel 構成ファイルを指定します |

# K3s サーバー CLI ヘルプ

> オプションが以下の括弧内に表示されている場合、たとえば `[$K3S_TOKEN]` は、そのオプションをその名前の環境変数として渡すことができることを意味します。

```bash
NAME:
   k3s server - Run management server

USAGE:
   k3s server [OPTIONS]

OPTIONS:
   --config FILE, -c FILE                     (config) Load configuration from FILE (default: "/etc/rancher/k3s/config.yaml") [$K3S_CONFIG_FILE]
   --debug                                    (logging) Turn on debug logs [$K3S_DEBUG]
   -v value                                   (logging) Number for the log level verbosity (default: 0)
   --vmodule value                            (logging) Comma-separated list of FILE_PATTERN=LOG_LEVEL settings for file-filtered logging
   --log value, -l value                      (logging) Log to file
   --alsologtostderr                          (logging) Log to standard error as well as file (if set)
   --bind-address value                       (listener) k3s bind address (default: 0.0.0.0)
   --https-listen-port value                  (listener) HTTPS listen port (default: 6443)
   --advertise-address value                  (listener) IPv4 address that apiserver uses to advertise to members of the cluster (default: node-external-ip/node-ip)
   --advertise-port value                     (listener) Port that apiserver uses to advertise to members of the cluster (default: listen-port) (default: 0)
   --tls-san value                            (listener) Add additional hostnames or IPv4/IPv6 addresses as Subject Alternative Names on the server TLS cert
   --data-dir value, -d value                 (data) Folder to hold state (default: /var/lib/rancher/k3s or ${HOME}/.rancher/k3s if not root)
   --cluster-cidr value                       (networking) IPv4/IPv6 network CIDRs to use for pod IPs (default: 10.42.0.0/16)
   --service-cidr value                       (networking) IPv4/IPv6 network CIDRs to use for service IPs (default: 10.43.0.0/16)
   --service-node-port-range value            (networking) Port range to reserve for services with NodePort visibility (default: "30000-32767")
   --cluster-dns value                        (networking) IPv4 Cluster IP for coredns service. Should be in your service-cidr range (default: 10.43.0.10)
   --cluster-domain value                     (networking) Cluster Domain (default: "cluster.local")
   --flannel-backend value                    (networking) backend<=option1=val1,option2=val2> where backend is one of 'none', 'vxlan', 'ipsec' (deprecated), 'host-gw', 'wireguard-native', 'wireguard' (deprecated) (default: "vxlan")
   --flannel-ipv6-masq                        (networking) Enable IPv6 masquerading for pod
   --flannel-external-ip                      (networking) Use node external IP addresses for Flannel traffic
   --egress-selector-mode value               (networking) One of 'agent', 'cluster', 'pod', 'disabled' (default: "agent")
   --servicelb-namespace value                (networking) Namespace of the pods for the servicelb component (default: "kube-system")
   --write-kubeconfig value, -o value         (client) Write kubeconfig for admin client to this file [$K3S_KUBECONFIG_OUTPUT]
   --write-kubeconfig-mode value              (client) Write kubeconfig with this mode [$K3S_KUBECONFIG_MODE]
   --token value, -t value                    (cluster) Shared secret used to join a server or agent to a cluster [$K3S_TOKEN]
   --token-file value                         (cluster) File containing the token [$K3S_TOKEN_FILE]
   --agent-token value                        (cluster) Shared secret used to join agents to the cluster, but not servers [$K3S_AGENT_TOKEN]
   --agent-token-file value                   (cluster) File containing the agent secret [$K3S_AGENT_TOKEN_FILE]
   --server value, -s value                   (cluster) Server to connect to, used to join a cluster [$K3S_URL]
   --cluster-init                             (cluster) Initialize a new cluster using embedded Etcd [$K3S_CLUSTER_INIT]
   --cluster-reset                            (cluster) Forget all peers and become sole member of a new cluster [$K3S_CLUSTER_RESET]
   --cluster-reset-restore-path value         (db) Path to snapshot file to be restored
   --kube-apiserver-arg value                 (flags) Customized flag for kube-apiserver process
   --etcd-arg value                           (flags) Customized flag for etcd process
   --kube-controller-manager-arg value        (flags) Customized flag for kube-controller-manager process
   --kube-scheduler-arg value                 (flags) Customized flag for kube-scheduler process
   --kube-cloud-controller-manager-arg value  (flags) Customized flag for kube-cloud-controller-manager process
   --datastore-endpoint value                 (db) Specify etcd, Mysql, Postgres, or Sqlite (default) data source name [$K3S_DATASTORE_ENDPOINT]
   --datastore-cafile value                   (db) TLS Certificate Authority file used to secure datastore backend communication [$K3S_DATASTORE_CAFILE]
   --datastore-certfile value                 (db) TLS certification file used to secure datastore backend communication [$K3S_DATASTORE_CERTFILE]
   --datastore-keyfile value                  (db) TLS key file used to secure datastore backend communication [$K3S_DATASTORE_KEYFILE]
   --etcd-expose-metrics                      (db) Expose etcd metrics to client interface. (default: false)
   --etcd-disable-snapshots                   (db) Disable automatic etcd snapshots
   --etcd-snapshot-name value                 (db) Set the base name of etcd snapshots (default: etcd-snapshot-<unix-timestamp>) (default: "etcd-snapshot")
   --etcd-snapshot-schedule-cron value        (db) Snapshot interval time in cron spec. eg. every 5 hours '* */5 * * *' (default: "0 */12 * * *")
   --etcd-snapshot-retention value            (db) Number of snapshots to retain (default: 5)
   --etcd-snapshot-dir value                  (db) Directory to save db snapshots. (default: ${data-dir}/db/snapshots)
   --etcd-snapshot-compress                   (db) Compress etcd snapshot
   --etcd-s3                                  (db) Enable backup to S3
   --etcd-s3-endpoint value                   (db) S3 endpoint url (default: "s3.amazonaws.com")
   --etcd-s3-endpoint-ca value                (db) S3 custom CA cert to connect to S3 endpoint
   --etcd-s3-skip-ssl-verify                  (db) Disables S3 SSL certificate validation
   --etcd-s3-access-key value                 (db) S3 access key [$AWS_ACCESS_KEY_ID]
   --etcd-s3-secret-key value                 (db) S3 secret key [$AWS_SECRET_ACCESS_KEY]
   --etcd-s3-bucket value                     (db) S3 bucket name
   --etcd-s3-region value                     (db) S3 region / bucket location (optional) (default: "us-east-1")
   --etcd-s3-folder value                     (db) S3 folder
   --etcd-s3-insecure                         (db) Disables S3 over HTTPS
   --etcd-s3-timeout value                    (db) S3 timeout (default: 5m0s)
   --default-local-storage-path value         (storage) Default local storage path for local provisioner storage class
   --disable value                            (components) Do not deploy packaged components and delete any deployed components (valid items: coredns, servicelb, traefik, local-storage, metrics-server)
   --disable-scheduler                        (components) Disable Kubernetes default scheduler
   --disable-cloud-controller                 (components) Disable k3s default cloud controller manager
   --disable-kube-proxy                       (components) Disable running kube-proxy
   --disable-network-policy                   (components) Disable k3s default network policy controller
   --disable-helm-controller                  (components) Disable Helm controller
   --node-name value                          (agent/node) Node name [$K3S_NODE_NAME]
   --with-node-id                             (agent/node) Append id to node name
   --node-label value                         (agent/node) Registering and starting kubelet with set of labels
   --node-taint value                         (agent/node) Registering kubelet with set of taints
   --image-credential-provider-bin-dir value  (agent/node) The path to the directory where credential provider plugin binaries are located (default: "/var/lib/rancher/credentialprovider/bin")
   --image-credential-provider-config value   (agent/node) The path to the credential provider plugin config file (default: "/var/lib/rancher/credentialprovider/config.yaml")
   --docker                                   (agent/runtime) (experimental) Use cri-dockerd instead of containerd
   --container-runtime-endpoint value         (agent/runtime) Disable embedded containerd and use the CRI socket at the given path; when used with --docker this sets the docker socket path
   --pause-image value                        (agent/runtime) Customized pause image for containerd or docker sandbox (default: "rancher/mirrored-pause:3.6")
   --snapshotter value                        (agent/runtime) Override default containerd snapshotter (default: "overlayfs")
   --private-registry value                   (agent/runtime) Private registry configuration file (default: "/etc/rancher/k3s/registries.yaml")
   --system-default-registry value            (agent/runtime) Private registry to be used for all system images [$K3S_SYSTEM_DEFAULT_REGISTRY]
   --node-ip value, -i value                  (agent/networking) IPv4/IPv6 addresses to advertise for node
   --node-external-ip value                   (agent/networking) IPv4/IPv6 external IP addresses to advertise for node
   --resolv-conf value                        (agent/networking) Kubelet resolv.conf file [$K3S_RESOLV_CONF]
   --flannel-iface value                      (agent/networking) Override default flannel interface
   --flannel-conf value                       (agent/networking) Override default flannel config file
   --flannel-cni-conf value                   (agent/networking) Override default flannel cni config file
   --kubelet-arg value                        (agent/flags) Customized flag for kubelet process
   --kube-proxy-arg value                     (agent/flags) Customized flag for kube-proxy process
   --protect-kernel-defaults                  (agent/node) Kernel tuning behavior. If set, error if kernel tunables are different than kubelet defaults.
   --secrets-encryption                       Enable secret encryption at rest
   --enable-pprof                             (experimental) Enable pprof endpoint on supervisor port
   --rootless                                 (experimental) Run rootless
   --prefer-bundled-bin                       (experimental) Prefer bundled userspace binaries over host binaries
   --selinux                                  (agent/node) Enable SELinux in containerd [$K3S_SELINUX]
   --lb-server-port value                     (agent/node) Local port for supervisor client load-balancer. If the supervisor and apiserver are not colocated an additional port 1 less than this port will also be used for the apiserver client load-balancer. (default: 6444) [$K3S_LB_SERVER_PORT]
```
