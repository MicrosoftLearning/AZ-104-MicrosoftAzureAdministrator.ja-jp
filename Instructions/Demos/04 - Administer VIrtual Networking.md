---
demo:
  title: 'デモ 04: 仮想ネットワークを管理する'
  module: Administer Virtual Networking
---

# 04 - 仮想ネットワークを管理する

## 仮想ネットワークの構成

このデモには、仮想ネットワークを作成します。

**参照**: [クイックスタート: 仮想ネットワークを作成する - Azure portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## Portal で仮想ネットワークを作成する

1.  Azure portal にサインインし、 **仮想ネットワーク**を検索します。

1.  仮想ネットワークを作成し、基本的な設定を説明します。 少なくとも 1 つのサブネットが作成されていることを確かめます。 

1.  仮想ネットワークが作成されたことを確認します。

## ネットワーク セキュリティ グループを構成する

このデモでは、NSG およびサービス エンドポイントについて説明します。

**参照**: [PaaS リソースへのアクセスを制限する - チュートリアル - Azure portal](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)

**ネットワーク セキュリティ グループの作成**

1. Azure portal にアクセスします。

1.  **ネットワーク セキュリティ グループ**を検索して選択します。

1. NSG を作成し、設定を説明します。 
 
1. 新しい NSG がデプロイされるのを待ちます。

**受信規則と送信規則を詳しく見る**

1. 新しい NSG を選択します。

1. NSG をサブネットまたはネットワーク インターフェイスにどのように関連付けることができるかを説明します。

1. インバウンドおよびアウトバウンド規則の目的について説明します。  

1. 既定のインバウンドおよびアウトバウンド規則を確認します。 

1. 新しい規則を作成し、設定を説明します。 具体的には、サービスの選択 (HTTPS など) と優先順位の設定について説明します。 

## Azure DNS を構成する

このデモでは、Azure DNS について説明します。

**参照**: [チュートリアル: ドメインとサブドメインをホストする - Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)


**DNS ゾーンの作成**

1. Azure portal にアクセスします。

1.  **DNS ゾーン** サービスを検索します。

1. **DNS ゾーン**を作成し、そのゾーンの目的を説明します。 名前には、contoso.internal.com を使用できます。

1.  DNS ゾーンが作成されるのを待ちます。 ページを **更新**する必要がある場合があります。 

**DNS レコード セットを追加する**

**参照**: [チュートリアル: ゾーン内のリソース レコードを参照するエイリアス レコードを作成する](https://learn.microsoft.com/azure/dns/tutorial-alias-rr)

1. ゾーンが作成されたら、 **[+ レコード セット]** を選択します。

1.  **[種類]**   ドロップダウンを使用して、さまざまな種類のレコードを表示します。 さまざまなレコードの種類の使用方法を確認します。 選択するレコードの種類によって、レコードの情報がどのように変わるかに注目してください。

1. 例として **A** レコードを作成します。 
