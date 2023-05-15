---
title: FAQ
weight: 60
---

FAQは定期的に更新され、K3sについてユーザーから最も頻繁に寄せられる質問に答えるように設計されています。

### K3sはKubernetesの代わりとして適切ですか？

K3sはCNCF認定のKubernetesディストリビューションであり、標準的なKubernetesクラスタに必要なすべてのことを行うことができます。より軽量化されたバージョンに過ぎません。詳細は[main](../introduction.md)のdocsページを参照してください。

### Traefikの代わりに自分自身のIngressを使うにはどうしたらいいですか？

単に `--disable=traefik` で K3s サーバを起動し、あなたの ingress をデプロイしてください。

### K3sはWindowsをサポートしていますか？

現時点では、K3sはWindowsをネイティブにサポートしていませんが、将来的にはこのアイデアを受け入れる予定です。

### ソースからビルドするにはどうすればよいですか？

K3sの[BUILDING.md](https://github.com/k3s-io/k3s/blob/master/BUILDING.md)を参照してください。

### K3sのログはどこにあるのですか？

インストールスクリプトは、OSがsystemdまたはopenrcを使用しているかどうかを自動検出し、サービスを開始します。

* コマンドラインから実行した場合、ログは標準出力と標準エラーに送信されます。
* openrcで実行した場合、ログは `/var/log/k3s.log` に作成されます。
* Systemdで動作している場合、ログはJournaldに送られ、`journalctl -u k3s`で見ることができます。

### DockerでK3sを実行することはできますか？

はい、DockerでK3sを実行する方法は複数あります。詳細は[Advanced Options](../advanced/advanced.md#running-k3s-in-docker)を参照してください。

### K3sのServer TokensとAgent Tokensの違いは何ですか？

K3sでは、`K3S_TOKEN`と`K3S_AGENT_TOKEN`の2種類のトークンがあります。

`K3S_TOKEN`： HTTP configリソースを提供するためにサーバーが必要とするキーを定義します。これらのリソースは、K3s HAクラスタに参加する前に、他のサーバーから要求されます。K3S_AGENT_TOKEN` が定義されていない場合、エージェントはこのトークンを使用して、クラスタに参加するために必要なHTTPリソースにアクセスします。なお、このトークンは、データベース内の重要なコンテンツ（ブートストラップデータなど）の暗号化キーを生成するためにも使用される。

`k3s_agent_token`： オプション。サーバーがエージェントにHTTP設定リソースを提供する際に必要となるキーを定義します。定義されていない場合、エージェントは `K3S_TOKEN` を要求します。K3S_AGENT_TOKEN` を定義しておくと、エージェントが `K3S_TOKEN` を知らなくても済むようになります。このキーはデータの暗号化にも使用されます。

`K3S_TOKEN`が定義されていない場合、最初のK3sサーバーは初期起動時にランダムなトークンを生成します。その結果は `/var/lib/rancher/k3s/server/token` の内容の一部となります。例えば, `K1070878408e06a827960208f84ed18b65fa10f27864e71a57d9e053c4caff8504b::server:df54383b5659b9280aa1e73e60ef78fc`. この例のトークンは `df54383b5659b9280aa1e73e60ef78fc` です。K10`というプレフィックスを持つフルフォーマットには、クラスタのCA証明書のハッシュが含まれています。このハッシュは、ノードが正しいクラスタに参加しているか、参加プロセス中に中間者攻撃を受けないかを確認するために使用することができます。このフルトークンは、クラスタの初期起動前、クラスタCAが生成される前に生成することはできません。
### 問題が発生しました、どこで助けを得ることができますか？
 
K3sのデプロイに問題がある場合、次のようにします：

1) [Known Issues](../known-issues/known-issues.md) ページをチェックします。

2) [追加のOS準備](../advanced/advanced.md#additional-os-preparations)を解決したことを確認する。k3s check-config`を実行し、パスしていることを確認します。

3) [K3s GitHub existing issues](https://github.com/k3s-io/k3s/issues)を検索して、あなたの問題にマッチするものを探す。

4) [Rancher Slack](https://slack.rancher.io/) K3sチャンネルに参加し、ヘルプを得る。

5) K3s Githubの[New Issue](https://github.com/k3s-io/k3s/issues/new/choose)に、あなたのセットアップと発生している問題を記述して送信します。