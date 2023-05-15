---
title: "ボリュームとストレージ"
weight: 30
---

データを保持する必要があるアプリケーションをデプロイする場合、永続ストレージを作成する必要があります。 永続ストレージを使用すると、アプリケーションを実行しているポッドの外部にアプリケーション データを保存できます。 このストレージ プラクティスにより、アプリケーションのポッドに障害が発生した場合でも、アプリケーション データを維持できます。

永続ボリューム (PV) は Kubernetes クラスター内のストレージの一部であり、永続ボリューム要求 (PVC) はストレージの要求です。 PV と PVC の仕組みの詳細については、[ストレージ] に関する Kubernetes の公式ドキュメント (https://kubernetes.io/docs/concepts/storage/volumes/) を参照してください。

このページでは、ローカル ストレージ プロバイダーまたは [Longhorn](#setting-up-longhorn) を使用して永続ストレージをセットアップする方法について説明します。

## K3s ストレージの違いは何ですか?

K3s は、いくつかのオプションのボリューム プラグインとすべての組み込み (「ツリー内」と呼ばれることもある) クラウド プロバイダーを削除します。 これは、より小さなバイナリ サイズを実現し、多くの K3s ユース ケースでは利用できない可能性があるサード パーティのクラウドまたはデータ センターのテクノロジおよびサービスへの依存を回避するために行います。 これを行うことができるのは、それらの削除が Kubernetes のコア機能にも適合性にも影響しないためです。

次のボリューム プラグインが K3s から削除されました。
* cephfs
* fc
* flocker
* git_repo
* glusterfs
* portworx
* quobyte
* rbd
* storageos

両方のコンポーネントには、K3s で使用できるツリー外の代替手段があります。 ) および [クラウド プロバイダー インターフェイス (CPI)](https://kubernetes.io/docs/tasks/administer-cluster/running-cloud-controller/)。

Kubernetes のメンテナーは、ツリー内のボリューム プラグインを CSI ドライバーに積極的に移行しています。 この移行の詳細については、[こちら](https://kubernetes.io/blog/2021/12/10/storage-in-tree-to-csi-migration-status-update/)を参照してください。

## ローカル ストレージ プロバイダの設定
K3s には Rancher の Local Path Provisioner が付属しており、これにより、それぞれのノードのローカル ストレージを使用して、すぐに永続的なボリューム クレームを作成することができます。 以下に簡単な例を示します。 詳細については、公式ドキュメント [こちら] (https://github.com/rancher/local-path-provisioner/blob/master/README.md#usage) を参照してください。

hostPath に基づく永続的なボリューム要求と、それを利用するポッドを作成します。
### pvc.yaml

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-path-pvc
  namespace: default
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: local-path
  resources:
    requests:
      storage: 2Gi
```

### pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-test
  namespace: default
spec:
  containers:
  - name: volume-test
    image: nginx:stable-alpine
    imagePullPolicy: IfNotPresent
    volumeMounts:
    - name: volv
      mountPath: /data
    ports:
    - containerPort: 80
  volumes:
  - name: volv
    persistentVolumeClaim:
      claimName: local-path-pvc
```

yamlを適用します。:

```bash
kubectl create -f pvc.yaml
kubectl create -f pod.yaml
```

PV,PVCが作成されたことを確認します。:

```bash
kubectl get pv
kubectl get pvc
```

ステータスは、それぞれの Bound である必要があります。

## Longhorn のセットアップ
:::caution

Longhorn は ARM32 をサポートしていません。
::: 


K3s は、Kubernetes 用のオープンソース分散ブロック ストレージ システムである [Longhorn](https://github.com/longhorn/longhorn) をサポートしています。

以下に簡単な例を示します。 詳細については、[公式ドキュメント](https://longhorn.io/docs/latest/)を参照してください。

longhorn.yaml を適用して Longhorn をインストールします。
```bash
kubectl apply -f https://raw.githubusercontent.com/longhorn/longhorn/master/deploy/longhorn.yaml
```

Longhorn は名前空間「longhorn-system」にインストールされます。

yaml を適用して PVC とポッドを作成します。
```bash
kubectl create -f pvc.yaml
kubectl create -f pod.yaml
```

### pvc.yaml

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: longhorn-volv-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: longhorn
  resources:
    requests:
      storage: 2Gi
```

### pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-test
  namespace: default
spec:
  containers:
  - name: volume-test
    image: nginx:stable-alpine
    imagePullPolicy: IfNotPresent
    volumeMounts:
    - name: volv
      mountPath: /data
    ports:
    - containerPort: 80
  volumes:
  - name: volv
    persistentVolumeClaim:
      claimName: longhorn-volv-pvc
```

PV と PVC が作成されたことを確認します。
```bash
kubectl get pv
kubectl get pvc
```

ステータスは、それぞれの Bound である必要があります。