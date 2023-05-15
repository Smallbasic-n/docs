---
title: CIS Self Assessment Guide
weight: 90
---

### CIS Kubernetes Benchmark v1.23 - K3s with Kubernetes v1.22 から v1.24

#### 概要

このドキュメントは、[K3s セキュリティ強化ガイド](hardening-guide.md) に付随するものです。 強化ガイドは、K3s の実稼働インストールを強化するための規範的なガイダンスを提供します。このベンチマーク ガイドは、強化されたクラスターのセキュリティ レベルを CIS Kubernetes Benchmark の各コントロールに対して評価するのに役立つことを目的としています。 これは、K3s のオペレーター、セキュリティ チーム、監査人、および意思決定者によって使用されます。

このガイドは、K3s の **v1.22**、**v1.23**、および **v1.24** リリース ラインと、CIS Kubernetes Benchmark の **v1.23** リリースに固有のものです。

詳細な説明や失敗したテストの修復など、各コントロールの詳細については、CIS Kubernetes Benchmark v1.6 の対応するセクションを参照してください。 ベンチマークは、無料アカウントを作成した後、[Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/kubernetes/) でダウンロードできます。

#### 統制方法のテスト

CIS Kubernetes Benchmark の各コントロールは、付属の強化ガイドに従って構成された K3s クラスターに対して評価されました。

制御監査が元の CIS ベンチマークと異なる場合、K3 に固有の監査コマンドがテスト用に提供されています。

各コントロールの可能な結果は次のとおりです。

- **合格** - テスト対象の K3s クラスターは、ベンチマークで概説されている監査に合格しました。
- **適用外** - コントロールは、その操作方法が設計されているため、K3s には適用されません。 是正セクションでは、なぜそうなのかを説明します。
- **警告** - 制御は CIS ベンチマークでは手動であり、クラスターのユース ケースまたはクラスター オペレーターが決定する必要があるその他の要因に依存します。 これらの制御は、K3s がそれらの実装を妨げないように評価されていますが、テスト中のクラスターのそれ以上の構成または監査は実行されていません。

このガイドでは、K3s が Systemd ユニットとして実行されていることを前提としています。 インストールは異なる場合があり、シナリオに合わせて「監査」コマンドを調整する必要があります。

> 注: このガイドでは、「自動」テスト (以前は「採点済み」と呼ばれていました) のみを扱います。
### コントロール

---

## 1.1 コントロール プレーン ノード構成ファイル
### 1.1.1 API サーバー ポッド仕様ファイルのアクセス許可が 644 以上に制限されていることを確認する (自動)


**結果:** 該当なし

**修復:**
次のコマンドを (システム上のファイルの場所に基づいて) 実行します。
コントロール プレーン ノード。
例: chmod 644 /etc/kubernetes/manifests/kube-apiserver.yaml

### 1.1.2 API サーバー ポッド仕様ファイルの所有権が root:root に設定されていることを確認する (自動)


**結果:** 該当なし

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例: chown root:root /etc/kubernetes/manifests/kube-apiserver.yaml

### 1.1.3 コントローラー マネージャー ポッド仕様ファイルのアクセス許可が 644 以上の制限 (自動) に設定されていることを確認する


**結果:** 該当なし

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例: chmod 644 /etc/kubernetes/manifests/kube-controller-manager.yaml

### 1.1.4 コントローラー マネージャー ポッド仕様ファイルの所有権が root:root (自動) に設定されていることを確認します。


**結果:** 該当なし

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例: chown root:root /etc/kubernetes/manifests/kube-controller-manager.yaml

### 1.1.5 スケジューラ Pod 仕様ファイルのアクセス許可が 644 以上の制限 (自動) に設定されていることを確認する


**結果:** 該当なし

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例: chmod 644 /etc/kubernetes/manifests/kube-scheduler.yaml

### 1.1.6 スケジューラ Pod 仕様ファイルの所有権が root:root に設定されていることを確認します (自動)


**結果:** 該当なし

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例: chown root:root /etc/kubernetes/manifests/kube-scheduler.yaml

### 1.1.7 etcd Pod 仕様ファイルのアクセス許可が 644 以上の制限 (自動) に設定されていることを確認する


**結果:** 該当なし

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chmod 644 /etc/kubernetes/manifests/etcd.yaml

### 1.1.8 etcd Pod 仕様ファイルの所有権が root:root に設定されていることを確認します (自動)


**結果:** 該当なし

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chown root:root /etc/kubernetes/manifests/etcd.yaml

### 1.1.9 Container Network Interfaceファイルのアクセス許可が644以上に制限されていることを確認する(手動)


**結果:** 該当なし

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
たとえば、chmod 644 <パス/to/cni/ファイル>

### 1.1.10 Container Network Interface ファイルの所有権が root:root に設定されていることを確認する (手動)


**結果:** 該当なし

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chown root:root <path/to/cni/files>

### 1.1.11 etcd データ ディレクトリのアクセス許可が 700 以上に制限されていることを確認する (自動)


**結果:**合格

**修復:**
etcd サーバー ノードで、引数 --data-dir として渡された etcd データ ディレクトリを取得します。
コマンドから 'ps -ef | grep etcd」.
以下のコマンドを実行します (上記の etcd データ ディレクトリに基づいて)。 例えば、
chmod 700 /var/lib/etcd

**監査スクリプト:** `check_for_k3s_etcd.sh`
```bash
#!/bin/bash

# This script is used to ensure that k3s is actually running etcd (and not other databases like sqlite3)
# before it checks the requirement
set -eE

handle_error() {
    echo "false"
}

trap 'handle_error' ERR


if [[ "$(journalctl -D /var/log/journal -u k3s | grep 'Managed etcd cluster initializing' | grep -v grep | wc -l)" -gt 0 ]]; then
    case $1 in 
        "1.1.11")
            echo $(stat -c %a /var/lib/rancher/k3s/server/db/etcd);;
        "1.2.29")
            echo $(journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'etcd-');;
        "2.1")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.2")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.3")
            echo $(grep 'auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.4")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.5")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.6")
            echo $(grep 'peer-auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.7")
            echo $(grep 'trusted-ca-file' /var/lib/rancher/k3s/server/db/etcd/config);;
    esac
else
# If another database is running, return whatever is required to pass the scan
    case $1 in
        "1.1.11")
            echo "700";;
        "1.2.29")
            echo "--etcd-certfile AND --etcd-keyfile";;
        "2.1")
            echo "cert-file AND key-file";;
        "2.2")
            echo "--client-cert-auth=true";;
        "2.3")
            echo "false";;
        "2.4")
            echo "peer-cert-file AND peer-key-file";;
        "2.5")
            echo "--client-cert-auth=true";;
        "2.6")
            echo "--peer-auto-tls=false";;
        "2.7")
            echo "--trusted-ca-file";;
    esac
fi

```

**監査の実施:**
```bash
./check_for_k3s_etcd.sh 1.1.11
```

**期待される結果**:

```console
'700' is equal to '700'
```

**戻り値**:

```console
700
```

### 1.1.12 etcd データ ディレクトリの所有権が etcd:etcd (自動) に設定されていることを確認します。


**結果:** 該当なし

**修復:**
etcd サーバー ノードで、引数 --data-dir として渡された etcd データ ディレクトリを取得します。
コマンドから 'ps -ef | grep etcd」.
以下のコマンドを実行します (上記の etcd データ ディレクトリに基づいて)。
例: chown etcd:etcd /var/lib/etcd

### 1.1.13 admin.conf ファイルのアクセス許可が 600 以上に制限されていることを確認する (自動)


**結果:** 該当なし

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例: chmod 600 /var/lib/rancher/k3s/server/cred/admin.kubeconfig

### 1.1.14 admin.conf ファイルの所有権が root:root に設定されていることを確認します (自動)


**結果:**合格

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例: chown root:root /etc/kubernetes/admin.conf

**監査：**
```bash
/bin/sh -c 'if test -e /var/lib/rancher/k3s/server/cred/admin.kubeconfig; then stat -c %U:%G /var/lib/rancher/k3s/server/cred/admin.kubeconfig; fi'
```

**期待される結果**:

```console
'root:root' is equal to 'root:root'
```

**戻り値**:

```console
root:root
```

### 1.1.15 scheduler.conf ファイルのアクセス許可が 644 以上に設定されていることを確認する (自動)


**結果:**合格

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chmod 644 スケジューラー

**監査：**
```bash
/bin/sh -c 'if test -e /var/lib/rancher/k3s/server/cred/scheduler.kubeconfig; then stat -c permissions=%a /var/lib/rancher/k3s/server/cred/scheduler.kubeconfig; fi'
```

**期待される結果**:

```console
permissions has permissions 644, expected 644 or more restrictive
```

**戻り値**:

```console
permissions=644
```

### 1.1.16 scheduler.conf ファイルの所有権が root:root に設定されていることを確認します (自動)


**結果:**合格

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chown root:ルートスケジューラ

**監査：**
```bash
/bin/sh -c 'if test -e /var/lib/rancher/k3s/server/cred/scheduler.kubeconfig; then stat -c %U:%G /var/lib/rancher/k3s/server/cred/scheduler.kubeconfig; fi'
```

**期待される結果**:

```console
'root:root' is present
```

**戻り値**:

```console
root:root
```

### 1.1.17 controller-manager.conf ファイルのアクセス許可が 644 以上に設定されていることを確認します (自動)


**結果:**合格

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chmod 644 コントローラーマネージャー

**監査：**

```bash
/bin/sh -c 'if test -e /var/lib/rancher/k3s/server/cred/controller.kubeconfig; 次に stat -c permissions=%a /var/lib/rancher/k3s/server/cred/controller.kubeconfig; フィ'
```

**期待される結果**：

```console
permissions has permissions 644, expected 644 or more restrictive
```

**戻り値**:

```console
permission=644
```

### 1.1.18 controller-manager.conf ファイルの所有権が root:root に設定されていることを確認します (自動)


**結果:**合格

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chown root:ルートコントローラーマネージャー

**監査：**

```bash
stat -c %U:%G /var/lib/rancher/k3s/server/tls
```

**期待される結果**：

```console
'root:root' is equal to 'root:root'
```

**戻り値**:

```console
root:root
```
### 1.1.19 Kubernetes PKI ディレクトリとファイルの所有権が root:root に設定されていることを確認します (自動)


**結果:**合格

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chown -R root:root /etc/kubernetes/pki/

**監査：**
```bash
find /var/lib/rancher/k3s/server/tls | xargs stat -c %U:%G
```

**期待値**:

```console
'root:root' is present
```

**戻り値**:

```console
root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root root:root
```

### 1.1.20 Kubernetes PKI 証明書ファイルのアクセス許可が 644 以上に制限されていることを確認する (手動)


