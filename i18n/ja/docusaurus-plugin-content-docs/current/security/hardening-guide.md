---
title: "CIS Hardening Guide"
weight: 80
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

このドキュメントは、K3s の実稼働インストールを強化するための規範的なガイダンスを提供します。 これは、Center for Internet Security (CIS) からの Kubernetes ベンチマーク コントロールに対処するために必要な構成とコントロールの概要を示しています。

K3s には、多数のセキュリティ緩和策が適用され、デフォルトでオンになっており、多数の Kubernetes CIS コントロールを変更せずに渡します。 これには、CIS ベンチマークに完全に準拠するために手動で介入する必要があるいくつかの注目すべき例外があります。

1. K3s はホスト オペレーティング システムを変更しません。 ホスト レベルの変更は手動で行う必要があります。
2. `NetworkPolicies` および `PodSecurityStandards` (v1.24 以前の `PodSecurityPolicies`) に対する特定の CIS ポリシー コントロールは、クラスターの機能を制限します。 適切なオプション (アドミッション プラグインの有効化) をコマンドライン フラグまたは構成ファイルに追加し、適切なポリシーを手動で適用することにより、K3s にこれらを構成させることを選択する必要があります。 詳細については、以下のセクションで説明します。

CIS ベンチマークの最初のセクション (1.1) は、主に Pod マニフェストのアクセス許可と所有権に関連しています。 すべてが単一のバイナリにパッケージ化されているため、K3s はコア コンポーネントにこれらを使用しません。

## ホストレベルの要件

ホスト レベルの要件には、カーネル パラメーターと etcd プロセス/ディレクトリ構成の 2 つの領域があります。 これらについては、このセクションで概説します。

### `protect-kernel-defaults`が設定されていることを確認してください

これは、必要なカーネル パラメータが設定されていないか、kubelet のデフォルトとは異なる値に設定されている場合に、kubelet を終了させる kubelet フラグです。

> **注:** `protect-kernel-defaults` は、K3s の最上位フラグとして公開されています。

#### カーネル パラメータを設定する

`/etc/sysctl.d/90-kubelet.conf` というファイルを作成し、以下のスニペットを追加します。 次に、「sysctl -p /etc/sysctl.d/90-kubelet.conf」を実行します。
```bash
vm.panic_on_oom=0
vm.overcommit_memory=1
kernel.panic=10
kernel.panic_on_oops=1
kernel.keys.root_maxbytes=25000000
```

このドキュメントは、K3s の実稼働インストールを強化するための規範的なガイダンスを提供します。 これは、Center for Internet Security (CIS) からの Kubernetes ベンチマーク コントロールに対処するために必要な構成とコントロールの概要を示しています。

K3s には、多数のセキュリティ緩和策が適用され、デフォルトでオンになっており、多数の Kubernetes CIS コントロールを変更せずに渡します。 これには、CIS ベンチマークに完全に準拠するために手動で介入する必要があるいくつかの注目すべき例外があります。

1. K3s はホスト オペレーティング システムを変更しません。 ホスト レベルの変更は手動で行う必要があります。
2. `NetworkPolicies` および `PodSecurityStandards` (v1.24 以前の `PodSecurityPolicies`) に対する特定の CIS ポリシー コントロールは、クラスターの機能を制限します。 適切なオプション (アドミッション プラグインの有効化) をコマンドライン フラグまたは構成ファイルに追加し、適切なポリシーを手動で適用することにより、K3s にこれらを構成させることを選択する必要があります。 詳細については、以下のセクションで説明します。

CIS ベンチマークの最初のセクション (1.1) は、主に Pod マニフェストのアクセス許可と所有権に関連しています。 すべてが単一のバイナリにパッケージ化されているため、K3s はコア コンポーネントにこれらを使用しません。

## ホストレベルの要件

ホスト レベルの要件には、カーネル パラメーターと etcd プロセス/ディレクトリ構成の 2 つの領域があります。 これらについては、このセクションで概説します。

### `protect-kernel-defaults`が設定されていることを確認してください

これは、必要なカーネル パラメータが設定されていないか、kubelet のデフォルトとは異なる値に設定されている場合に、kubelet を終了させる kubelet フラグです。

