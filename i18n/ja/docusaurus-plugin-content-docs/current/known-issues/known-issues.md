---
title: 既知の問題
weight: 70
---
既知の問題は定期的に更新され、次回のリリースですぐに対処されない可能性がある問題について通知するように設計されています。

### スナップドッカー

Docker で K3s を使用する場合、K3s の実行時に問題が発生することが知られているため、スナップ パッケージを介してインストールされた Docker は推奨されません。

### iptables

レガシーではなく nftables モードで iptables を実行している場合、問題が発生する可能性があります。 問題を回避するために、新しい iptables (1.6.1+ など) を使用することをお勧めします。

さらに、バージョン 1.8.0 ～ 1.8.4 には、K3 が失敗する原因となる既知の問題があります。 回避策については、[その他の OS の準備](../advanced/advanced.md#old-iptables-versions) を参照してください。

### ルートレスモード

ルートレス モードで K3 を実行することは実験的なものであり、[既知の問題] がいくつかあります。

# 強化されたクラスターを v1.24.x から v1.25.x にアップグレードする

Kubernetes は、Pod Security Standards を優先して v1.25 から PodSecurityPolicy を削除しました。 PSS の詳細については、[アップストリーム ドキュメント](https://kubernetes.io/docs/concepts/security/pod-security-standards/) を参照してください。 K3S の場合、ノードで「PodSecurityPoliciy」が構成されている場合は、いくつかの手動の手順を実行する必要があります。

1. すべてのノードで、`kube-apiserver-arg` 値を更新して、`PodSecurityPolicy` アドミッション プラグインを削除します。 代わりに次の引数値を追加します: `'admission-control-config-file=/var/lib/rancher/k3s/server/psa.yaml'` ただし、まだ K3S を再起動またはアップグレードしないでください。 以下は、ノードを強化するためのこの更新後の構成ファイルの例です。
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
2. 以下の内容で `/var/lib/rancher/k3s/server/psa.yaml` ファイルを作成します。 さらに名前空間を除外することもできます。 以下の例では、`kube-system` (必須)、`cis-operator-system` (オプションですが、Rancher を介してセキュリティ スキャンを実行する場合に役立ちます)、および `system-upgrade` ([自動アップグレード](. ./upgrades/automated.md))。
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
      namespaces: [kube-system, cis-operator-system, system-upgrade]
```
3. 通常どおりアップグレードを実行します。 [自動アップグレード](../upgrades/automated.md) を実行する場合は、`system-upgrade-controller` Pod が実行されている名前空間が [Pod セキュリティ レベル](https ://kubernetes.io/docs/concepts/security/pod-security-admission/#pod-security-levels):
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: system-upgrade
  labels:
    # This value must be privileged for the controller to run successfully.
    pod-security.kubernetes.io/enforce: privileged
    pod-security.kubernetes.io/enforce-version: v1.25
    # We are setting these to our _desired_ `enforce` level, but note that these below values can be any of the available options.
    pod-security.kubernetes.io/audit: privileged
    pod-security.kubernetes.io/audit-version: v1.25
    pod-security.kubernetes.io/warn: privileged
    pod-security.kubernetes.io/warn-version: v1.25
```
4. アップグレードが完了したら、クラスタから残りの PSP リソースをすべて削除します。 多くの場合、「/var/lib/rancher/k3s/server/manifests/」内の強化に使用されるカスタム ファイルに、PodSecurityPolicies と関連する RBAC リソースが存在する可能性があります。 これらのリソースを削除すると、k3s が自動的に更新されます。 タイミングによっては、これらの一部がクラスターに残ることがあります。その場合は、手動で削除する必要があります。 [強化ガイド](../security/hardening-guide.md) に従っていた場合は、次の方法で削除できます。
```sh
# Get the resources associated with PSPs
$ kubectl get roles,clusterroles,rolebindings,clusterrolebindings -A | grep -i psp

# Delete those resources:
$ kubectl delete clusterrole.rbac.authorization.k8s.io/psp:restricted-psp clusterrole.rbac.authorization.k8s.io/psp:svclb-psp clusterrole.rbac.authorization.k8s.io/psp:system-unrestricted-psp clusterrolebinding.rbac.authorization.k8s.io/default:restricted-psp clusterrolebinding.rbac.authorization.k8s.io/system-unrestricted-node-psp-rolebinding && kubectl delete -n kube-system rolebinding.rbac.authorization.k8s.io/svclb-psp-rolebinding rolebinding.rbac.authorization.k8s.io/system-unrestricted-svc-acct-psp-rolebinding
```
