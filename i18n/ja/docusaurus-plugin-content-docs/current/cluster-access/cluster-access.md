---
title: クラスタアクセス
weight: 21
---

Kubernetesクラスタへのアクセスを設定するには、`/etc/rancher/k3s/k3s.yaml`に格納されているkubeconfigファイルが使用されます。kubectlやhelmなどのアップストリームKubernetesコマンドラインツールをインストールした場合は、正しいkubeconfigパスで設定する必要があります。これは、環境変数 `KUBECONFIG` をエクスポートするか、`--kubeconfig` コマンドラインフラグを起動することによって行うことができます。詳しくは以下の例を参照してください。

KUBECONFIG 環境変数を利用する：
```bash
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
kubectl get pods --all-namespaces
helm ls --all-namespaces
```

または、コマンドでkubeconfigファイルの場所を指定します：
```bash
kubectl --kubeconfig /etc/rancher/k3s/k3s.yaml get pods --all-namespaces
helm --kubeconfig /etc/rancher/k3s/k3s.yaml ls --all-namespaces
```

### kubectlで外部からクラスターにアクセスする

クラスタ外にあるマシンに `/etc/rancher/k3s/k3s.yaml` を `~/.kube/config` としてコピーします。そして、`server`フィールドの値をK3sサーバーのIPまたは名前に置き換えます。これで `kubectl` が K3s クラスタを管理できるようになりました。