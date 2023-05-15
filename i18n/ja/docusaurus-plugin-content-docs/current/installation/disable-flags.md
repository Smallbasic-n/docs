---
title: "コンポーネントを無効化するフラグ"
weight: 60
---

cluster-init`でK3sサーバを起動すると、apiサーバ、コントローラマネージャ、スケジューラ、etcdを含む全てのコントロールプレーンコンポーネントが実行されます。しかし、特定のコンポーネントを搭載したサーバノードを実行し、他のコンポーネントを除外することができます。以下のセクションでは、その方法を説明します。

## ETCDのみのノード

このドキュメントは、サーバープロセスに `--cluster-init` フラグを渡すことで、etcd を組み込んだ K3s サーバーを実行することを前提としています。

etcdコンポーネントのみでK3sサーバを実行するには、`--disable-apiserver --disable-controller-manager --disable-scheduler` フラグをk3sに渡せば、etcdのみでサーバノードを実行することになります。：
```bash
curl -fL https://get.k3s.io | sh -s - server --cluster-init --disable-apiserver --disable-controller-manager --disable-scheduler
```

この後、他のノードを正常にクラスタに参加させることができます。

## ETCD を無効化する

これは、例えばフラグ `--disable-etcd` でk3sサーバを実行し、制御コンポーネントのみを持つ別のノードを前のセクションで作成したetcdノードに結合することで達成できます：
```bash
curl -fL https://get.k3s.io | sh -s - server --token <token> --disable-etcd --server https://<etcd-only-node>:6443 
```

最終的には2つのノードができ、そのうちの1つはetcd専用ノード、もう1つはcontrolplane専用ノードとなり、ノードリストを確認すると、以下のように表示されるはずです：
```bash
$ kubectl get nodes
NAME              STATUS   ROLES                       AGE     VERSION
ip-172-31-13-32   Ready    etcd                        5h39m   v1.20.4+k3s1
ip-172-31-14-69   Ready    control-plane,master        5h39m   v1.20.4+k3s1
```

なお、`kubectl`コマンドはapiが動作しているk3sサーバーでのみ実行可能で、etcdのみのノードでは`kubectl`コマンドを実行できない。


### コントロールコンポーネントの再有効化

例えば、etcdのみのノードを全てのコンポーネントを含む完全なk3sサーバーに戻したい場合、次の3つのフラグを削除するだけです。`--disable-apiserver --disable-controller-manager --disable-scheduler`。 この例では、ノード `ip-172-31-13-32` を完全なk3sサーバーに戻すには無効化フラグなしでcurlコマンドを再度実行するだけです：
```bash
curl -fL https://get.k3s.io | sh -s - server --cluster-init
``` 

すべてのコンポーネントが再び起動し、kubectlコマンドを再び実行できることがわかります：
```bash
$ kubectl get nodes
NAME              STATUS   ROLES                       AGE     VERSION
ip-172-31-13-32   Ready    control-plane,etcd,master   5h45m   v1.20.4+k3s1
ip-172-31-14-69   Ready    control-plane,master        5h45m   v1.20.4+k3s1
```

ノード `ip-172-31-13-32` にロールラベルが正しいラベル（control-plane,etcd,master）で再追加されたことに注意してください。

## 設定ファイルを使用して無効化フラグを追加します。

例えば、etcdのみのノードを実行するには、`/etc/rancher/k3s/config.yaml` ファイルに以下のオプションを追加します：
```yaml
---
disable-apiserver: true
disable-controller-manager: true
disable-scheduler: true
cluster-init: true
```

引数なしでcurlコマンドでK3sを起動します：
```bash
curl -fL https://get.k3s.io | sh -
```
## .skip ファイルを使用してコンポーネントを無効化する

`/var/lib/rancher/k3s/server/manifests`の下にある任意のyamlファイル（coredns、traefik、local-storeageなど）に対して、K3sに関連yamlファイルを適用しないようにするための `.skip` ファイルを追加することができます。
例えば、manifestsディレクトリに `traefik.yaml.skip` を追加すると、K3sは `traefik.yaml` をスキップするようになります。
```bash
$ ls /var/lib/rancher/k3s/server/manifests
ccm.yaml      local-storage.yaml  rolebindings.yaml  traefik.yaml.skip
coredns.yaml  traefik.yaml

$ kubectl get pods -A
NAMESPACE     NAME                                     READY   STATUS    RESTARTS   AGE
kube-system   local-path-provisioner-64ffb68fd-xx98j   1/1     Running   0          74s
kube-system   metrics-server-5489f84d5d-7zwkt          1/1     Running   0          74s
kube-system   coredns-85cb69466-vcq7j                  1/1     Running   0          74s
```
