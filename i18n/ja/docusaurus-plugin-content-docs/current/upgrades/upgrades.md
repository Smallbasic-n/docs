---
title: "アップグレード"
weight: 25
---

### K3s クラスターのアップグレード

[手動アップグレード](manual.md) では、クラスターを手動でアップグレードするためのいくつかの手法について説明しています。 また、[Terraform](https://www.terraform.io/) などのサードパーティの Infrastructure-as-Code ツールによるアップグレードの基礎としても使用できます。

[Automated Upgrades](automated.md) では、Rancher の [system-upgrade-controller](https://github.com/rancher/system-upgrade-controller) を使用して Kubernetes ネイティブの自動アップグレードを実行する方法について説明しています。

### バージョン固有の注意事項

- **Traefik:** Traefik が無効になっていない場合、K3s バージョン 1.20 以前は Traefik v1 をインストールしますが、K3s バージョン 1.21 以降は Traefik v2 をインストールします (v1 がまだ存在しない場合)。 古い Traefik v1 から Traefik v2 にアップグレードするには、[Traefik ドキュメント](https://doc.traefik.io/traefik/migration/v1-to-v2/) を参照し、[移行ツール](https ://github.com/traefik/traefik-migration-tool)。

- **K3s ブートストラップ データ:** 外部 SQL データストアを使用する HA 構成で K3s を使用していて、サーバー (コントロール プレーン) ノードが「--token」CLI フラグで起動されていない場合、 トークンを指定せずに追加の K3s サーバーをクラスターに追加できます。 バックアップから復元するときに必要になるため、このトークンのコピーを必ず保持してください。 以前は、K3s は、外部 SQL データストアを使用するときにトークンの使用を強制しませんでした。
     - 影響を受けるバージョンは <= v1.19.12+k3s1、v1.20.8+k3s1、v1.21.2+k3s1 です。 パッチが適用されたバージョンは、v1.19.13+k3s1、v1.20.9+k3s1、v1.21.3+k3s1 です。

     - 次のように、クラスターに既に参加しているサーバーからトークン値を取得できます。
```bash
cat /var/lib/rancher/k3s/server/token
```

- **実験的な Dqlite:** 実験的な埋め込み Dqlite データ ストアは、K3s v1.19.1 で廃止されました。 実験的な Dqlite から実験的な組み込み etcd へのアップグレードはサポートされていないことに注意してください。 アップグレードを試みても成功せず、データが失われます。