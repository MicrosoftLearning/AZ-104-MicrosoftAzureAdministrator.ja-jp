---
lab:
  title: 09b - Azure Container Instances の実装
  module: Administer Serverless Computing
---

# <a name="lab-09b---implement-azure-container-instances"></a>ラボ 09b - Azure Container Instances を実装する
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

Contoso は、仮想化されたワークロード用の新しいプラットフォームを見つけることを目的としています。 この目的を達成するために使用できるコンテナー イメージの数を特定しました。 コンテナー管理を最小限に抑えるため、Docker イメージのデプロイに Azure Container Instances の使用を評価する予定です。

                **メモ:** このラボをご自分のペースでクリックして進めることができる、 **[ラボの対話型シミュレーション](https://mslabs.cloudguides.com/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%2014)** が用意されています。 対話型シミュレーションとホストされたラボの間に若干の違いがある場合がありますが、示されている主要な概念とアイデアは同じです。 

## <a name="objectives"></a>目標

このラボでは、次のことを行います。

- タスク 1:Azure Container Instance を使用して Docker イメージをデプロイする
- タスク 2:Azure Container Instances の機能を確認する

## <a name="estimated-timing-20-minutes"></a>推定時間:20 分

## <a name="architecture-diagram"></a>アーキテクチャの図

![image](../media/lab09b.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1"></a>演習 1

#### <a name="task-1-deploy-a-docker-image-by-using-the-azure-container-instance"></a>タスク 1:Azure Container Instance を使用して Docker イメージをデプロイする

このタスクでは、Web アプリケーションの新しいコンテナー インスタンスを作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal で **[コンテナー インスタンス]** を検索し、**[コンテナー インスタンス]** ブレードで **[+ 作成]** をクリックします。

1. **[コンテナー インスタンスの作成]** ブレードの **[基本]** タブで、次の設定を指定します (他の設定は既定値のままにします)。

    | 設定 | 値 |
    | ---- | ---- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | 新しいリソース グループ **az104-09b-rg1** の名前 |
    | コンテナー名 | **az104-9b-c1** |
    | リージョン | Azure Container Instances をプロビジョニングできるリージョンの名前 |
    | イメージのソース | **クイックスタートのイメージ** |
    | Image | **mcr.microsoft.com/azuredocs/aci-helloworld:latest (Linux)** |

1. **[次へ: ネットワーク >]** をクリックし、 **[コンテナー インスタンスの作成]** ブレードの **[ネットワーク]** タブで、次の設定を指定します (他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | DNS 名ラベル | 有効な、グローバルに一意の DNS ホスト名 |

    >**注**:コンテナーは、dns-name-label.region.azurecontainer.io でパブリックからアクセスできるようになります。 **[DNS 名ラベルは使用できません]** というエラー メッセージを受け取った場合は、別の DNS 名ラベルを指定します。

1. **[次へ: 詳細 >]** をクリックし、 **[コンテナー インスタンスの作成]** ブレードの **[詳細]** タブで、何も変更せずに設定を確認し、 **[レビューと作成]** をクリックし、検証が成功したことを確認して、 **[作成]** をクリックします。

    >**注**: デプロイが完了するまで待ちます。 これには 3 分ほどかかります。

    >**注**:待っているあいだに、この[サンプル アプリケーションに隠れたコード](https://github.com/Azure-Samples/aci-helloworld)を見ることに興味を持つかもしれません。 それを参照するには、\\app フォルダーを開きます。

#### <a name="task-2-review-the-functionality-of-the-azure-container-instance"></a>タスク 2:Azure Container Instances の機能を確認する

このタスクでは、コンテナー インスタンスのデプロイを確認します。

1. デプロイ ブレードで、**[リソースに移動]** リンクをクリックします。

1. コンテナー インスタンスの **[概要]** ブレードで、**[状態]** が **[実行中]** と表示されていることを確認します。

1. コンテナー インスタンスの **FQDN** の値をコピーし、新しいブラウザー タブを開き、対応する URL に移動します。

1. **[Azure コンテナー インスタンスへようこそ]** ページが表示されていることを確認します。

1. 新しいブラウザー タブを閉じ、Azure portal に戻り、コンテナー インスタンス ブレードの **[設定]** セクションで **[コンテナー]** をクリックし、**[ログ]** をクリックします。

1. ブラウザーでアプリケーションを表示すると生成される HTTP GET 要求のログを確認します。

#### <a name="clean-up-resources"></a>リソースをクリーンアップする

>**注**:新規に作成し、使用しなくなったすべての Azure リソースを削除することを忘れないでください。 使用していないリソースを削除することで、予期しない料金が発生しなくなります。

>**注**:ラボのリソースをすぐに削除できなくても心配する必要はありません。 リソースに依存関係が存在し、削除に時間がかかる場合があります。 リソースの使用状況を監視することは管理者の一般的なタスクであるため、ポータルでリソースを定期的にチェックして、クリーンアップの進捗を確認するようにしてください。 

1. Azure portal で、**[Cloud Shell]** ペイン内に **PowerShell** セッションを開きます。

1. 次のコマンドを実行して、このモジュールのラボ全体で作成したすべてのリソース グループのリストを表示します。

   ```powershell
   Get-AzResourceGroup -Name 'az104-09b*'
   ```

1. 次のコマンドを実行して、このモジュールのラボ全体を通して作成したすべてのリソース グループを削除します。

   ```powershell
   Get-AzResourceGroup -Name 'az104-09b*' | Remove-AzResourceGroup -Force -AsJob
   ```

    >**注**:このコマンドは非同期で実行されるため (-AsJob パラメーターによって決定されます)、同じ PowerShell セッション内で直後に別の PowerShell コマンドを実行できますが、リソース グループが実際に削除されるまでに数分かかります。

#### <a name="review"></a>確認

このラボでは、次のことを行いました。

- Azure コンテナー インスタンス を使って ドッカー イメージ をデプロイしました
- Azure コンテナー インスタンスの機能をレビュー確認しました