> **注:** `protect-kernel-defaults` は、K3s の最上位フラグとして公開されています。

#### カーネル パラメータを設定する

`/etc/sysctl.d/90-kubelet.conf` というファイルを作成し、以下のスニペットを追加します。 次に、「sysctl -p /etc/sysctl.d/90-kubelet.conf」を実行します。
<Tabs>
<TabItem value="v1.25以降" default>

K3s v1.25 以降では、ポッド セキュリティを制御するための [ポッド セキュリティ アドミッション (PSA)](https://kubernetes.io/docs/concepts/security/pod-security-admission/) がサポートされています。 PSA は、次のフラグを K3s サーバーに渡すことで有効になります。
```
--kube-apiserver-arg="admission-control-config-file=/var/lib/rancher/k3s/server/psa.yaml"
```

ポリシーは、「/var/lib/rancher/k3s/server」ディレクトリの「psa.yaml」という名前のファイルに書き込む必要があります。

準拠した PSA の例を次に示します。
```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: AdmissionConfiguration
plugins:
- name: PodSecurity
  configuration:
    apiVersion: pod-security.admission.config.k8s.io/v1beta1
    kind: PodSecurityConfiguration
    defaults:
      enforce: "restricted"
      enforce-version: "latest"
      audit: "restricted"
      audit-version: "latest"
      warn: "restricted"
      warn-version: "latest"
    exemptions:
      usernames: []
      runtimeClasses: []
      namespaces: [kube-system, cis-operator-system]
```
</TabItem>
<TabItem value="v1.24 and Older" default>

K3s v1.24 以前は、ポッド セキュリティを制御するための [ポッド セキュリティ ポリシー (PSP)](https://v1-24.docs.kubernetes.io/docs/concepts/security/pod-security-policy/) をサポートしています。 PSP を有効にするには、次のフラグを K3s サーバーに渡します。
```
--kube-apiserver-arg="enable-admission-plugins=NodeRestriction,PodSecurityPolicy"
```
これにより、「NodeRestriction」プラグインを維持し、「PodSecurityPolicy」を有効にする効果があります。

PSP が有効になっている場合、ポリシーを適用して、CIS ベンチマークのセクション 5.2 で説明されている必要な制御を満たすことができます。

準拠した PSP の例を次に示します。
```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted-psp
spec:
  privileged: false                # CIS - 5.2.1
  allowPrivilegeEscalation: false  # CIS - 5.2.5
  requiredDropCapabilities:        # CIS - 5.2.7/8/9
    - ALL
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'downwardAPI'
    - 'csi'
    - 'persistentVolumeClaim'
    - 'ephemeral'
  hostNetwork: false               # CIS - 5.2.4
  hostIPC: false                   # CIS - 5.2.3
  hostPID: false                   # CIS - 5.2.2
  runAsUser:
    rule: 'MustRunAsNonRoot'       # CIS - 5.2.6
  seLinux:
    rule: 'RunAsAny'
  supplementalGroups:
    rule: 'MustRunAs'
    ranges:
      - min: 1
        max: 65535
  fsGroup:
    rule: 'MustRunAs'
    ranges:
      - min: 1
        max: 65535
  readOnlyRootFilesystem: false
```

上記の PSP を有効にするには、ClusterRole と ClusterRoleBinding を作成する必要があります。 また、追加の権限を必要とするシステムレベルの Pod に必要な「system unrestricted ポリシー」と、servicelb が適切に機能するために必要な sysctl を許可する追加のポリシーを含める必要があります。

上記の構成を次のセクションで説明する [ネットワーク ポリシー](#networkpolicies) と組み合わせて、単一のファイルを `/var/lib/rancher/k3s/server/manifests` ディレクトリに配置できます。 これは `policy.yaml` ファイルの例です:
```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted-psp
spec:
  privileged: false
  allowPrivilegeEscalation: false
  requiredDropCapabilities:
    - ALL
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'downwardAPI'
    - 'csi'
    - 'persistentVolumeClaim'
    - 'ephemeral'
  hostNetwork: false
  hostIPC: false
  hostPID: false
  runAsUser:
    rule: 'MustRunAsNonRoot'
  seLinux:
    rule: 'RunAsAny'
  supplementalGroups:
    rule: 'MustRunAs'
    ranges:
      - min: 1
        max: 65535
  fsGroup:
    rule: 'MustRunAs'
    ranges:
      - min: 1
        max: 65535
  readOnlyRootFilesystem: false
---
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: system-unrestricted-psp
  annotations:
    seccomp.security.alpha.kubernetes.io/allowedProfileNames: '*'
spec:
  allowPrivilegeEscalation: true
  allowedCapabilities:
  - '*'
  fsGroup:
    rule: RunAsAny
  hostIPC: true
  hostNetwork: true
  hostPID: true
  hostPorts:
  - max: 65535
    min: 0
  privileged: true
  runAsUser:
    rule: RunAsAny
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  volumes:
  - '*'
---
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: svclb-psp
  annotations:
    seccomp.security.alpha.kubernetes.io/allowedProfileNames: '*'
spec:
  allowPrivilegeEscalation: false
  allowedCapabilities:
  - NET_ADMIN
  allowedUnsafeSysctls:
  - net.ipv4.ip_forward
  - net.ipv6.conf.all.forwarding
  fsGroup:
    rule: RunAsAny
  hostPorts:
  - max: 65535
    min: 0
  runAsUser:
    rule: RunAsAny
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: psp:restricted-psp
rules:
- apiGroups:
  - policy
  resources:
  - podsecuritypolicies
  verbs:
  - use
  resourceNames:
  - restricted-psp
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: psp:system-unrestricted-psp
rules:
- apiGroups:
  - policy
  resources:
  - podsecuritypolicies
  resourceNames:
  - system-unrestricted-psp
  verbs:
  - use
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: psp:svclb-psp
rules:
- apiGroups:
  - policy
  resources:
  - podsecuritypolicies
  resourceNames:
  - svclb-psp
  verbs:
  - use
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: default:restricted-psp
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted-psp
subjects:
- kind: Group
  name: system:authenticated
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system-unrestricted-node-psp-rolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:system-unrestricted-psp
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:nodes
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: system-unrestricted-svc-acct-psp-rolebinding
  namespace: kube-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:system-unrestricted-psp
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: svclb-psp-rolebinding
  namespace: kube-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:svclb-psp
subjects:
- kind: ServiceAccount
  name: svclb
---
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: intra-namespace
  namespace: kube-system
spec:
  podSelector: {}
  ingress:
    - from:
      - namespaceSelector:
          matchLabels:
            name: kube-system
---
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: intra-namespace
  namespace: default
spec:
  podSelector: {}
  ingress:
    - from:
      - namespaceSelector:
          matchLabels:
            name: default
---
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: intra-namespace
  namespace: kube-public
spec:
  podSelector: {}
  ingress:
    - from:
      - namespaceSelector:
          matchLabels:
            name: kube-public
```

</TabItem>
</Tabs>


> **注:** CNI、DNS、Ingress などの Kubernetes の重要な追加機能は、「kube-system」名前空間でポッドとして実行されます。 したがって、この名前空間には、これらのコンポーネントが適切に実行できるように制限の少ないポリシーが設定されます。

### ネットワークポリシー

CIS では、すべての名前空間に、名前空間とポッドへのトラフィックを合理的に制限するネットワーク ポリシーが適用されている必要があります。

ネットワーク ポリシーは、起動時に自動的に展開される「/var/lib/rancher/k3s/server/manifests」ディレクトリに配置する必要があります。

準拠したネットワーク ポリシーの例を次に示します。
```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: intra-namespace
  namespace: kube-system
spec:
  podSelector: {}
  ingress:
    - from:
      - namespaceSelector:
          matchLabels:
            name: kube-system
```

適用された制限により、意図的に許可しない限り、DNS はブロックされます。 以下は、DNS のトラフィックが存在することを許可するネットワーク ポリシーです。
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-network-dns-policy
  namespace: <NAMESPACE>
spec:
  ingress:
  - ports:
    - port: 53
      protocol: TCP
    - port: 53
      protocol: UDP
  podSelector:
    matchLabels:
      k8s-app: kube-dns
  policyTypes:
  - Ingress
```

アクセスを許可するネットワーク ポリシーが作成されていない場合、metrics-server と Traefik イングレス コントローラーはデフォルトでブロックされます。 K3s バージョン 1.20 以下にパッケージ化されている Traefik v1 は、Traefik v2 とは異なるラベルを使用します。 クラスターに存在する Traefik のバージョンに関連付けられている以下のサンプル yaml のみを使用してください。
<Tabs>
<TabItem value="v1.21以降" default>

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-metrics-server
  namespace: kube-system
spec:
  podSelector:
    matchLabels:
      k8s-app: metrics-server
  ingress:
  - {}
  policyTypes:
  - Ingress
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-svclbtraefik-ingress
  namespace: kube-system
spec:
  podSelector: 
    matchLabels:
      svccontroller.k3s.cattle.io/svcname: traefik
  ingress:
  - {}
  policyTypes:
  - Ingress
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-traefik-v121-ingress
  namespace: kube-system
spec:
  podSelector:
    matchLabels:
      app.kubernetes.io/name: traefik
  ingress:
  - {}
  policyTypes:
  - Ingress
---

```
</TabItem>

<TabItem value="v1.20以前" default>

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-metrics-server
  namespace: kube-system
spec:
  podSelector:
    matchLabels:
      k8s-app: metrics-server
  ingress:
  - {}
  policyTypes:
  - Ingress
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-svclbtraefik-ingress
  namespace: kube-system
spec:
  podSelector: 
    matchLabels:
      svccontroller.k3s.cattle.io/svcname: traefik
  ingress:
  - {}
  policyTypes:
  - Ingress
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-traefik-v120-ingress
  namespace: kube-system
spec:
  podSelector:
    matchLabels:
      app: traefik
  ingress:
  - {}
  policyTypes:
  - Ingress
---

```
</TabItem>
</Tabs>

:::info
オペレーターは、作成された追加の名前空間に対して通常どおりネットワーク ポリシーを管理する必要があります。
:::

### API サーバーの監査設定

CIS 要件 1.2.22 から 1.2.25 は、API サーバーの監査ログの構成に関連しています。 監査要件は各ユーザーのポリシーと環境に固有であるため、K3s は既定ではログ ディレクトリと監査ポリシーを作成しません。

ログ ディレクトリは、理想的には、K3s を起動する前に作成する必要があります。 潜在的な機密情報の漏えいを避けるために、アクセス許可を制限することをお勧めします。
```bash
sudo mkdir -p -m 700 /var/lib/rancher/k3s/server/logs
```

リクエストのメタデータをログに記録するスターター監査ポリシーを以下に示します。 ポリシーは、「/var/lib/rancher/k3s/server」ディレクトリの「audit.yaml」という名前のファイルに書き込む必要があります。 API サーバーのポリシー構成に関する詳細情報は、Kubernetes の [ドキュメント](https://kubernetes.io/docs/tasks/debug-application-cluster/audit/) に記載されています。
```yaml
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
- level: Metadata
```

両方の構成を次のように引数として API サーバーに渡す必要があります。
```bash
--kube-apiserver-arg='audit-log-path=/var/lib/rancher/k3s/server/logs/audit.log'
--kube-apiserver-arg='audit-policy-file=/var/lib/rancher/k3s/server/audit.yaml'
```

K3s のインストール後に構成を作成する場合は、それらを「/etc/systemd/system/k3s.service」で K3s の systemd サービスに追加する必要があります。
```bash
ExecStart=/usr/local/bin/k3s \
    server \
	'--kube-apiserver-arg=audit-log-path=/var/lib/rancher/k3s/server/logs/audit.log' \
	'--kube-apiserver-arg=audit-policy-file=/var/lib/rancher/k3s/server/audit.yaml' \
```

新しい構成をロードするには、K3 を再起動する必要があります。
```bash
sudo systemctl daemon-reload
sudo systemctl restart k3s.service
```

## Kubernetes コンポーネントの構成


以下の構成は [構成ファイル](../installation/configuration.md#configuration-file) に配置する必要があり、Kubernetes コンポーネントを強化するために必要なすべての修正が含まれています。

<Tabs>
<TabItem value="v1.25以降" default>

```yaml
protect-kernel-defaults: true
secrets-encryption: true
kube-apiserver-arg:
  - 'admission-control-config-file=/var/lib/rancher/k3s/server/psa.yaml'
  - 'audit-log-path=/var/lib/rancher/k3s/server/logs/audit.log'
  - 'audit-policy-file=/var/lib/rancher/k3s/server/audit.yaml'
  - 'audit-log-maxage=30'
  - 'audit-log-maxbackup=10'
  - 'audit-log-maxsize=100'
  - 'request-timeout=300s'
  - 'service-account-lookup=true'
kube-controller-manager-arg:
  - 'terminated-pod-gc-threshold=10'
  - 'use-service-account-credentials=true'
kubelet-arg:
  - 'streaming-connection-idle-timeout=5m'
  - 'make-iptables-util-chains=true'
```

</TabItem>

<TabItem value="v1.24 以前" default>

```yaml
protect-kernel-defaults: true
secrets-encryption: true
kube-apiserver-arg:
  - 'enable-admission-plugins=NodeRestriction,PodSecurityPolicy,NamespaceLifecycle,ServiceAccount'
  - 'audit-log-path=/var/lib/rancher/k3s/server/logs/audit.log'
  - 'audit-policy-file=/var/lib/rancher/k3s/server/audit.yaml'
  - 'audit-log-maxage=30'
  - 'audit-log-maxbackup=10'
  - 'audit-log-maxsize=100'
  - 'request-timeout=300s'
  - 'service-account-lookup=true'
kube-controller-manager-arg:
  - 'terminated-pod-gc-threshold=10'
  - 'use-service-account-credentials=true'
kubelet-arg:
  - 'streaming-connection-idle-timeout=5m'
  - 'make-iptables-util-chains=true'
```

</TabItem>
</Tabs>


## コントロール プレーンの実行と引数

以下にリストされているのは、K3s コントロール プレーン コンポーネントと、それらがデフォルトで開始時に与えられる引数です。 それらの右側にコメントされているのは、それらが満たす CIS 1.6 コントロールです。
```bash
kube-apiserver 
    --advertise-port=6443 
    --allow-privileged=true 
    --anonymous-auth=false                                                            # 1.2.1
    --api-audiences=unknown 
    --authorization-mode=Node,RBAC 
    --bind-address=127.0.0.1 
    --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs
    --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt                    # 1.2.31
    --enable-admission-plugins=NodeRestriction,PodSecurityPolicy                      # 1.2.17
    --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt                  # 1.2.32
    --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt                   # 1.2.29
    --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key                    # 1.2.29
    --etcd-servers=https://127.0.0.1:2379 
    --insecure-port=0                                                                 # 1.2.19
    --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt 
    --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt 
    --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key 
    --profiling=false                                                                 # 1.2.21
    --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt 
    --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key 
    --requestheader-allowed-names=system:auth-proxy 
    --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt 
    --requestheader-extra-headers-prefix=X-Remote-Extra- 
    --requestheader-group-headers=X-Remote-Group 
    --requestheader-username-headers=X-Remote-User 
    --secure-port=6444                                                                # 1.2.20
    --service-account-issuer=k3s 
    --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key            # 1.2.28
    --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key 
    --service-cluster-ip-range=10.43.0.0/16 
    --storage-backend=etcd3 
    --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt        # 1.2.30
    --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key # 1.2.30
    --tls-cipher-suites=TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305,TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305
```

```bash
kube-controller-manager 
    --address=127.0.0.1 
    --allocate-node-cidrs=true 
    --bind-address=127.0.0.1                                                       # 1.3.7
    --cluster-cidr=10.42.0.0/16 
    --cluster-signing-cert-file=/var/lib/rancher/k3s/server/tls/client-ca.crt 
    --cluster-signing-key-file=/var/lib/rancher/k3s/server/tls/client-ca.key 
    --kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig 
    --port=10252 
    --profiling=false                                                              # 1.3.2
    --root-ca-file=/var/lib/rancher/k3s/server/tls/server-ca.crt                   # 1.3.5
    --secure-port=0 
    --service-account-private-key-file=/var/lib/rancher/k3s/server/tls/service.key # 1.3.4 
    --use-service-account-credentials=true                                         # 1.3.3
```

```bash
kube-scheduler 
    --address=127.0.0.1 
    --bind-address=127.0.0.1                                              # 1.4.2
    --kubeconfig=/var/lib/rancher/k3s/server/cred/scheduler.kubeconfig 
    --port=10251 
    --profiling=false                                                     # 1.4.1
    --secure-port=0
```

```bash
kubelet 
    --address=0.0.0.0 
    --anonymous-auth=false                                                # 4.2.1
    --authentication-token-webhook=true 
    --authorization-mode=Webhook                                          # 4.2.2
    --cgroup-driver=cgroupfs 
    --client-ca-file=/var/lib/rancher/k3s/agent/client-ca.crt             # 4.2.3
    --cloud-provider=external 
    --cluster-dns=10.43.0.10 
    --cluster-domain=cluster.local 
    --cni-bin-dir=/var/lib/rancher/k3s/data/223e6420f8db0d8828a8f5ed3c44489bb8eb47aa71485404f8af8c462a29bea3/bin 
    --cni-conf-dir=/var/lib/rancher/k3s/agent/etc/cni/net.d 
    --container-runtime-endpoint=/run/k3s/containerd/containerd.sock 
    --container-runtime=remote 
    --containerd=/run/k3s/containerd/containerd.sock 
    --eviction-hard=imagefs.available<5%,nodefs.available<5% 
    --eviction-minimum-reclaim=imagefs.available=10%,nodefs.available=10% 
    --fail-swap-on=false 
    --healthz-bind-address=127.0.0.1 
    --hostname-override=hostname01 
    --kubeconfig=/var/lib/rancher/k3s/agent/kubelet.kubeconfig 
    --kubelet-cgroups=/systemd/system.slice 
    --node-labels= 
    --pod-manifest-path=/var/lib/rancher/k3s/agent/pod-manifests 
    --protect-kernel-defaults=true                                        # 4.2.6
    --read-only-port=0                                                    # 4.2.4
    --resolv-conf=/run/systemd/resolve/resolv.conf 
    --runtime-cgroups=/systemd/system.slice 
    --serialize-image-pulls=false 
    --tls-cert-file=/var/lib/rancher/k3s/agent/serving-kubelet.crt        # 4.2.10
    --tls-private-key-file=/var/lib/rancher/k3s/agent/serving-kubelet.key # 4.2.10
```
CIS 要件 1.2.22 から 1.2.25 に関する追加情報を以下に示します。

＃＃ 既知の問題点
以下は、K3s が現在デフォルトで渡さないコントロールです。 それぞれのギャップについて説明し、オペレーターの手動介入によってそれを通過できるかどうか、または K3s の将来のリリースで対処されるかどうかを明確にするメモを付けます。

### コントロール 1.2.15
アドミッション コントロール プラグイン「NamespaceLifecycle」が設定されていることを確認します。
<details>
<summary>根拠</summary>
アドミッション コントロール ポリシーを「NamespaceLifecycle」に設定すると、存在しない名前空間でオブジェクトを作成できなくなり、終了中の名前空間が新しいオブジェクトの作成に使用されなくなります。 これは、名前空間の終了プロセスの整合性を確保し、新しいオブジェクトを利用できるようにするために推奨されます。

これは、この引数を値として「enable-admission-plugins=」に渡し、それを「k3s サーバー」の「--kube-apiserver-arg=」引数に渡すことで修正できます。 以下に例を示します。
</details>

### コントロール 1.2.16
アドミッション コントロール プラグイン「PodSecurityPolicy」が設定されていることを確認します。
<details>
<summary>根拠</summary>
Pod セキュリティ ポリシーは、Pod が実行できるアクションと、Pod がアクセスできるものを制御するクラスター レベルのリソースです。 「PodSecurityPolicy」オブジェクトは、Pod がシステムに受け入れられるために実行する必要がある一連の条件を定義します。 Pod セキュリティ ポリシーは、Pod がアクセスできるセキュリティ機能を制御する設定と戦略で構成されているため、これを使用して Pod のアクセス許可を制御する必要があります。

これは、この引数を値として「enable-admission-plugins=」に渡し、それを「k3s サーバー」の「--kube-apiserver-arg=」引数に渡すことで修正できます。 以下に例を示します。
</details>

### コントロール 1.2.22
`--audit-log-path` 引数が設定されていることを確認してください。
<details>
<summary>根拠</summary>
Kubernetes API サーバーを監査すると、個々のユーザー、管理者、またはシステムの他のコンポーネントによってシステムに影響を与えた一連のアクティビティを文書化する、セキュリティ関連の時系列の一連の記録が提供されます。 現在、Kubernetes は基本的な監査機能しか提供していませんが、有効にする必要があります。 適切な監査ログ パスを設定することで有効にできます。

これは、この引数を値として「k3s サーバー」の「--kube-apiserver-arg=」引数に渡すことで修正できます。 以下に例を示します。
</details>

### コントロール 1.2.23
`--audit-log-maxage` 引数が 30 または適切に設定されていることを確認します。
<details>
<summary>根拠</summary>
少なくとも 30 日間ログを保持することで、時間をさかのぼってイベントを調査または関連付けることができます。 監査ログの保持期間を 30 日に設定するか、ビジネス要件に従ってください。

これは、この引数を値として「k3s サーバー」の「--kube-apiserver-arg=」引数に渡すことで修正できます。 以下に例を示します。
</details>

### コントロール 1.2.24
`--audit-log-maxbackup` 引数が 10 または適切に設定されていることを確認します。
<details>
<summary>根拠</summary>
Kubernetes は、ログ ファイルを自動的にローテーションします。 古いログ ファイルを保持することで、調査や関連付けを実行するために十分なログ データを利用できるようになります。 たとえば、ファイル サイズを 100 MB に設定し、保持する古いログ ファイルの数を 10 に設定した場合、分析に使用できるログ データはおよそ 1 GB になります。

これは、この引数を値として「k3s サーバー」の「--kube-apiserver-arg=」引数に渡すことで修正できます。 以下に例を示します。
</details>

### コントロール 1.2.25
`--audit-log-maxsize` 引数が 100 または適切に設定されていることを確認します。
<details>
<summary>根拠</summary>
Kubernetes は、ログ ファイルを自動的にローテーションします。 古いログ ファイルを保持することで、調査や関連付けを実行するために十分なログ データを利用できるようになります。 ファイル サイズを 100 MB に設定し、保持する古いログ ファイルの数を 10 に設定した場合、分析に使用できるログ データはおよそ 1 GB になります。

これは、この引数を値として「k3s サーバー」の「--kube-apiserver-arg=」引数に渡すことで修正できます。 以下に例を示します。
</details>

### コントロール 1.2.26
`--request-timeout` 引数が適切に設定されていることを確認してください。
<details>
<summary>根拠</summary>
グローバル リクエスト タイムアウトを設定すると、API サーバーのリクエスト タイムアウト制限を、ユーザーの接続速度に適した期間まで延長できます。 デフォルトでは 60 秒に設定されていますが、低速の接続では問題が発生する可能性があり、リクエストのデータ ボリュームが 60 秒で送信できる量を超えると、クラスター リソースにアクセスできなくなります。 ただし、このタイムアウト制限を大きく設定しすぎると、API サーバーのリソースが使い果たされ、サービス拒否攻撃を受けやすくなります。 したがって、この制限を適切に設定し、必要な場合にのみデフォルトの制限である 60 秒を変更することをお勧めします。

これは、この引数を値として「k3s サーバー」の「--kube-apiserver-arg=」引数に渡すことで修正できます。 以下に例を示します。
</details>

### コントロール 1.2.27
`--service-account-lookup` 引数が true に設定されていることを確認してください。
<details>
<summary>根拠</summary>
「--service-account-lookup」が有効になっていない場合、apiserver は認証トークンが有効であることのみを検証し、リクエストで言及されたサービス アカウント トークンが実際に etcd に存在することを検証しません。 これにより、対応するサービス アカウントが削除された後でも、サービス アカウント トークンを使用できます。 これは、チェック時から使用時までのセキュリティ問題の例です。

これは、この引数を値として「k3s サーバー」の「--kube-apiserver-arg=」引数に渡すことで修正できます。 以下に例を示します。
</details>

### コントロール 1.2.33
`--encryption-provider-config` 引数が適切に設定されていることを確認してください。
<details>
<summary>根拠</summary>
「etcd」は、すべての REST API オブジェクトの永続ストレージのために Kubernetes デプロイメントで使用される高可用性キー値ストアです。 これらのオブジェクトは本質的に機密性が高く、開示を避けるために暗号化する必要があります。

K3s でシークレットの暗号化を構成する方法の詳細な手順は、[シークレットの暗号化](secrets-encryption.md) で入手できます。
</details>

### コントロール 1.2.34
暗号化プロバイダーが適切に構成されていることを確認してください。
<details>
<summary>根拠</summary>
「etcd」暗号化が使用されている場合、適切な暗号化プロバイダーのセットが使用されていることを確認することが重要です。 現在、「aescbc」、「kms」、および「secretbox」が適切なオプションである可能性があります。

これは、上記のように有効な構成を「k3s」に渡すことで修正できます。 K3s でシークレットの暗号化を構成する方法の詳細な手順は、[シークレットの暗号化](secrets-encryption.md) で入手できます。
</details>

### コントロール 1.3.1
`--terminated-pod-gc-threshold` 引数が適切に設定されていることを確認してください。
<details>
<summary>根拠</summary>
ガベージ コレクションは、十分なリソースの可用性を確保し、パフォーマンスと可用性の低下を回避するために重要です。 最悪の場合、システムがクラッシュするか、長期間使用できなくなる可能性があります。 ガベージ コレクションの現在の設定は 12,500 の終了 Pod であり、システムが維持するには高すぎる可能性があります。 システム リソースとテストに基づいて、ガベージ コレクションをアクティブにする適切なしきい値を選択します。

これは、この引数を値として「k3s サーバー」の「--kube-apiserver-arg=」引数に渡すことで修正できます。 以下に例を示します。
</details>

### コントロール 3.2.1
最小限の監査ポリシーが作成されていることを確認します。
<details>
<summary>根拠</summary>
ロギングは、潜在的な不正アクセスを検出するために、すべてのシステムにとって重要な検出制御です。

これは、コントロール 1.2.22 ～ 1.2.25 を渡し、その有効性を検証することで修正できます。
</details>

### コントロール 4.2.7
`--make-iptables-util-chains` 引数が true に設定されていることを確認してください。
<details>
<summary>根拠</summary>
Kubelets は、ポッドのネットワーク オプションの選択方法に基づいて、iptables に必要な変更を自動的に管理できます。 kubelets に iptables への変更を管理させることをお勧めします。 これにより、iptables 構成が Pod ネットワーク構成と確実に同期されます。 動的ポッド ネットワーク構成の変更を使用して iptables を手動で構成すると、ポッド/コンテナー間および外部との通信が妨げられる可能性があります。 iptables ルールの制限が厳しすぎるか、またはオープンすぎる可能性があります。

これは、この引数を値として「k3s サーバー」の「--kube-apiserver-arg=」引数に渡すことで修正できます。 以下に例を示します。
</details>

### コントロール 5.1.5
デフォルトのサービス アカウントが積極的に使用されていないことを確認する
<details>
<summary>根拠</summary>
Kubernetes は、特定のサービス アカウントがポッドに割り当てられていないクラスター ワークロードによって使用される「デフォルト」サービス アカウントを提供します。

ポッドから Kubernetes API へのアクセスが必要な場合は、そのポッド用に特定のサービス アカウントを作成し、そのサービス アカウントに権限を付与する必要があります。

既定のサービス アカウントは、サービス アカウント トークンを提供せず、明示的な権利の割り当てを持たないように構成する必要があります。

これは、各名前空間の「デフォルト」サービス アカウントの「automountServiceAccountToken」フィールドを「false」に更新することで修正できます。

組み込みの名前空間 (「kube-system」、「kube-public」、「kube-node-lease」、および「default」) の「default」サービス アカウントの場合、K3s はこれを自動的に行いません。 これらのサービス アカウントのこのフィールドを手動で更新して、コントロールを渡すことができます。
</details>

＃＃ 結論

このガイドに従った場合、K3s クラスターは CIS Kubernetes Benchmark に準拠するように構成されます。 [CIS Benchmark Self-Assessment Guide](self-assessment.md) を確認して、ベンチマークの各チェックの期待値と、クラスターで同じことを行う方法を理解できます。