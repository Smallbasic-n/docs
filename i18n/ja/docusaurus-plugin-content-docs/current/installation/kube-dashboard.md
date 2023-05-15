---
title: "Kubernetes ダッシュボード"
weight: 60
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

このインストール ガイドは、[Kubernetes ダッシュボード](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) を K3s にデプロイして構成するのに役立ちます。

### Kubernetes ダッシュボードのデプロイ
```bash
GITHUB_URL=https://github.com/kubernetes/dashboard/releases
VERSION_KUBE_DASHBOARD=$(curl -w '%{url_effective}' -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e 's|.*/||')
sudo k3s kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml
```

### ダッシュボードの RBAC 構成

> **重要:** このガイドで作成された「admin-user」には、ダッシュボードでの管理者権限が付与されます。

次のリソース マニフェスト ファイルを作成します。
`dashboard.admin-user.yml`
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

`dashboard.admin-user-role.yml`
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

「admin-user」構成をデプロイします。
```bash
sudo k3s kubectl create -f dashboard.admin-user.yml -f dashboard.admin-user-role.yml
```

### Bearerトークンを取得する
<Tabs>
<TabItem value="v1.24以降">

```bash
sudo k3s kubectl -n kubernetes-dashboard create token admin-user
```
</TabItem>
<TabItem value="v1.23以前">

```bash
sudo k3s kubectl -n kubernetes-dashboard describe secret admin-user-token | grep '^token'
```

</TabItem>
</Tabs>


### ダッシュボードへのローカル アクセス

ダッシュボードにアクセスするには、K3s クラスターへの安全なチャネルを作成する必要があります。
```bash
sudo k3s kubectl proxy
```

ダッシュボードは次の場所からアクセスできるようになりました:

* http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
* `admin-user` ベアラー トークンで `Sign In`

#### 詳細: ダッシュボードへのリモート アクセス

ダッシュボードのドキュメントを参照してください: [ポート フォワーディング](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) を使用してクラスター内のアプリケーションにアクセスします。

### ダッシュボードのアップグレード
```bash
sudo k3s kubectl delete ns kubernetes-dashboard
GITHUB_URL=https://github.com/kubernetes/dashboard/releases
VERSION_KUBE_DASHBOARD=$(curl -w '%{url_effective}' -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e 's|.*/||')
sudo k3s kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml -f dashboard.admin-user.yml -f dashboard.admin-user-role.yml
```

### ダッシュボードと管理者ユーザー構成の削除

```bash
sudo k3s kubectl delete ns kubernetes-dashboard
sudo k3s kubectl delete clusterrolebinding kubernetes-dashboard
sudo k3s kubectl delete clusterrole kubernetes-dashboard
```
