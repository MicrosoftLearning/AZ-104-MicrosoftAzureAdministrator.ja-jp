---
lab:
  title: 'ラボ 09b: Azure Container Instances を実装する'
  module: Administer PaaS Compute Options
---

# ラボ 09b - Azure Container Instances を実装する

## ラボ概要

このラボでは、Azure Container Instances を実装してデプロイする方法について説明します。

このラボでは Azure サブスクリプションが必要です。 お使いのサブスクリプションの種類により、このラボの機能が使用できるかどうかに影響する可能性があります。 リージョンを変更できますが、手順は**米国東部**を使って作成されています。

## 推定時間:15 分

## ラボのシナリオ

あなたの組織には、オンプレミス データ センターの仮想マシン上で実行されている Web アプリケーションがあります。 組織は、すべてのアプリケーションをクラウドに移行したいと考えていますが、多数のサーバーを管理したくありません。 あなたは、Azure Container Instances と Docker を評価することにしました。 
## 対話型ラボ シミュレーション

このトピックで役に立つ対話型ラボ シミュレーションがあります。 シミュレーションを使うと、同様のシナリオを自分のペースでクリックして進めることができます。 対話型シミュレーションとこのラボには違いがありますが、主要な概念の多くは同じです。 Azure サブスクリプションは必要ありません。

+ 「[Azure Container Instances をデプロイする](https://mslearn.cloudguides.com/en-us/guides/AZ-900%20Exam%20Guide%20-%20Azure%20Fundamentals%20Exercise%203)」。 Azure Container Instances を使って Docker コンテナーを作成、構成、デプロイします。
  
+ 「[Azure Container Instances を実装する](https://mslabs.cloudguides.com/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%2014)」。  Azure Container Instances を使って Docker イメージをデプロイします。 

## 職務スキル

- タスク 1:Docker イメージを使って Azure Container Instances をデプロイする。
- タスク 2:Azure Container Instance のデプロイをテストして確認する。


## アーキテクチャの図

![タスクの図。](../media/az104-lab09b-aci-architecture.png)

## タスク 1: Docker イメージを使用して Azure Container Instances をデプロイする

このタスクでは、Docker イメージを使って単純な Web アプリケーションを作成します。 Docker とは、コンテナーという分離された環境でアプリケーションをパッケージ化して実行する機能を提供するプラットフォームです。 Azure Container Instances は、コンテナー イメージのコンピューティング環境を提供します。

1. **Azure portal** - `https://portal.azure.com` にサインインします。

1. Azure portal で `Container instances` を検索して選び、**[コンテナー インスタンス]** ブレードで **[+ 作成]** をクリックします。

1. **[コンテナー インスタンスの作成]** ブレードの **[基本]** タブで、次の設定を指定します (他の設定は既定値のままにします)。

    | 設定 | 値 |
    | ---- | ---- |
    | サブスクリプション | お使いの "Azure サブスクリプション" を選択します |
    | リソース グループ | `az104-rg9` (必要に応じて、**[新規作成]** を選びます) |
    | コンテナー名 | `az104-c1` |
    | リージョン | **米国東部** (またはお近くの使用可能なリージョン)|
    | イメージのソース | **クイックスタートのイメージ** |
    | Image | **mcr.microsoft.com/azuredocs/aci-helloworld:latest (Linux)** |

1. **[次へ: ネットワーク >]** をクリックし、次の設定を指定します (他の設定は既定値のままにしておきます)。

    | 設定 | 値 |
    | --- | --- |
    | DNS 名ラベル | 有効な、グローバルに一意の DNS ホスト名 |

    >**注**:コンテナーは、dns-name-label.region.azurecontainer.io でパブリックからアクセスできるようになります。 **[DNS 名ラベルは使用できません]** というエラー メッセージを受け取った場合は、別の DNS 名ラベルを指定します。

1. **[次へ: 詳細 >]** をクリックし、変更を加えずに設定を確認します。

 1. **[確認および作成]** をクリックし、検証が成功したことを確認して、**[作成]** を選びます。

    >**注**: デプロイが完了するまで待ちます。 これには 2 分から 3 分かかります。

    >**注**:待っているあいだに、この[サンプル アプリケーションに隠れたコード](https://github.com/Azure-Samples/aci-helloworld)を見ることに興味を持つかもしれません。 コードを表示するには、\\app フォルダーを参照します。

## タスク 2:Azure Container Instance のデプロイをテストして確認する 

このタスクでは、コンテナー インスタンスのデプロイを確認します。 既定では、Azure Container Instance はポート 80 経由でアクセスできます。 インスタンスがデプロイされたら、前のタスクで指定した DNS 名を使ってコンテナーに移動できます。

1. デプロイ ブレードで、**[リソースに移動]** リンクをクリックします。

1. コンテナー インスタンスの **[概要]** ブレードで、**[状態]** が **[実行中]** と表示されていることを確認します。

1. コンテナー インスタンスの **FQDN** の値をコピーし、新しいブラウザー タブを開き、対応する URL に移動します。

     ![ポータルの ACI の概要ページを示すスクリーンショット。](../media/az104-lab09b-aci-overview.png)

1. **[Azure コンテナー インスタンスへようこそ]** ページが表示されていることを確認します。 ページを数回更新してログ エントリを作成し、ブラウザー タブを閉じます。  

1. コンテナー インスタンス ブレードの **[設定]** セクションで、**[コンテナー]** をクリックし、**[ログ]** をクリックします。

1. ブラウザーでアプリケーションを表示すると生成される HTTP GET 要求のログを確認します。
   
## リソースのクリーンアップ

**自分のサブスクリプション**を使っている場合は、少し時間を取り、ラボ リソースを削除してください。 こうすることで、リソースが解放され、コストを最小限に抑えられます。 ラボ リソースを削除する最も簡単な方法は、ラボ リソース グループを削除することです。 

+ Azure portal の場合、リソース グループを選び、**[リソース グループの削除]**、**[リソース グループ名を入力してください]** の順に選び、**[削除]** をクリックします。
+ Azure PowerShell を使う場合は `Remove-AzResourceGroup -Name resourceGroupName`。
+ CLI を使う場合は `az group delete --name resourceGroupName`。


## 要点

以上でラボは完了です。 このラボの要点は次のとおりです。 

+ Azure Container Instances (ACI) は、Microsoft Azure パブリック クラウドにコンテナーをデプロイできるサービスです。
+ ACI では、基になるインフラストラクチャをプロビジョニングまたは管理する必要はありません。
+ ACI は、Linux コンテナーと Windows コンテナーの両方をサポートします。
+ ACI 上のワークロードは、通常、何らかのプロセスまたはトリガーによって開始および停止され、通常、有効期間は短期です。 

## 自習トレーニングでさらに学習する

+ [Azure Container Instances でコンテナー イメージを実行する](https://learn.microsoft.com/training/modules/create-run-container-images-azure-container-instances/)。 Azure Container Instances を使ってコンテナーを迅速にデプロイする方法、環境変数を設定する方法、コンテナーの再起動ポリシーを指定する方法について説明します。

    