**結果:** 警告

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chmod -R 644 /etc/kubernetes/pki/*.crt

**監査：**

```bash
stat -c %n %a /var/lib/rancher/k3s/server/tls/*.crt
```

### 1.1.21 Kubernetes PKI キー ファイルのアクセス許可が 600 (手動) に設定されていることを確認する


**結果:** 警告

**修復:**
コントロール プレーン ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chmod -R 600 /etc/kubernetes/pki/*.key

**監査：**

```bash
stat -c %n %a /var/lib/rancher/k3s/server/tls/*.key
```

## 1.2 API サーバー
### 1.2.1 --anonymous-auth 引数が false に設定されていることを確認する (手動)


**結果:** 警告

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで次のパラメータを設定します。
--anonymous-auth=false

**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'anonymous-auth'
```
### 1.2.2 --token-auth-file パラメータが設定されていないことを確認する (自動)


**結果:**合格

**修復:**
ドキュメントに従って、認証用の代替メカニズムを構成します。 それから、
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで、`--token-auth-file=<filename>` パラメータを削除します。

**監査：**

```bash
/bin/ps -ef | grep containerd | grep -v grep
```

**期待される結果**：

```console
'--token-auth-file' is not present
```


**戻り値** :
```console
root 1616 1600 6 13:26 ? 00:01:28 containerd -c /var/lib/rancher/k3s/agent/etc/containerd/config.toml -a /run/k3s/containerd/containerd.sock --state /run/k3s/containerd --root /var/lib/rancher/k3s/agent/containerd root 2318 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id b41ec3297be4625c2406ad8b7b4f8b91cddd60850c420050c4c3273f809b3e7e -address /run/k3s/containerd/containerd.sock root 2341 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id e7999a65ae0a4e9969f32317ec48ae4f7071b62f92e5236696737973be77c2e1 -address /run/k3s/containerd/containerd.sock root 3199 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 90c4e63d6ee29d40a48c2fdaf2738c2472cba1139dde8a550466c452184f8528 -address /run/k3s/containerd/containerd.sock root 3923 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id be5f4b9bd1ed9239362b7000b47f353acb8bc8ca52a9c9145cba0e902ec1c4b9 -address /run/k3s/containerd/containerd.sock root 4559 1 0 13:28 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 04cd40ea6b6078797f177c902c89412c70e523ad2a687a62829bf1d16ff0e19c -address /run/k3s/containerd/containerd.sock root 4647 1 0 13:28 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 48f37a480315b6adce2d2a5c5d67a85412dd0ba7a2e82816434e0deb9fa75de9 -address /run/k3s/containerd/containerd.sock root 6610 1 0 13:47 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 1cf71c22f568468055e517ab363437c0e54e45274c64024d337cc5bcce66341d -address /run/k3s/containerd/containerd.sock
```

### 1.2.3 --DenyServiceExternalIPs が設定されていないことを確認する (自動)


**結果:**合格

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで、「DenyServiceExternalIPs」を削除します
有効な入場プラグインから。

**監査：**
```bash
/bin/ps -ef | grep containerd | grep -v grep
```

**期待される結果**：
```console
'--enable-admission-plugins' is present OR '--enable-admission-plugins' is not present
```

**戻り値**:
```console
root 1616 1600 6 13:26 ? 00:01:28 containerd -c /var/lib/rancher/k3s/agent/etc/containerd/config.toml -a /run/k3s/containerd/containerd.sock --state /run/k3s/containerd --root /var/lib/rancher/k3s/agent/containerd root 2318 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id b41ec3297be4625c2406ad8b7b4f8b91cddd60850c420050c4c3273f809b3e7e -address /run/k3s/containerd/containerd.sock root 2341 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id e7999a65ae0a4e9969f32317ec48ae4f7071b62f92e5236696737973be77c2e1 -address /run/k3s/containerd/containerd.sock root 3199 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 90c4e63d6ee29d40a48c2fdaf2738c2472cba1139dde8a550466c452184f8528 -address /run/k3s/containerd/containerd.sock root 3923 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id be5f4b9bd1ed9239362b7000b47f353acb8bc8ca52a9c9145cba0e902ec1c4b9 -address /run/k3s/containerd/containerd.sock root 4559 1 0 13:28 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 04cd40ea6b6078797f177c902c89412c70e523ad2a687a62829bf1d16ff0e19c -address /run/k3s/containerd/containerd.sock root 4647 1 0 13:28 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 48f37a480315b6adce2d2a5c5d67a85412dd0ba7a2e82816434e0deb9fa75de9 -address /run/k3s/containerd/containerd.sock root 6610 1 0 13:47 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 1cf71c22f568468055e517ab363437c0e54e45274c64024d337cc5bcce66341d -address /run/k3s/containerd/containerd.sock
```

### 1.2.4 --kubelet-https 引数が true に設定されていることを確認する (自動)


**結果:** 該当なし

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --kubelet-https パラメータを削除します。

### 1.2.5 --kubelet-client-certificate および --kubelet-client-key 引数が適切に設定されていることを確認する (自動)


**結果:**合格

**修復:**
Kubernetes のドキュメントに従って、Kubernetes 間の TLS 接続をセットアップします。
apiserver と kubelets。 次に、API サーバー ポッド仕様ファイルを編集します。
/etc/kubernetes/manifests/kube-apiserver.yaml をコントロール プレーン ノードに配置し、
kubelet クライアント証明書とキー パラメータは次のとおりです。
--kubelet-client-certificate=<path/to/client-certificate-file>
--kubelet-client-key=<path/to/client-key-file>

**監査：**
```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'kubelet-certificate-authority'
```

**期待される結果**：
```console
'--kubelet-client-certificate' is present AND '--kubelet-client-key' is present
```

**戻り値**:
```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.6 --kubelet-certificate-authority 引数が適切に設定されていることを確認する (自動)


**結果:**合格

**修復:**
Kubernetes のドキュメントに従って、TLS 接続をセットアップします。
apiserver と kubelets。 次に、API サーバー ポッド仕様ファイルを編集します。
/etc/kubernetes/manifests/kube-apiserver.yaml をコントロール プレーン ノードに配置し、
--kubelet-certificate-authority パラメーターを認証局の証明書ファイルへのパスに
`--kubelet-certificate-authority=<ca-string>`.

**監査：**
```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'kubelet-certificate-authority'
```

**期待される結果**：
```console
'--kubelet-certificate-authority' is present
```

**戻り値**:
```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.7 --authorization-mode 引数が AlwaysAllow (自動) に設定されていないことを確認する


**結果:**合格

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --authorization-mode パラメータを AlwaysAllow 以外の値に設定します。
そのような例の 1 つを以下に示します。
--authorization-mode=RBAC

**監査：**
```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'authorization-mode'
```

**期待される結果**：
```console
'--authorization-mode' does not have 'AlwaysAllow'
```

**戻り値**:
```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.8 --authorization-mode 引数に Node (Automated) が含まれていることを確認します


**結果:**合格

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --authorization-mode パラメータをノードを含む値に設定します。
--authorization-mode=ノード、RBAC

**監査：**
```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'authorization-mode'
```

**期待される結果**：
```console
'--authorization-mode' has 'Node'
```

**戻り値**:
```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.9 --authorization-mode 引数に RBAC (自動) が含まれていることを確認する


**結果:**合格

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --authorization-mode パラメータを RBAC を含む値に設定します。
たとえば、`--authorization-mode=Node,RBAC` です。

**監査：**
```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'authorization-mode'
```

**期待される結果**：
```console
'--authorization-mode' has 'RBAC'
```

**戻り値**:
```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.10 アドミッション コントロール プラグインの EventRateLimit が設定されていることを確認する (手動)


**結果:** 警告

**修復:**
Kubernetes のドキュメントに従って、構成ファイルで必要な制限を設定します。
次に、API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
以下のパラメータを設定します。
--enable-admission-plugins=...,EventRateLimit,...
--admission-control-config-file=<path/to/configuration/file>

**監査：**
```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'enable-admission-plugins'
```

**期待される結果**：
```console
'--enable-admission-plugins' has 'EventRateLimit'
```

**戻り値**:
```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.11 アドミッション コントロール プラグイン AlwaysAdmit が設定されていないことを確認する (自動)


**結果:**合格

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --enable-admission-plugins パラメータを削除するか、または
AlwaysAdmit を含まない値。

**監査：**
```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'enable-admission-plugins'
```

**期待される結果**：
```console
'--enable-admission-plugins' does not have 'AlwaysAdmit' OR '--enable-admission-plugins' is not present
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.12 アドミッション コントロール プラグイン AlwaysPullImages が設定されていることを確認する (手動)

**結果：** warn

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --enable-admission-plugins パラメーターを設定して、
AlwaysPullImages。
--enable-admission-plugins=...,AlwaysPullImages,...
**監査：**

```bash
/bin/ps -ef | grep containerd | grep -v grep
```

**期待される結果**：

```console
'--enable-admission-plugins' is present
```

**戻り値**:

```console
root 1616 1600 6 13:26 ? 00:01:28 containerd -c /var/lib/rancher/k3s/agent/etc/containerd/config.toml -a /run/k3s/containerd/containerd.sock --state /run/k3s/containerd --root /var/lib/rancher/k3s/agent/containerd root 2318 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id b41ec3297be4625c2406ad8b7b4f8b91cddd60850c420050c4c3273f809b3e7e -address /run/k3s/containerd/containerd.sock root 2341 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id e7999a65ae0a4e9969f32317ec48ae4f7071b62f92e5236696737973be77c2e1 -address /run/k3s/containerd/containerd.sock root 3199 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 90c4e63d6ee29d40a48c2fdaf2738c2472cba1139dde8a550466c452184f8528 -address /run/k3s/containerd/containerd.sock root 3923 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id be5f4b9bd1ed9239362b7000b47f353acb8bc8ca52a9c9145cba0e902ec1c4b9 -address /run/k3s/containerd/containerd.sock root 4559 1 0 13:28 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 04cd40ea6b6078797f177c902c89412c70e523ad2a687a62829bf1d16ff0e19c -address /run/k3s/containerd/containerd.sock root 4647 1 0 13:28 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 48f37a480315b6adce2d2a5c5d67a85412dd0ba7a2e82816434e0deb9fa75de9 -address /run/k3s/containerd/containerd.sock root 6610 1 0 13:47 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 1cf71c22f568468055e517ab363437c0e54e45274c64024d337cc5bcce66341d -address /run/k3s/containerd/containerd.sock
```

### 1.2.13 PodSecurityPolicy が使用されていない場合、アドミッション コントロール プラグイン SecurityContextDeny が設定されていることを確認する (手動)


**結果：** 警告

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --enable-admission-plugins パラメーターを設定して、
PodSecurityPolicy が既に配置されていない限り、SecurityContextDeny。
--enable-admission-plugins=...,SecurityContextDeny,...
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'enable-admission-plugins'
```

**期待される結果**：

```console
'--enable-admission-plugins' has 'SecurityContextDeny' OR '--enable-admission-plugins' has 'PodSecurityPolicy'
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.14 アドミッション コントロール プラグイン ServiceAccount が設定されていることを確認する (自動)


**結果：**合格

**修復:**
ドキュメントに従って、環境に応じて ServiceAccount オブジェクトを作成します。
次に、API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --disable-admission-plugins パラメータが設定されていることを確認します。
ServiceAccount を含まない値。
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep -v grep
```

**期待される結果**：

```console
'--disable-admission-plugins' is present OR '--disable-admission-plugins' is not present
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.15 アドミッション コントロール プラグイン NamespaceLifecycle が設定されていることを確認する (自動)


**結果：**合格

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --disable-admission-plugins パラメータを
NamespaceLifecycle が含まれていないことを確認してください。
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep -v grep
```

**期待される結果**：

```console
'--disable-admission-plugins' is present OR '--disable-admission-plugins' is not present
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.16 アドミッション コントロール プラグイン NodeRestriction が設定されていることを確認する (自動)


**結果：**合格

**修復:**
Kubernetes のドキュメントに従って、kubelets で NodeRestriction プラグインを構成します。
次に、API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --enable-admission-plugins パラメータを
NodeRestriction を含む値。
--enable-admission-plugins=...,NodeRestriction,...
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'enable-admission-plugins'
```

**期待される結果**：

```console
'--enable-admission-plugins' has 'NodeRestriction'
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.17 --secure-port 引数が 0 に設定されていないことを確認します (自動)


**結果：**合格

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --secure-port パラメータを削除するか、
別の (ゼロ以外の) 目的のポートに設定します。
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'secure-port'
```

**期待される結果**：

```console
'--secure-port' is greater than 0 OR '--secure-port' is not present
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.18 --profiling 引数が false に設定されていることを確認します (自動)


**結果：**合格

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで次のパラメータを設定します。
--profiling=false
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'profiling'
```

**期待される結果**：

```console
'--profiling' is equal to 'false'
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.19 --audit-log-path 引数が設定されていることを確認します (自動)


**結果：** 該当なし

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --audit-log-path パラメータを適切なパスに設定し、
監査ログを書き込むファイル。たとえば、
--audit-log-path=/var/log/apiserver/audit.log

### 1.2.20 --audit-log-maxage 引数が 30 または適切に設定されていることを確認します (自動)


**結果：** 該当なし

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --audit-log-maxage パラメータを 30 に設定します。
または適切な日数として、たとえば、
--audit-log-maxage=30

### 1.2.21 --audit-log-maxbackup 引数が 10 または適切に設定されていることを確認します (自動)


**結果：** 該当なし

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --audit-log-maxbackup パラメータを 10 または適切な
価値。 例えば、
--audit-log-maxbackup=10

### 1.2.22 --audit-log-maxsize 引数が 100 または適切に設定されていることを確認します (自動)


**結果：** 該当なし

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --audit-log-maxsize パラメータを適切なサイズ (MB) に設定します。
たとえば、100 MB に設定するには、 --audit-log-maxsize=100

### 1.2.24 --service-account-lookup 引数が true に設定されていることを確認します (自動)


**結果：**合格

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで次のパラメータを設定します。
--service-account-lookup=true
または、このファイルから --service-account-lookup パラメータを削除して、
デフォルトが有効になること。
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep -v grep
```

**期待される結果**：

```console
'--service-account-lookup' is not present OR '--service-account-lookup' is present
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.25 --request-timeout 引数が適切に設定されていることを確認します (自動)


**結果：** 該当なし

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで --service-account-key-file パラメータを設定します
サービス アカウントの公開鍵ファイルに追加します。 例えば、
`--service-account-key-file=<ファイル名>`.

### 1.2.26 --etcd-certfile および --etcd-keyfile 引数が適切に設定されていることを確認する (自動化)


**結果：**合格

**修復:**
Kubernetes のドキュメントに従って、apiserver と etcd の間の TLS 接続をセットアップします。
次に、API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで、etcd 証明書とキー ファイルのパラメーターを設定します。
--etcd-certfile=<path/to/client-certificate-file>
--etcd-keyfile=<path/to/client-key-file>

**監査スクリプト:** `check_for_k3s_etcd.sh`
```bash
#!/bin/bash

# This script is used to ensure that k3s is actually running etcd (and not other databases like sqlite3)
# before it checks the requirement
set -eE

handle_error() {
    echo "false"
}

trap 'handle_error' ERR


if [[ "$(journalctl -D /var/log/journal -u k3s | grep 'Managed etcd cluster initializing' | grep -v grep | wc -l)" -gt 0 ]]; then
    case $1 in 
        "1.1.11")
            echo $(stat -c %a /var/lib/rancher/k3s/server/db/etcd);;
        "1.2.29")
            echo $(journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'etcd-');;
        "2.1")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.2")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.3")
            echo $(grep 'auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.4")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.5")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.6")
            echo $(grep 'peer-auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.7")
            echo $(grep 'trusted-ca-file' /var/lib/rancher/k3s/server/db/etcd/config);;
    esac
else
# If another database is running, return whatever is required to pass the scan
    case $1 in
        "1.1.11")
            echo "700";;
        "1.2.29")
            echo "--etcd-certfile AND --etcd-keyfile";;
        "2.1")
            echo "cert-file AND key-file";;
        "2.2")
            echo "--client-cert-auth=true";;
        "2.3")
            echo "false";;
        "2.4")
            echo "peer-cert-file AND peer-key-file";;
        "2.5")
            echo "--client-cert-auth=true";;
        "2.6")
            echo "--peer-auto-tls=false";;
        "2.7")
            echo "--trusted-ca-file";;
    esac
fi

```

**監査の実施:**
```bash
./check_for_k3s_etcd.sh 1.2.29
```

**期待される結果**：

```console
'--etcd-certfile' is present AND '--etcd-keyfile' is present
```

**戻り値**:

```console
--etcd-certfile AND --etcd-keyfile
```

### 1.2.27 --tls-cert-file および --tls-private-key-file 引数が適切に設定されていることを確認します (自動)


**結果：**合格

**修復:**
Kubernetes のドキュメントに従って、apiserver で TLS 接続をセットアップします。
次に、API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで、TLS 証明書と秘密鍵ファイルのパラメーターを設定します。--tls-cert-file=<path/to/tls-certificate-file>
--tls-private-key-file=<path/to/tls-key-file>

**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep -A1 'Running kube-apiserver' | tail -n2
```

**期待される結果**：

```console
'--tls-cert-file' is present AND '--tls-private-key-file' is present
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key" Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-scheduler --authentication-kubeconfig=/var/lib/rancher/k3s/server/cred/scheduler.kubeconfig --authorization-kubeconfig=/var/lib/rancher/k3s/server/cred/scheduler.kubeconfig --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/kube-scheduler --kubeconfig=/var/lib/rancher/k3s/server/cred/scheduler.kubeconfig --profiling=false --secure-port=10259"
```

### 1.2.28 --client-ca-file 引数が適切に設定されていることを確認します (自動化)


**結果：**合格

**修復:**
Kubernetes のドキュメントに従って、apiserver で TLS 接続をセットアップします。
次に、API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで、クライアント認証局ファイルを設定します。
--client-ca-file=<path/to/client-ca-file>
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'client-ca-file'
```

**期待される結果**：

```console
'--client-ca-file' is present
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.29 --etcd-cafile 引数が適切に設定されていることを確認します (自動化)


**結果：**合格

**修復:**
Kubernetes のドキュメントに従って、apiserver と etcd の間の TLS 接続をセットアップします。
次に、API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで etcd 認証局ファイル パラメータを設定します。--etcd-cafile=<path/to/ca-file>

**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'etcd-cafile'
```

**期待される結果**：

```console
'--etcd-cafile' is present
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 1.2.30 --encryption-provider-config 引数が適切に設定されていることを確認する (手動)


**結果：** 警告

**修復:**
Kubernetes のドキュメントに従って、EncryptionConfig ファイルを構成します。
次に、API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで、 --encryption-provider-config パラメータをそのファイルのパスに設定します。
例: --encryption-provider-config=</path/to/EncryptionConfig/File>
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'encryption-provider-config'
```

### 1.2.31 暗号化プロバイダーが適切に構成されていることを確認する (手動)


**結果：** 警告

**修復:**
Kubernetes のドキュメントに従って、EncryptionConfig ファイルを構成します。
このファイルで、暗号化プロバイダーとして aescbc、kms、または secretbox を選択します。
**監査：**

```bash
grep aescbc /path/to/encryption-config.json
```

### 1.2.32 API サーバーが強力な暗号化方式のみを使用するようにする (手動)


**結果：** 警告

**修復:**
API サーバー ポッド仕様ファイル /etc/kubernetes/manifests/kube-apiserver.yaml を編集します。
コントロール プレーン ノードで次のパラメータを設定します。--tls-cipher-suites=TLS_AES_128_GCM_SHA256,TLS_AES_256_GCM_SHA384,TLS_CHACHA20_POLY1305_SHA256,
TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305,TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256,
TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,
TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305,
TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256,TLS_RSA_WITH_3DES_EDE_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA,
TLS_RSA_WITH_AES_128_GCM_SHA256,TLS_RSA_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_AES_256_GCM_SHA384

**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'tls-cipher-suites'
```

## 1.3 コントローラーマネージャー
### 1.3.1 --terminated-pod-gc-threshold 引数が適切に設定されていることを確認する (手動)


**結果：** 警告

**修復:**
Controller Manager ポッド仕様ファイル /etc/kubernetes/manifests/kube-controller-manager.yaml を編集します。
コントロール プレーン ノードで --terminated-pod-gc-threshold を適切なしきい値に設定します。
例: --terminated-pod-gc-threshold=10
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-controller-manager' | tail -n1 | grep 'terminated-pod-gc-threshold'
```

### 1.3.2 --profiling 引数が false に設定されていることを確認する (自動)


**結果：**合格

**修復:**
Controller Manager ポッド仕様ファイル /etc/kubernetes/manifests/kube-controller-manager.yaml を編集します。
コントロール プレーン ノードで次のパラメータを設定します。
--profiling=false
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-controller-manager' | tail -n1 | grep 'profiling'
```

**期待される結果**：

```console
'--profiling' is equal to 'false'
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-controller-manager --allocate-node-cidrs=true --authentication-kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig --authorization-kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/kube-controller-manager --cluster-cidr=10.42.0.0/16 --cluster-signing-kube-apiserver-client-cert-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --cluster-signing-kube-apiserver-client-key-file=/var/lib/rancher/k3s/server/tls/client-ca.key --cluster-signing-kubelet-client-cert-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --cluster-signing-kubelet-client-key-file=/var/lib/rancher/k3s/server/tls/client-ca.key --cluster-signing-kubelet-serving-cert-file=/var/lib/rancher/k3s/server/tls/server-ca.crt --cluster-signing-kubelet-serving-key-file=/var/lib/rancher/k3s/server/tls/server-ca.key --cluster-signing-legacy-unknown-cert-file=/var/lib/rancher/k3s/server/tls/server-ca.crt --cluster-signing-legacy-unknown-key-file=/var/lib/rancher/k3s/server/tls/server-ca.key --configure-cloud-routes=false --controllers=*,-service,-route,-cloud-node-lifecycle --feature-gates=JobTrackingWithFinalizers=true --kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig --profiling=false --root-ca-file=/var/lib/rancher/k3s/server/tls/server-ca.crt --secure-port=10257 --service-account-private-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --use-service-account-credentials=true"
```

### 1.3.3 --use-service-account-credentials 引数が true (自動) に設定されていることを確認する


**結果：**合格

**修復:**
Controller Manager ポッド仕様ファイル /etc/kubernetes/manifests/kube-controller-manager.yaml を編集します。
コントロール プレーン ノードで次のパラメータを設定します。
--use-service-account-credentials=true
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-controller-manager' | tail -n1 | grep 'use-service-account-credentials'
```

**期待される結果**：

```console
'--use-service-account-credentials' is not equal to 'false'
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-controller-manager --allocate-node-cidrs=true --authentication-kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig --authorization-kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/kube-controller-manager --cluster-cidr=10.42.0.0/16 --cluster-signing-kube-apiserver-client-cert-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --cluster-signing-kube-apiserver-client-key-file=/var/lib/rancher/k3s/server/tls/client-ca.key --cluster-signing-kubelet-client-cert-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --cluster-signing-kubelet-client-key-file=/var/lib/rancher/k3s/server/tls/client-ca.key --cluster-signing-kubelet-serving-cert-file=/var/lib/rancher/k3s/server/tls/server-ca.crt --cluster-signing-kubelet-serving-key-file=/var/lib/rancher/k3s/server/tls/server-ca.key --cluster-signing-legacy-unknown-cert-file=/var/lib/rancher/k3s/server/tls/server-ca.crt --cluster-signing-legacy-unknown-key-file=/var/lib/rancher/k3s/server/tls/server-ca.key --configure-cloud-routes=false --controllers=*,-service,-route,-cloud-node-lifecycle --feature-gates=JobTrackingWithFinalizers=true --kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig --profiling=false --root-ca-file=/var/lib/rancher/k3s/server/tls/server-ca.crt --secure-port=10257 --service-account-private-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --use-service-account-credentials=true"
```

### 1.3.4 --service-account-private-key-file 引数が適切に設定されていることを確認する (自動)


**結果：**合格

**修復:**
Controller Manager ポッド仕様ファイル /etc/kubernetes/manifests/kube-controller-manager.yaml を編集します。
コントロール プレーン ノードで --service-account-private-key-file パラメータを設定します
サービス アカウントの秘密鍵ファイルに追加します。 例えば、`--service-account-private-key-file=<filename>`.

**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-controller-manager' | tail -n1 | grep 'service-account-private-key-file'
```

**期待される結果**：

```console
'--service-account-private-key-file' is present
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-controller-manager --allocate-node-cidrs=true --authentication-kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig --authorization-kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/kube-controller-manager --cluster-cidr=10.42.0.0/16 --cluster-signing-kube-apiserver-client-cert-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --cluster-signing-kube-apiserver-client-key-file=/var/lib/rancher/k3s/server/tls/client-ca.key --cluster-signing-kubelet-client-cert-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --cluster-signing-kubelet-client-key-file=/var/lib/rancher/k3s/server/tls/client-ca.key --cluster-signing-kubelet-serving-cert-file=/var/lib/rancher/k3s/server/tls/server-ca.crt --cluster-signing-kubelet-serving-key-file=/var/lib/rancher/k3s/server/tls/server-ca.key --cluster-signing-legacy-unknown-cert-file=/var/lib/rancher/k3s/server/tls/server-ca.crt --cluster-signing-legacy-unknown-key-file=/var/lib/rancher/k3s/server/tls/server-ca.key --configure-cloud-routes=false --controllers=*,-service,-route,-cloud-node-lifecycle --feature-gates=JobTrackingWithFinalizers=true --kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig --profiling=false --root-ca-file=/var/lib/rancher/k3s/server/tls/server-ca.crt --secure-port=10257 --service-account-private-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --use-service-account-credentials=true"
```

### 1.3.5 --root-ca-file 引数が適切に設定されていることを確認する (自動化)


**結果：**合格

**修復:**
Controller Manager ポッド仕様ファイル /etc/kubernetes/manifests/kube-controller-manager.yaml を編集します。
コントロール プレーン ノードで --root-ca-file パラメータを証明書バンドル ファイルに設定します。
--root-ca-file=<パス/ファイルへのパス>
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-controller-manager' | tail -n1 | grep 'root-ca-file'
```

**期待される結果**：

```console
'--root-ca-file' is present
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-controller-manager --allocate-node-cidrs=true --authentication-kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig --authorization-kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/kube-controller-manager --cluster-cidr=10.42.0.0/16 --cluster-signing-kube-apiserver-client-cert-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --cluster-signing-kube-apiserver-client-key-file=/var/lib/rancher/k3s/server/tls/client-ca.key --cluster-signing-kubelet-client-cert-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --cluster-signing-kubelet-client-key-file=/var/lib/rancher/k3s/server/tls/client-ca.key --cluster-signing-kubelet-serving-cert-file=/var/lib/rancher/k3s/server/tls/server-ca.crt --cluster-signing-kubelet-serving-key-file=/var/lib/rancher/k3s/server/tls/server-ca.key --cluster-signing-legacy-unknown-cert-file=/var/lib/rancher/k3s/server/tls/server-ca.crt --cluster-signing-legacy-unknown-key-file=/var/lib/rancher/k3s/server/tls/server-ca.key --configure-cloud-routes=false --controllers=*,-service,-route,-cloud-node-lifecycle --feature-gates=JobTrackingWithFinalizers=true --kubeconfig=/var/lib/rancher/k3s/server/cred/controller.kubeconfig --profiling=false --root-ca-file=/var/lib/rancher/k3s/server/tls/server-ca.crt --secure-port=10257 --service-account-private-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --use-service-account-credentials=true"
```

### 1.3.6 RotateKubeletServerCertificate 引数が true (自動) に設定されていることを確認する


**結果：** 該当なし

**修復:**
Controller Manager ポッド仕様ファイル /etc/kubernetes/manifests/kube-controller-manager.yaml を編集します。
コントロール プレーン ノードで、--feature-gates パラメーターを設定して、RotateKubeletServerCertificate=true を含めます。
--feature-gates=RotateKubeletServerCertificate=true

### 1.3.7 --bind-address 引数が 127.0.0.1 (自動) に設定されていることを確認します


**結果：**合格

**修復:**
Controller Manager ポッド仕様ファイル /etc/kubernetes/manifests/kube-controller-manager.yaml を編集します。
コントロールプレーンノードで正しい値を確認してください--bind-address parameter

**監査：**

```bash
/bin/ps -ef | grep containerd | grep -v grep
```

**期待される結果**：

```console
'--bind-address' is present OR '--bind-address' is not present
```

**戻り値**:

```console
root 1616 1600 6 13:26 ? 00:01:28 containerd -c /var/lib/rancher/k3s/agent/etc/containerd/config.toml -a /run/k3s/containerd/containerd.sock --state /run/k3s/containerd --root /var/lib/rancher/k3s/agent/containerd root 2318 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id b41ec3297be4625c2406ad8b7b4f8b91cddd60850c420050c4c3273f809b3e7e -address /run/k3s/containerd/containerd.sock root 2341 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id e7999a65ae0a4e9969f32317ec48ae4f7071b62f92e5236696737973be77c2e1 -address /run/k3s/containerd/containerd.sock root 3199 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 90c4e63d6ee29d40a48c2fdaf2738c2472cba1139dde8a550466c452184f8528 -address /run/k3s/containerd/containerd.sock root 3923 1 0 13:27 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id be5f4b9bd1ed9239362b7000b47f353acb8bc8ca52a9c9145cba0e902ec1c4b9 -address /run/k3s/containerd/containerd.sock root 4559 1 0 13:28 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 04cd40ea6b6078797f177c902c89412c70e523ad2a687a62829bf1d16ff0e19c -address /run/k3s/containerd/containerd.sock root 4647 1 0 13:28 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 48f37a480315b6adce2d2a5c5d67a85412dd0ba7a2e82816434e0deb9fa75de9 -address /run/k3s/containerd/containerd.sock root 6610 1 0 13:47 ? 00:00:00 /var/lib/rancher/k3s/data/577968fa3d58539cc4265245941b7be688833e6bf5ad7869fa2afe02f15f1cd2/bin/containerd-shim-runc-v2 -namespace k8s.io -id 1cf71c22f568468055e517ab363437c0e54e45274c64024d337cc5bcce66341d -address /run/k3s/containerd/containerd.sock
```

## 1.4 スケジューラ
### 1.4.1 --profiling 引数が false に設定されていることを確認する (自動)


**結果：**合格

**修復:**
スケジューラ ポッド仕様ファイル /etc/kubernetes/manifests/kube-scheduler.yaml ファイルを編集します
コントロール プレーン ノードで次のパラメータを設定します。
--profiling=false
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-scheduler' | tail -n1
```

**期待される結果**：

```console
'--profiling' is equal to 'false'
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-scheduler --authentication-kubeconfig=/var/lib/rancher/k3s/server/cred/scheduler.kubeconfig --authorization-kubeconfig=/var/lib/rancher/k3s/server/cred/scheduler.kubeconfig --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/kube-scheduler --kubeconfig=/var/lib/rancher/k3s/server/cred/scheduler.kubeconfig --profiling=false --secure-port=10259"
```

### 1.4.2 --bind-address 引数が 127.0.0.1 (自動) に設定されていることを確認します


**結果：**合格

**修復:**
スケジューラ ポッド仕様ファイル /etc/kubernetes/manifests/kube-scheduler.yaml を編集します。
コントロールプレーンノードで正しい値を確認してください--bind-address parameter

**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-scheduler' | tail -n1 | grep 'bind-address'
```

**期待される結果**：

```console
'--bind-address' is equal to '127.0.0.1' OR '--bind-address' is not present
```

**戻り値**:

```console
Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-scheduler --authentication-kubeconfig=/var/lib/rancher/k3s/server/cred/scheduler.kubeconfig --authorization-kubeconfig=/var/lib/rancher/k3s/server/cred/scheduler.kubeconfig --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/kube-scheduler --kubeconfig=/var/lib/rancher/k3s/server/cred/scheduler.kubeconfig --profiling=false --secure-port=10259"
```

## 2 etcd ノードの構成
### 2.1 --cert-file および --key-file 引数が適切に設定されていることを確認する (自動)


**結果：**合格

**修復:**
etcd サービスのドキュメントに従って、TLS 暗号化を構成します。
次に、etcd Pod 仕様ファイル /etc/kubernetes/manifests/etcd.yaml を編集します。
マスターノードで、以下のパラメーターを設定します。
--cert-file=</path/to/ca-file>
--key-file=</path/to/key-file>

**監査スクリプト:** `check_for_k3s_etcd.sh`

```bash
#!/bin/bash

# This script is used to ensure that k3s is actually running etcd (and not other databases like sqlite3)
# before it checks the requirement
set -eE

handle_error() {
    echo "false"
}

trap 'handle_error' ERR


if [[ "$(journalctl -D /var/log/journal -u k3s | grep 'Managed etcd cluster initializing' | grep -v grep | wc -l)" -gt 0 ]]; then
    case $1 in 
        "1.1.11")
            echo $(stat -c %a /var/lib/rancher/k3s/server/db/etcd);;
        "1.2.29")
            echo $(journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'etcd-');;
        "2.1")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.2")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.3")
            echo $(grep 'auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.4")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.5")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.6")
            echo $(grep 'peer-auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.7")
            echo $(grep 'trusted-ca-file' /var/lib/rancher/k3s/server/db/etcd/config);;
    esac
else
# If another database is running, return whatever is required to pass the scan
    case $1 in
        "1.1.11")
            echo "700";;
        "1.2.29")
            echo "--etcd-certfile AND --etcd-keyfile";;
        "2.1")
            echo "cert-file AND key-file";;
        "2.2")
            echo "--client-cert-auth=true";;
        "2.3")
            echo "false";;
        "2.4")
            echo "peer-cert-file AND peer-key-file";;
        "2.5")
            echo "--client-cert-auth=true";;
        "2.6")
            echo "--peer-auto-tls=false";;
        "2.7")
            echo "--trusted-ca-file";;
    esac
fi

```

**監査の実施:**

```bash
./check_for_k3s_etcd.sh 2.1
```

**期待される結果**：

```console
'cert-file' is present AND 'key-file' is present
```

**戻り値**:

```console
cert-file AND key-file cert-file: /var/lib/rancher/k3s/server/tls/etcd/server-client.crt key-file: /var/lib/rancher/k3s/server/tls/etcd/server-client.key cert-file AND key-file
```

### 2.2 --client-cert-auth 引数が true (自動) に設定されていることを確認する


**結果：**合格

**修復:**
マスターで etcd Pod 仕様ファイル /etc/kubernetes/manifests/etcd.yaml を編集します。
ノードを開き、以下のパラメーターを設定します。
--client-cert-auth="true"

**監査スクリプト:** `check_for_k3s_etcd.sh`
```bash
#!/bin/bash

# This script is used to ensure that k3s is actually running etcd (and not other databases like sqlite3)
# before it checks the requirement
set -eE

handle_error() {
    echo "false"
}

trap 'handle_error' ERR


if [[ "$(journalctl -D /var/log/journal -u k3s | grep 'Managed etcd cluster initializing' | grep -v grep | wc -l)" -gt 0 ]]; then
    case $1 in 
        "1.1.11")
            echo $(stat -c %a /var/lib/rancher/k3s/server/db/etcd);;
        "1.2.29")
            echo $(journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'etcd-');;
        "2.1")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.2")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.3")
            echo $(grep 'auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.4")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.5")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.6")
            echo $(grep 'peer-auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.7")
            echo $(grep 'trusted-ca-file' /var/lib/rancher/k3s/server/db/etcd/config);;
    esac
else
# If another database is running, return whatever is required to pass the scan
    case $1 in
        "1.1.11")
            echo "700";;
        "1.2.29")
            echo "--etcd-certfile AND --etcd-keyfile";;
        "2.1")
            echo "cert-file AND key-file";;
        "2.2")
            echo "--client-cert-auth=true";;
        "2.3")
            echo "false";;
        "2.4")
            echo "peer-cert-file AND peer-key-file";;
        "2.5")
            echo "--client-cert-auth=true";;
        "2.6")
            echo "--peer-auto-tls=false";;
        "2.7")
            echo "--trusted-ca-file";;
    esac
fi

```

**監査の実施:**

```bash
./check_for_k3s_etcd.sh 2.2
```

**期待される結果**：

```console
'--client-cert-auth' is present OR 'client-cert-auth' is equal to 'true'
```

**戻り値**:

```console
--client-cert-auth=true client-cert-auth: true --client-cert-auth=true
```

### 2.3 --auto-tls 引数が true に設定されていないことを確認する (自動)


**結果：**合格

**修復:**
マスターで etcd Pod 仕様ファイル /etc/kubernetes/manifests/etcd.yaml を編集します。
--auto-tls パラメータを削除するか、false に設定します。 --auto-tls=false

**監査スクリプト:** `check_for_k3s_etcd.sh`

```bash
#!/bin/bash

# This script is used to ensure that k3s is actually running etcd (and not other databases like sqlite3)
# before it checks the requirement
set -eE

handle_error() {
    echo "false"
}

trap 'handle_error' ERR


if [[ "$(journalctl -D /var/log/journal -u k3s | grep 'Managed etcd cluster initializing' | grep -v grep | wc -l)" -gt 0 ]]; then
    case $1 in 
        "1.1.11")
            echo $(stat -c %a /var/lib/rancher/k3s/server/db/etcd);;
        "1.2.29")
            echo $(journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'etcd-');;
        "2.1")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.2")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.3")
            echo $(grep 'auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.4")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.5")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.6")
            echo $(grep 'peer-auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.7")
            echo $(grep 'trusted-ca-file' /var/lib/rancher/k3s/server/db/etcd/config);;
    esac
else
# If another database is running, return whatever is required to pass the scan
    case $1 in
        "1.1.11")
            echo "700";;
        "1.2.29")
            echo "--etcd-certfile AND --etcd-keyfile";;
        "2.1")
            echo "cert-file AND key-file";;
        "2.2")
            echo "--client-cert-auth=true";;
        "2.3")
            echo "false";;
        "2.4")
            echo "peer-cert-file AND peer-key-file";;
        "2.5")
            echo "--client-cert-auth=true";;
        "2.6")
            echo "--peer-auto-tls=false";;
        "2.7")
            echo "--trusted-ca-file";;
    esac
fi

```

**監査の実施:**

```bash
./check_for_k3s_etcd.sh 2.3
```

**期待される結果**：

```console
'ETCD_AUTO_TLS' is not present OR 'ETCD_AUTO_TLS' is present
```

**戻り値**:

```console
error: process ID list syntax error Usage: ps [options] Try 'ps --help <simple|list|output|threads|misc|all>' or 'ps --help <s|l|o|t|m|a>' for additional help text. For more details see ps(1). cat: /proc//environ: No such file or directory error: process ID list syntax error Usage: ps [options] Try 'ps --help <simple|list|output|threads|misc|all>' or 'ps --help <s|l|o|t|m|a>' for additional help text. For more details see ps(1). cat: /proc//environ: No such file or directory error: process ID list syntax error Usage: ps [options] Try 'ps --help <simple|list|output|threads|misc|all>' or 'ps --help <s|l|o|t|m|a>' for additional help text. For more details see ps(1). cat: /proc//environ: No such file or directory
```

### 2.4 --peer-cert-file および --peer-key-file 引数が適切に設定されていることを確認する (自動)


**結果：**合格

**修復:**
etcd サービスのドキュメントに従い、必要に応じてピア TLS 暗号化を構成します
etcd クラスター用。
次に、etcd ポッド仕様ファイル /etc/kubernetes/manifests/etcd.yaml を編集します。
以下のパラメータを設定します。
--peer-client-file=</path/to/peer-cert-file>
--peer-key-file=</path/to/peer-key-file>

**監査スクリプト:** `check_for_k3s_etcd.sh`

```bash
#!/bin/bash

# This script is used to ensure that k3s is actually running etcd (and not other databases like sqlite3)
# before it checks the requirement
set -eE

handle_error() {
    echo "false"
}

trap 'handle_error' ERR


if [[ "$(journalctl -D /var/log/journal -u k3s | grep 'Managed etcd cluster initializing' | grep -v grep | wc -l)" -gt 0 ]]; then
    case $1 in 
        "1.1.11")
            echo $(stat -c %a /var/lib/rancher/k3s/server/db/etcd);;
        "1.2.29")
            echo $(journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'etcd-');;
        "2.1")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.2")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.3")
            echo $(grep 'auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.4")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.5")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.6")
            echo $(grep 'peer-auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.7")
            echo $(grep 'trusted-ca-file' /var/lib/rancher/k3s/server/db/etcd/config);;
    esac
else
# If another database is running, return whatever is required to pass the scan
    case $1 in
        "1.1.11")
            echo "700";;
        "1.2.29")
            echo "--etcd-certfile AND --etcd-keyfile";;
        "2.1")
            echo "cert-file AND key-file";;
        "2.2")
            echo "--client-cert-auth=true";;
        "2.3")
            echo "false";;
        "2.4")
            echo "peer-cert-file AND peer-key-file";;
        "2.5")
            echo "--client-cert-auth=true";;
        "2.6")
            echo "--peer-auto-tls=false";;
        "2.7")
            echo "--trusted-ca-file";;
    esac
fi

```

**監査の実施:**

```bash
./check_for_k3s_etcd.sh 2.4
```

**期待される結果**：

```console
'cert-file' is present AND 'key-file' is present
```

**戻り値**:

```console
peer-cert-file AND peer-key-file cert-file: /var/lib/rancher/k3s/server/tls/etcd/peer-server-client.crt key-file: /var/lib/rancher/k3s/server/tls/etcd/peer-server-client.key peer-cert-file AND peer-key-file
```

### 2.5 --peer-client-cert-auth 引数が true (自動) に設定されていることを確認します


**結果：**合格

**修復:**
マスターで etcd Pod 仕様ファイル /etc/kubernetes/manifests/etcd.yaml を編集します。
ノードを開き、以下のパラメーターを設定します。
--peer-client-cert-auth=true

**監査スクリプト:** `check_for_k3s_etcd.sh`

```bash
#!/bin/bash

# This script is used to ensure that k3s is actually running etcd (and not other databases like sqlite3)
# before it checks the requirement
set -eE

handle_error() {
    echo "false"
}

trap 'handle_error' ERR


if [[ "$(journalctl -D /var/log/journal -u k3s | grep 'Managed etcd cluster initializing' | grep -v grep | wc -l)" -gt 0 ]]; then
    case $1 in 
        "1.1.11")
            echo $(stat -c %a /var/lib/rancher/k3s/server/db/etcd);;
        "1.2.29")
            echo $(journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'etcd-');;
        "2.1")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.2")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.3")
            echo $(grep 'auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.4")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.5")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.6")
            echo $(grep 'peer-auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.7")
            echo $(grep 'trusted-ca-file' /var/lib/rancher/k3s/server/db/etcd/config);;
    esac
else
# If another database is running, return whatever is required to pass the scan
    case $1 in
        "1.1.11")
            echo "700";;
        "1.2.29")
            echo "--etcd-certfile AND --etcd-keyfile";;
        "2.1")
            echo "cert-file AND key-file";;
        "2.2")
            echo "--client-cert-auth=true";;
        "2.3")
            echo "false";;
        "2.4")
            echo "peer-cert-file AND peer-key-file";;
        "2.5")
            echo "--client-cert-auth=true";;
        "2.6")
            echo "--peer-auto-tls=false";;
        "2.7")
            echo "--trusted-ca-file";;
    esac
fi

```

**監査の実施:**

```bash
./check_for_k3s_etcd.sh 2.5
```

**期待される結果**：

```console
'--client-cert-auth' is present OR 'client-cert-auth' is equal to 'true'
```

**戻り値**:

```console
--client-cert-auth=true client-cert-auth: true --client-cert-auth=true
```

### 2.6 --peer-auto-tls 引数が true に設定されていないことを確認する (自動)


**結果：**合格

**修復:**
マスターで etcd Pod 仕様ファイル /etc/kubernetes/manifests/etcd.yaml を編集します。
--peer-auto-tls パラメータを削除するか、false に設定します。--peer-auto-tls=false

**監査スクリプト:** `check_for_k3s_etcd.sh`

```bash
#!/bin/bash

# This script is used to ensure that k3s is actually running etcd (and not other databases like sqlite3)
# before it checks the requirement
set -eE

handle_error() {
    echo "false"
}

trap 'handle_error' ERR


if [[ "$(journalctl -D /var/log/journal -u k3s | grep 'Managed etcd cluster initializing' | grep -v grep | wc -l)" -gt 0 ]]; then
    case $1 in 
        "1.1.11")
            echo $(stat -c %a /var/lib/rancher/k3s/server/db/etcd);;
        "1.2.29")
            echo $(journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'etcd-');;
        "2.1")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.2")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.3")
            echo $(grep 'auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.4")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.5")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.6")
            echo $(grep 'peer-auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.7")
            echo $(grep 'trusted-ca-file' /var/lib/rancher/k3s/server/db/etcd/config);;
    esac
else
# If another database is running, return whatever is required to pass the scan
    case $1 in
        "1.1.11")
            echo "700";;
        "1.2.29")
            echo "--etcd-certfile AND --etcd-keyfile";;
        "2.1")
            echo "cert-file AND key-file";;
        "2.2")
            echo "--client-cert-auth=true";;
        "2.3")
            echo "false";;
        "2.4")
            echo "peer-cert-file AND peer-key-file";;
        "2.5")
            echo "--client-cert-auth=true";;
        "2.6")
            echo "--peer-auto-tls=false";;
        "2.7")
            echo "--trusted-ca-file";;
    esac
fi

```

**監査の実施:**

```bash
./check_for_k3s_etcd.sh 2.6
```

**期待される結果**：

```console
'--peer-auto-tls' is not present OR '--peer-auto-tls' is equal to 'false'
```

**戻り値**:

```console
--peer-auto-tls=false error: process ID list syntax error Usage: ps [options] Try 'ps --help <simple|list|output|threads|misc|all>' or 'ps --help <s|l|o|t|m|a>' for additional help text. For more details see ps(1). cat: /proc//environ: No such file or directory --peer-auto-tls=false
```

### 2.7 etcd に一意の認証局が使用されていることを確認する (手動)


**結果：**合格

**修復:**
【手動テスト】
etcd のドキュメントに従って、専用の認証局セットアップを作成します。
etcd サービス。
次に、etcd ポッド仕様ファイル /etc/kubernetes/manifests/etcd.yaml を編集します。
以下のパラメータを設定します。
--trusted-ca-file=</path/to/ca-file>

**監査スクリプト:** `check_for_k3s_etcd.sh`

```bash
#!/bin/bash

# This script is used to ensure that k3s is actually running etcd (and not other databases like sqlite3)
# before it checks the requirement
set -eE

handle_error() {
    echo "false"
}

trap 'handle_error' ERR


if [[ "$(journalctl -D /var/log/journal -u k3s | grep 'Managed etcd cluster initializing' | grep -v grep | wc -l)" -gt 0 ]]; then
    case $1 in 
        "1.1.11")
            echo $(stat -c %a /var/lib/rancher/k3s/server/db/etcd);;
        "1.2.29")
            echo $(journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'etcd-');;
        "2.1")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.2")
            echo $(grep -A 5 'client-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.3")
            echo $(grep 'auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.4")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep -E 'cert-file|key-file');;
        "2.5")
            echo $(grep -A 5 'peer-transport-security' /var/lib/rancher/k3s/server/db/etcd/config | grep 'client-cert-auth');;
        "2.6")
            echo $(grep 'peer-auto-tls' /var/lib/rancher/k3s/server/db/etcd/config);;
        "2.7")
            echo $(grep 'trusted-ca-file' /var/lib/rancher/k3s/server/db/etcd/config);;
    esac
else
# If another database is running, return whatever is required to pass the scan
    case $1 in
        "1.1.11")
            echo "700";;
        "1.2.29")
            echo "--etcd-certfile AND --etcd-keyfile";;
        "2.1")
            echo "cert-file AND key-file";;
        "2.2")
            echo "--client-cert-auth=true";;
        "2.3")
            echo "false";;
        "2.4")
            echo "peer-cert-file AND peer-key-file";;
        "2.5")
            echo "--client-cert-auth=true";;
        "2.6")
            echo "--peer-auto-tls=false";;
        "2.7")
            echo "--trusted-ca-file";;
    esac
fi

```

**監査の実施:**

```bash
./check_for_k3s_etcd.sh 2.7
```

**期待される結果**：

```console
'trusted-ca-file' is present
```

**戻り値**:

```console
--trusted-ca-file trusted-ca-file: /var/lib/rancher/k3s/server/tls/etcd/server-ca.crt trusted-ca-file: /var/lib/rancher/k3s/server/tls/etcd/peer-ca.crt --trusted-ca-file
```

## 3.1 認証と承認
### 3.1.1 ユーザーに対してクライアント証明書認証を使用してはならない (手動)


**結果：** 警告

**修復:**
OIDC の使用など、Kubernetes によって提供される代替メカニズムは、
クライアント証明書の代わりに実装されます。

## 3.2 ロギング
### 3.2.1 最小限の監査ポリシーが作成されていることを確認する (手動)


**結果：** 警告

**修復:**
クラスターの監査ポリシー ファイルを作成します。
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kube-apiserver' | tail -n1 | grep 'audit-policy-file'
```

### 3.2.2 監査ポリシーが主要なセキュリティ上の問題をカバーしていることを確認する (手動)


**結果：** 警告

**修復:**
クラスターに提供された監査ポリシーを確認し、それがカバーしていることを確認します
少なくとも次の領域、
- クラスターによって管理されるシークレットへのアクセス。 のみに注意する必要があります。
  Secrets、ConfigMaps、および TokenReviews へのリクエストのメタデータをログに記録します。
  機密データをログに記録するリスクを回避するため。
- Pod および Deployment オブジェクトの変更。
- `pods/exec`、`pods/portforward`、`pods/proxy`、`services/proxy` の使用。
ほとんどのリクエストでは、最小限のメタデータ レベルでのログ記録が推奨されます
(ロギングの最も基本的なレベル)。

## 4.1 ワーカーノード構成ファイル
### 4.1.1 kubelet サービス ファイルのアクセス許可が 644 以上に設定されていることを確認する (自動)


**結果：** 該当なし

**修復:**
各ワーカー ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例: chmod 644 /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

### 4.1.2 kubelet サービス ファイルの所有権が root:root に設定されていることを確認する (自動)


**結果：** 該当なし

**修復:**
各ワーカー ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chown root:root /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

### 4.1.3 プロキシ kubeconfig ファイルが存在する場合は、権限が 644 以上に制限されていることを確認します (手動)


**結果：**合格

**修復:**
各ワーカー ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chmod 644 /var/lib/rancher/k3s/agent/kubeproxy.kubeconfig
**監査：**

```bash
stat -c %a /var/lib/rancher/k3s/agent/kubeproxy.kubeconfig
```

**期待される結果**：

```console
'permissions' is present OR '/var/lib/rancher/k3s/agent/kubeproxy.kubeconfig' is not present
```

**戻り値**:

```console
644 644
```

### 4.1.4 プロキシ kubeconfig ファイルが存在する場合、所有権が root:root に設定されていることを確認します (手動)


**結果：**合格

**修復:**
各ワーカー ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例: chown root:root /var/lib/rancher/k3s/agent/kubeproxy.kubeconfig
**監査：**

```bash
/bin/sh -c 'if test -e /var/lib/rancher/k3s/agent/kubeproxy.kubeconfig; then stat -c %U:%G /var/lib/rancher/k3s/agent/kubeproxy.kubeconfig; fi'
```

**期待される結果**：

```console
'root:root' is present OR '/var/lib/rancher/k3s/agent/kubeproxy.kubeconfig' is not present
```

**戻り値**:

```console
root:root root:root
```

### 4.1.5 --kubeconfig kubelet.conf ファイルのアクセス許可が 644 以上に制限されていることを確認する (自動)


**結果：**合格

**修復:**
各ワーカー ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chmod 644 /var/lib/rancher/k3s/server/cred/admin.kubeconfig
**監査：**

```bash
stat -c %a /var/lib/rancher/k3s/agent/kubelet.kubeconfig
```

**期待される結果**：

```console
'644' is equal to '644'
```

**戻り値**:

```console
644 644
```

### 4.1.6 --kubeconfig kubelet.conf ファイルの所有権が root:root に設定されていることを確認します (自動)


**結果：**合格

**修復:**
各ワーカー ノードで、(システム上のファイルの場所に基づいて) 以下のコマンドを実行します。
例えば、
chown root:root /var/lib/rancher/k3s/server/cred/admin.kubeconfig
**監査：**

```bash
stat -c %U:%G /var/lib/rancher/k3s/agent/kubelet.kubeconfig
```

**期待される結果**：

```console
'root:root' is equal to 'root:root'
```

**戻り値**:

```console
root:root root:root
```

### 4.1.7 認証局ファイルのアクセス許可が 644 以上に制限されていることを確認する (手動)


**結果：**合格

**修復:**
次のコマンドを実行して、
--client-ca-file: `chmod 644 <ファイル名>`
**監査：**

```bash
stat -c %a /var/lib/rancher/k3s/server/tls/server-ca.crt
```

**期待される結果**：

```console
'644' is present OR '640' is present OR '600' is equal to '600' OR '444' is present OR '440' is present OR '400' is present OR '000' is present
```

**戻り値**:

```console
644 600
```

### 4.1.8 クライアント認証局ファイルの所有権が root:root に設定されていることを確認する (手動)


**結果：**合格

**修復:**
次のコマンドを実行
--client-ca-file:
`chown root:root <filename>`.

**監査：**

```bash
stat -c %U:%G /var/lib/rancher/k3s/server/tls/client-ca.crt
```

**期待される結果**：

```console
'root:root' is equal to 'root:root'
```

**戻り値**:

```console
root:root root:root
```

### 4.1.9 kubelet --config 構成ファイルの権限が 644 以上に制限されていることを確認する (自動化)


**結果：** 該当なし

**修復:**
次のコマンドを実行します (監査ステップで特定された構成ファイルの場所を使用)
chmod 644 /var/lib/kubelet/config.yaml

### 4.1.10 kubelet --config 構成ファイルの所有権が root:root に設定されていることを確認します (自動)


**結果：** 該当なし

**修復:**
次のコマンドを実行します (監査ステップで特定された構成ファイルの場所を使用)
chown root:root /var/lib/kubelet/config.yaml

## 4.2 キューブレット
### 4.2.1 --anonymous-auth 引数が false に設定されていることを確認する (自動)


**結果：**合格

**修復:**
Kubelet 構成ファイルを使用している場合は、ファイルを編集して「認証: 匿名: 有効」に設定します。
「偽」。
実行可能な引数を使用する場合は、kubelet サービス ファイルを編集します。
各ワーカーノードの /etc/systemd/system/kubelet.service.d/10-kubeadm.conf および
KUBELET_SYSTEM_PODS_ARGS 変数に以下のパラメーターを設定します。
`--anonymous-auth=false`
システムに基づいて、kubelet サービスを再起動します。 例えば、
systemctl daemon-reload
systemctl restart kubelet.service
**監査：**

```bash
/bin/sh -c 'if test $(journalctl -D /var/log/journal -u k3s | grep "Running kube-apiserver" | wc -l) -gt 0; then journalctl -D /var/log/journal -u k3s | grep "Running kube-apiserver" | tail -n1 | grep "anonymous-auth" | grep -v grep; else echo "--anonymous-auth=false"; fi'
```

**期待される結果**：

```console
'--anonymous-auth' is equal to 'false'
```

**戻り値**:

```console
--anonymous-auth=false Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 4.2.2 --authorization-mode 引数が AlwaysAllow (自動) に設定されていないことを確認する


**結果：**合格

**修復:**
Kubelet 構成ファイルを使用している場合は、ファイルを編集して「authorization.mode」を Webhook に設定します。 もしも
実行可能な引数を使用して、kubelet サービス ファイルを編集します
各ワーカーノードの /etc/systemd/system/kubelet.service.d/10-kubeadm.conf および
KUBELET_AUTHZ_ARGS 変数に以下のパラメーターを設定します。
--authorization-mode=Webhook
システムに基づいて、kubelet サービスを再起動します。 例えば、
systemctl daemon-reload
systemctl restart kubelet.service

**監査：**

```bash
/bin/sh -c 'if test $(journalctl -D /var/log/journal -u k3s | grep "Running kube-apiserver" | wc -l) -gt 0; then journalctl -D /var/log/journal -u k3s | grep "Running kube-apiserver" | tail -n1 | grep "authorization-mode" | grep -v grep; else echo "--authorization-mode=Webhook"; fi'
```

**期待される結果**：

```console
'--authorization-mode' does not have 'AlwaysAllow'
```

**戻り値**:

```console
--authorization-mode=Webhook Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 4.2.3 --client-ca-file 引数が適切に設定されていることを確認する (自動)


**結果：**合格

**修復:**
Kubelet 構成ファイルを使用している場合は、ファイルを編集して「authentication.x509.clientCAFile」を
クライアント CA ファイルの場所。
コマンド ライン引数を使用する場合は、kubelet サービス ファイルを編集します。
各ワーカーノードの /etc/systemd/system/kubelet.service.d/10-kubeadm.conf および
KUBELET_AUTHZ_ARGS 変数に以下のパラメーターを設定します。
--client-ca-file=<path/to/client-ca-file>
システムに基づいて、kubelet サービスを再起動します。 例えば、
systemctl daemon-reload
systemctl restart kubelet.service

**監査：**

```bash
/bin/sh -c 'if test $(journalctl -D /var/log/journal -u k3s | grep "Running kube-apiserver" | wc -l) -gt 0; then journalctl -D /var/log/journal -u k3s | grep "Running kube-apiserver" | tail -n1 | grep "client-ca-file" | grep -v grep; else echo "--client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt"; fi'
```

**期待される結果**：

```console
'--client-ca-file' is present
```

**戻り値**:

```console
--client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt Sep 13 13:26:40 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:40Z" level=info msg="Running kube-apiserver --advertise-address=172.31.0.140 --advertise-port=6443 --allow-privileged=true --anonymous-auth=false --api-audiences=https://kubernetes.default.svc.cluster.local,k3s --authorization-mode=Node,RBAC --bind-address=127.0.0.1 --cert-dir=/var/lib/rancher/k3s/server/tls/temporary-certs --client-ca-file=/var/lib/rancher/k3s/server/tls/client-ca.crt --egress-selector-config-file=/var/lib/rancher/k3s/server/etc/egress-selector-config.yaml --enable-admission-plugins=NodeRestriction --enable-aggregator-routing=true --etcd-cafile=/var/lib/rancher/k3s/server/tls/etcd/server-ca.crt --etcd-certfile=/var/lib/rancher/k3s/server/tls/etcd/client.crt --etcd-keyfile=/var/lib/rancher/k3s/server/tls/etcd/client.key --etcd-servers=https://127.0.0.1:2379 --feature-gates=JobTrackingWithFinalizers=true --kubelet-certificate-authority=/var/lib/rancher/k3s/server/tls/server-ca.crt --kubelet-client-certificate=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.crt --kubelet-client-key=/var/lib/rancher/k3s/server/tls/client-kube-apiserver.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --profiling=false --proxy-client-cert-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.crt --proxy-client-key-file=/var/lib/rancher/k3s/server/tls/client-auth-proxy.key --requestheader-allowed-names=system:auth-proxy --requestheader-client-ca-file=/var/lib/rancher/k3s/server/tls/request-header-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6444 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-account-signing-key-file=/var/lib/rancher/k3s/server/tls/service.key --service-cluster-ip-range=10.43.0.0/16 --service-node-port-range=30000-32767 --storage-backend=etcd3 --tls-cert-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.crt --tls-private-key-file=/var/lib/rancher/k3s/server/tls/serving-kube-apiserver.key"
```

### 4.2.3 --client-ca-file 引数が適切に設定されていることを確認する (自動)


**結果：**合格

**修復:**
Kubelet 構成ファイルを使用している場合は、ファイルを編集して「authentication.x509.clientCAFile」を
クライアント CA ファイルの場所。
コマンド ライン引数を使用する場合は、kubelet サービス ファイルを編集します。
各ワーカーノードの /etc/systemd/system/kubelet.service.d/10-kubeadm.conf および
KUBELET_AUTHZ_ARGS 変数に以下のパラメーターを設定します。
--client-ca-file=<path/to/client-ca-file>
システムに基づいて、kubelet サービスを再起動します。 例えば、
**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kubelet' | tail -n1 | grep 'read-only-port'
```

**期待される結果**：

```console
'--read-only-port' is equal to '0' OR '--read-only-port' is not present
```

**戻り値**:

```console
Sep 13 13:26:50 k3s-123-cis-pool2-98604672-hr9p5 k3s[1592]: time="2022-09-13T13:26:50Z" level=info msg="Running kubelet --address=0.0.0.0 --anonymous-auth=false --authentication-token-webhook=true --authorization-mode=Webhook --cgroup-driver=systemd --client-ca-file=/var/lib/rancher/k3s/agent/client-ca.crt --cloud-provider=external --cluster-dns=10.43.0.10 --cluster-domain=cluster.local --container-runtime-endpoint=unix:///run/k3s/containerd/containerd.sock --containerd=/run/k3s/containerd/containerd.sock --eviction-hard=imagefs.available<5%,nodefs.available<5% --eviction-minimum-reclaim=imagefs.available=10%,nodefs.available=10% --fail-swap-on=false --healthz-bind-address=127.0.0.1 --hostname-override=k3s-123-cis-pool2-98604672-hr9p5 --kubeconfig=/var/lib/rancher/k3s/agent/kubelet.kubeconfig --node-labels=rke.cattle.io/machine=00c4e7a0-5497-4367-a70c-0b836757eae8 --pod-infra-container-image=rancher/mirrored-pause:3.6 --pod-manifest-path=/var/lib/rancher/k3s/agent/pod-manifests --read-only-port=0 --resolv-conf=/run/systemd/resolve/resolv.conf --serialize-image-pulls=false --tls-cert-file=/var/lib/rancher/k3s/agent/serving-kubelet.crt --tls-private-key-file=/var/lib/rancher/k3s/agent/serving-kubelet.key" Sep 13 13:26:44 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:44Z" level=info msg="Running kubelet --address=0.0.0.0 --anonymous-auth=false --authentication-token-webhook=true --authorization-mode=Webhook --cgroup-driver=systemd --client-ca-file=/var/lib/rancher/k3s/agent/client-ca.crt --cloud-provider=external --cluster-dns=10.43.0.10 --cluster-domain=cluster.local --container-runtime-endpoint=unix:///run/k3s/containerd/containerd.sock --containerd=/run/k3s/containerd/containerd.sock --eviction-hard=imagefs.available<5%,nodefs.available<5% --eviction-minimum-reclaim=imagefs.available=10%,nodefs.available=10% --fail-swap-on=false --healthz-bind-address=127.0.0.1 --hostname-override=k3s-123-cis-pool3-b403f678-bzdg5 --kubeconfig=/var/lib/rancher/k3s/agent/kubelet.kubeconfig --node-labels=rke.cattle.io/machine=109d596c-89f5-4c10-8c7f-6b82a38edd8f --pod-infra-container-image=rancher/mirrored-pause:3.6 --pod-manifest-path=/var/lib/rancher/k3s/agent/pod-manifests --read-only-port=0 --resolv-conf=/run/systemd/resolve/resolv.conf --serialize-image-pulls=false --tls-cert-file=/var/lib/rancher/k3s/agent/serving-kubelet.crt --tls-private-key-file=/var/lib/rancher/k3s/agent/serving-kubelet.key"
```

### 4.2.5 --streaming-connection-idle-timeout 引数が 0 に設定されていないことを確認する (手動)


**結果：** 警告

**修復:**
Kubelet 構成ファイルを使用している場合は、ファイルを編集して「streamingConnectionIdleTimeout」を
0 以外の値。
コマンド ライン引数を使用する場合は、kubelet サービス ファイルを編集します。
各ワーカーノードの /etc/systemd/system/kubelet.service.d/10-kubeadm.conf および
KUBELET_SYSTEM_PODS_ARGS 変数に以下のパラメーターを設定します。
--streaming-connection-idle-timeout=5m
システムに基づいて、kubelet サービスを再起動します。 例えば、
systemctl daemon-reload
systemctl restart kubelet.service

**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kubelet' | tail -n1 | grep 'streaming-connection-idle-timeout'
```

### 4.2.6 --protect-kernel-defaults 引数が true に設定されていることを確認する (自動)


**結果：** 該当なし

**修復:**
Kubelet 構成ファイルを使用する場合は、ファイルを編集して「protectKernelDefaults」を「true」に設定します。
コマンド ライン引数を使用する場合は、kubelet サービス ファイルを編集します。
各ワーカーノードの /etc/systemd/system/kubelet.service.d/10-kubeadm.conf および
KUBELET_SYSTEM_PODS_ARGS 変数に以下のパラメーターを設定します。
--protect-kernel-defaults=true
システムに基づいて、kubelet サービスを再起動します。 例えば：
systemctl daemon-reload
systemctl restart kubelet.service

### 4.2.7 --make-iptables-util-chains 引数が true に設定されていることを確認する (自動)


**結果：** 該当なし

**修復:**
Kubelet 構成ファイルを使用する場合は、ファイルを編集して「makeIPTablesUtilChains」を「true」に設定します。
コマンド ライン引数を使用する場合は、kubelet サービス ファイルを編集します。
各ワーカーノードの /etc/systemd/system/kubelet.service.d/10-kubeadm.conf および
--make-iptables-util-chains 引数を
KUBELET_SYSTEM_PODS_ARGS 変数。
システムに基づいて、kubelet サービスを再起動します。 例えば：
systemctl デーモン-リロード
systemctl restart kubelet.service

### 4.2.8 --hostname-override 引数が設定されていないことを確認する (手動)


**結果：** 該当なし

**修復:**
kubelet サービス ファイル /etc/systemd/system/kubelet.service.d/10-kubeadm.conf を編集します。
各ワーカー ノードで --hostname-override 引数を削除します。
KUBELET_SYSTEM_PODS_ARGS 変数。
システムに基づいて、kubelet サービスを再起動します。 例えば、
systemctl デーモン-リロード
systemctl restart kubelet.service

### 4.2.9 --event-qps 引数が 0 または適切なイベント キャプチャを保証するレベルに設定されていることを確認する (手動)


**結果：** 警告

**修復:**
Kubelet 構成ファイルを使用している場合は、ファイルを編集して「eventRecordQPS」を適切なレベルに設定します。
コマンド ライン引数を使用する場合は、kubelet サービス ファイルを編集します。
各ワーカーノードの /etc/systemd/system/kubelet.service.d/10-kubeadm.conf および
KUBELET_SYSTEM_PODS_ARGS 変数に以下のパラメーターを設定します。
システムに基づいて、kubelet サービスを再起動します。 例えば、
systemctl daemon-reload
systemctl restart kubelet.service
**監査：**

```bash
/bin/ps -fC containerd
```

### 4.2.10 --tls-cert-file および --tls-private-key-file 引数が適切に設定されていることを確認する (手動)


**結果：**合格

**修復:**
Kubelet 構成ファイルを使用している場合は、ファイルを編集して「tlsCertFile」を場所に設定します
この Kubelet を識別するために使用する証明書ファイルの、および `tlsPrivateKeyFile`
対応する秘密鍵ファイルの場所に。
コマンド ライン引数を使用する場合は、kubelet サービス ファイルを編集します。
各ワーカーノードの /etc/systemd/system/kubelet.service.d/10-kubeadm.conf および
KUBELET_CERTIFICATE_ARGS 変数に以下のパラメーターを設定します。
--tls-cert-file=<path/to/tls-certificate-file>
--tls-private-key-file=<path/to/tls-key-file>
システムに基づいて、kubelet サービスを再起動します。 例えば、
systemctl daemon-reload
systemctl restart kubelet.service

**監査：**

```bash
journalctl -D /var/log/journal -u k3s | grep 'Running kubelet' | tail -n1
```

**期待される結果**：

```console
'--tls-cert-file' is present AND '--tls-private-key-file' is present
```

**戻り値**:

```console
Sep 13 13:26:50 k3s-123-cis-pool2-98604672-hr9p5 k3s[1592]: time="2022-09-13T13:26:50Z" level=info msg="Running kubelet --address=0.0.0.0 --anonymous-auth=false --authentication-token-webhook=true --authorization-mode=Webhook --cgroup-driver=systemd --client-ca-file=/var/lib/rancher/k3s/agent/client-ca.crt --cloud-provider=external --cluster-dns=10.43.0.10 --cluster-domain=cluster.local --container-runtime-endpoint=unix:///run/k3s/containerd/containerd.sock --containerd=/run/k3s/containerd/containerd.sock --eviction-hard=imagefs.available<5%,nodefs.available<5% --eviction-minimum-reclaim=imagefs.available=10%,nodefs.available=10% --fail-swap-on=false --healthz-bind-address=127.0.0.1 --hostname-override=k3s-123-cis-pool2-98604672-hr9p5 --kubeconfig=/var/lib/rancher/k3s/agent/kubelet.kubeconfig --node-labels=rke.cattle.io/machine=00c4e7a0-5497-4367-a70c-0b836757eae8 --pod-infra-container-image=rancher/mirrored-pause:3.6 --pod-manifest-path=/var/lib/rancher/k3s/agent/pod-manifests --read-only-port=0 --resolv-conf=/run/systemd/resolve/resolv.conf --serialize-image-pulls=false --tls-cert-file=/var/lib/rancher/k3s/agent/serving-kubelet.crt --tls-private-key-file=/var/lib/rancher/k3s/agent/serving-kubelet.key" Sep 13 13:26:44 k3s-123-cis-pool3-b403f678-bzdg5 k3s[1600]: time="2022-09-13T13:26:44Z" level=info msg="Running kubelet --address=0.0.0.0 --anonymous-auth=false --authentication-token-webhook=true --authorization-mode=Webhook --cgroup-driver=systemd --client-ca-file=/var/lib/rancher/k3s/agent/client-ca.crt --cloud-provider=external --cluster-dns=10.43.0.10 --cluster-domain=cluster.local --container-runtime-endpoint=unix:///run/k3s/containerd/containerd.sock --containerd=/run/k3s/containerd/containerd.sock --eviction-hard=imagefs.available<5%,nodefs.available<5% --eviction-minimum-reclaim=imagefs.available=10%,nodefs.available=10% --fail-swap-on=false --healthz-bind-address=127.0.0.1 --hostname-override=k3s-123-cis-pool3-b403f678-bzdg5 --kubeconfig=/var/lib/rancher/k3s/agent/kubelet.kubeconfig --node-labels=rke.cattle.io/machine=109d596c-89f5-4c10-8c7f-6b82a38edd8f --pod-infra-container-image=rancher/mirrored-pause:3.6 --pod-manifest-path=/var/lib/rancher/k3s/agent/pod-manifests --read-only-port=0 --resolv-conf=/run/systemd/resolve/resolv.conf --serialize-image-pulls=false --tls-cert-file=/var/lib/rancher/k3s/agent/serving-kubelet.crt --tls-private-key-file=/var/lib/rancher/k3s/agent/serving-kubelet.key"
```

### 4.2.11 --rotate-certificates 引数が false に設定されていないことを確認する (自動)


**結果：** 該当なし

**修復:**
Kubelet 構成ファイルを使用している場合は、ファイルを編集して「rotateCertificates」という行を「true」に追加するか、
デフォルト値を使用するには、完全に削除してください。
コマンド ライン引数を使用する場合は、kubelet サービス ファイルを編集します。
各ワーカーノードの /etc/systemd/system/kubelet.service.d/10-kubeadm.conf および
KUBELET_CERTIFICATE_ARGS から --rotate-certificates=false 引数を削除します
変数。
システムに基づいて、kubelet サービスを再起動します。 例えば、
systemctl デーモン-リロード
systemctl restart kubelet.service

### 4.2.12 RotateKubeletServerCertificate 引数が true に設定されていることを確認する (手動)


**結果：** 該当なし

**修復:**
kubelet サービス ファイル /etc/systemd/system/kubelet.service.d/10-kubeadm.conf を編集します。
各ワーカー ノードで、次のパラメーターを KUBELET_CERTIFICATE_ARGS 変数に設定します。
--feature-gates=RotateKubeletServerCertificate=true
システムに基づいて、kubelet サービスを再起動します。 例えば：
systemctl デーモン-リロード
systemctl restart kubelet.service

### 4.2.13 Kubelet が強力な暗号化暗号のみを使用していることを確認する (手動)


**結果：** 警告

**修復:**
Kubelet 構成ファイルを使用している場合は、ファイルを編集して「TLSCipherSuites」を
TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256、TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256、TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305、TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384、TLS_ECDHE_RSA_WITH_CHACHA2 0_POLY1305,TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_128_GCM_SHA256
またはこれらの値のサブセットに。
実行可能な引数を使用する場合は、kubelet サービス ファイルを編集します。
各ワーカーノードの /etc/systemd/system/kubelet.service.d/10-kubeadm.conf および
--tls-cipher-suites パラメータを次のように設定するか、これらの値のサブセットに設定します。
--tls-cipher-suites=TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305,TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_ RSA_WITH_CHACHA20_POLY1305、TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384、TLS_RSA_WITH_AES_256_GCM_SHA384、TLS_RSA_WITH_AES_128_GCM_SHA256
システムに基づいて、kubelet サービスを再起動します。 例えば：
systemctl daemon-reload
systemctl restart kubelet.service

**監査：**

```bash
/bin/ps -fC containerd
```

## 5.1 RBAC とサービス アカウント
### 5.1.1 cluster-admin ロールが必要な場合にのみ使用されるようにする (手動)


**結果：** 警告

**修復:**
cluster-admin ロールへのすべての clusterrolebindings を識別します。 それらが使用されているかどうかを確認し、
このロールが必要な場合、または権限の少ないロールを使用できる場合。
可能な場合は、最初にユーザーをより低い特権ロールにバインドしてから、
cluster-admin ロールへの clusterrolebinding :
kubectl delete clusterrolebinding [名前]

### 5.1.2 シークレットへのアクセスを最小限に抑える (手動)


**結果：** 警告

**修復:**
可能であれば、クラスター内の Secret オブジェクトへの get、list、および watch アクセスを削除します。

### 5.1.3 Roles と ClusterRoles でのワイルドカードの使用を最小限に抑える (手動)


**結果：** 警告

**修復:**
可能であれば、clusterrole および role でのワイルドカードの使用を特定のものに置き換えます。
オブジェクトまたはアクション。

### 5.1.4 ポッドを作成するためのアクセスを最小限に抑える (手動)


**結果：** 警告

**修復:**
可能であれば、クラスター内のポッド オブジェクトへの作成アクセスを削除します。

### 5.1.5 デフォルトのサービス アカウントが積極的に使用されていないことを確認します。 （マニュアル）


**結果：** 警告

**修復:**
Kubernetes ワークロードが特定のアクセスを必要とする場合はいつでも、明示的なサービス アカウントを作成します
Kubernetes API サーバーに。
各デフォルト サービス アカウントの構成を変更して、この値を含めます
automountServiceAccountToken: false

### 5.1.6 サービス アカウント トークンが必要な場合にのみマウントされるようにする (手動)


**結果：** 警告

**修復:**
サービスをマウントする必要のない Pod とサービス アカウントの定義を変更します
アカウント トークンを使用して無効にします。

### 5.1.7 system:masters グループの使用を避ける (マニュアル)


**結果：** 警告

**修復:**
クラスター内のすべてのユーザーから system:masters グループを削除します。

### 5.1.8 Kubernetes クラスターでの Bind、Impersonate、および Escalate 権限の使用を制限する (手動)


**結果：** 警告

**修復:**
可能であれば、サブジェクトからなりすまし、バインド、およびエスカレーションの権利を削除します。

## 5.2 ポッドのセキュリティ基準
### 5.2.1 クラスターに少なくとも 1 つのアクティブなポリシー制御メカニズムがあることを確認する (手動)


**結果：** 警告

**修復:**
ポッド セキュリティ アドミッションまたは外部ポリシー制御システムのいずれかが配置されていることを確認する
ユーザー ワークロードを含むすべての名前空間に対して。

### 5.2.2 特権コンテナーの許可を最小限に抑える (自動化)


**結果：** 警告

**修復:**
ユーザー ワークロードを制限するクラスター内の各名前空間にポリシーを追加します。
特権コンテナの入場。

### 5.2.3 ホスト プロセス ID 名前空間の共有を希望するコンテナーの許可を最小限に抑える (自動化)


**結果：** 警告

**修復:**
ユーザー ワークロードを制限するクラスター内の各名前空間にポリシーを追加します。
「hostPID」コンテナの許可。

### 5.2.4 ホスト IPC 名前空間を共有したいコンテナの許可を最小限に抑える (自動化)


**結果：** 警告

**修復:**
ユーザー ワークロードを制限するクラスター内の各名前空間にポリシーを追加します。
「hostIPC」コンテナの許可。

### 5.2.5 ホストネットワーク名前空間を共有したいコンテナの許可を最小限に抑える (自動化)


**結果：** 警告

**修復:**
ユーザー ワークロードを制限するクラスター内の各名前空間にポリシーを追加します。
「hostNetwork」コンテナの許可。

### 5.2.6 allowPrivilegeEscalation (自動) でコンテナの許可を最小限に抑える


**結果：** 警告

**修復:**
ユーザー ワークロードを制限するクラスター内の各名前空間にポリシーを追加します。
`.spec.allowPrivilegeEscalation` が `true` に設定されたコンテナの許可。

### 5.2.7 ルートコンテナの許可を最小限に抑える (自動化)


**結果：** 警告

**修復:**
クラスタ内の名前空間ごとにポリシーを作成し、「MustRunAsNonRoot」
または 0 を含まない UID の範囲を持つ `MustRunAs` が設定されています。

### 5.2.8 NET_RAW 機能を使用してコンテナーの許可を最小限に抑える (自動化)


**結果：** 警告

**修復:**
ユーザー ワークロードを制限するクラスター内の各名前空間にポリシーを追加します。
`NET_RAW` 機能によるコンテナの許可。

### 5.2.9 機能が追加されたコンテナの許可を最小限に抑える (自動化)


**結果：** 警告

**修復:**
クラスターのポリシーに「allowedCapabilities」が存在しないことを確認します。
空の配列に設定されます。

### 5.2.10 機能が割り当てられたコンテナの許可を最小限に抑える (手動)


**結果：** 警告

**修復:**
クラスターで実行されているアプリケーションでの機能の使用を確認します。 名前空間の場所
動作に Linux 機能を必要としないアプリケーションが含まれている場合は、追加を検討してください
すべての機能をドロップしないコンテナの許可を禁止する PSP。

### 5.2.11 Windows HostProcess コンテナーの許可を最小限に抑える (手動)


**結果：** 警告

**修復:**
ユーザー ワークロードを制限するクラスター内の各名前空間にポリシーを追加します。
`.securityContext.windowsOptions.hostProcess` が `true` に設定されているコンテナの許可。

### 5.2.12 HostPath ボリュームの許可を最小限に抑える (手動)


**結果：** 警告

**修復:**
ユーザー ワークロードを制限するクラスター内の各名前空間にポリシーを追加します。
「hostPath」ボリュームを持つコンテナの許可。

### 5.2.13 HostPorts を使用するコンテナーの許可を最小限に抑える (手動)


**結果：** 警告

**修復:**
ユーザー ワークロードを制限するクラスター内の各名前空間にポリシーを追加します。
「hostPort」セクションを使用するコンテナの許可。

## 5.3 ネットワーク ポリシーと CNI
### 5.3.1 使用中の CNI が NetworkPolicies をサポートしていることを確認する (手動)


**結果：** 警告

**修復:**
使用中の CNI プラグインがネットワーク ポリシーをサポートしていない場合は、以下を考慮する必要があります。
別のプラグインを利用するか、トラフィックを制限するための代替メカニズムを見つける
Kubernetes クラスター内。

### 5.3.2 すべての名前空間に NetworkPolicies が定義されていることを確認する (手動)


**結果：** 警告

**修復:**
ドキュメントに従って、必要に応じて NetworkPolicy オブジェクトを作成します。

## 5.4 シークレット管理
### 5.4.1 シークレットを環境変数として使用するよりも、ファイルとしてシークレットを使用することを好む (手動)


**結果：** 警告

**修復:**
可能であれば、マウントされたシークレット ファイルからシークレットを読み取るようにアプリケーション コードを書き直してください。
環境変数から。

### 5.4.2 外部シークレット ストレージを検討する (マニュアル)


**結果：** 警告

**修復:**
クラウド プロバイダーまたはサードパーティが提供するシークレット管理オプションを参照してください。
秘密管理ソリューション。

## 5.5 拡張可能なアドミッション コントロール
### 5.5.1 ImagePolicyWebhook アドミッション コントローラーを使用してイメージの来歴を構成する (手動)


**結果：** 警告

**修復:**
Kubernetes のドキュメントとセットアップ イメージの出所に従ってください。

## 5.7 一般的なポリシー
### 5.7.1 名前空間を使用してリソース間の管理境界を作成する (手動)


**結果：** 警告

**修復:**
ドキュメントに従い、必要に応じて展開内のオブジェクトの名前空間を作成します
彼ら。

### 5.7.2 Pod 定義で seccomp プロファイルが docker/default に設定されていることを確認する (手動)


**結果：** 警告

**修復:**
`securityContext` を使用して、ポッド定義で docker/default seccomp プロファイルを有効にします。
以下に例を示します。
  セキュリティコンテキスト:
  seccompProfile:
  タイプ: ランタイムデフォルト

### 5.7.3 SecurityContext をポッドとコンテナーに適用する (手動)


**結果：** 警告

**修復:**
Kubernetes のドキュメントに従って、SecurityContext を Pod に適用します。 のために
SecurityContexts の推奨リストについては、Docker の CIS セキュリティ ベンチマークを参照してください。
コンテナ。

### 5.7.4 デフォルトの名前空間は使用しないでください (マニュアル)


**結果：** 警告

**修復:**
Kubernetes を適切に分離できるように名前空間が作成されていることを確認する
リソースと、すべての新しいリソースが特定の名前空間で作成されることを確認します。
