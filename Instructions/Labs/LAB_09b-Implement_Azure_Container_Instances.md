---
lab:
  title: 09b - Azure Container Instances の実装
  module: Module 09 - Serverless Computing
---

# <a name="lab-09b---implement-azure-container-instances"></a>ラボ 09b - Azure Container Instances を実装する
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

Contoso wants to find a new platform for its virtualized workloads. You identified a number of container images that can be leveraged to accomplish this objective. Since you want to minimize container management, you plan to evaluate the use of Azure Container Instances for deployment of Docker images.

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

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Your container will be publicly reachable at dns-name-label.region.azurecontainer.io. If you receive a <bpt id="p1">**</bpt>DNS name label not available<ept id="p1">**</ept> error message, specify a different value.

1. **[次へ: 詳細 >]** をクリックし、 **[コンテナー インスタンスの作成]** ブレードの **[詳細]** タブで、何も変更せずに設定を確認し、 **[レビューと作成]** をクリックし、検証が成功したことを確認して、 **[作成]** をクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the deployment to complete. This should take about 3 minutes.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: While you wait, you may be interested in viewing the <bpt id="p2">[</bpt>code behind the sample application<ept id="p2">](https://github.com/Azure-Samples/aci-helloworld)</ept>. To view it, browse the <ph id="ph1">\\</ph>app folder.

#### <a name="task-2-review-the-functionality-of-the-azure-container-instance"></a>タスク 2:Azure Container Instances の機能を確認する

このタスクでは、コンテナー インスタンスのデプロイを確認します。

1. デプロイ ブレードで、**[リソースに移動]** リンクをクリックします。

1. コンテナー インスタンスの **[概要]** ブレードで、**[状態]** が **[実行中]** と表示されていることを確認します。

1. コンテナー インスタンスの **FQDN** の値をコピーし、新しいブラウザー タブを開き、対応する URL に移動します。

1. **[Azure コンテナー インスタンスへようこそ]** ページが表示されていることを確認します。

1. 新しいブラウザー タブを閉じ、Azure portal に戻り、コンテナー インスタンス ブレードの **[設定]** セクションで **[コンテナー]** をクリックし、**[ログ]** をクリックします。

1. ブラウザーでアプリケーションを表示すると生成される HTTP GET 要求のログを確認します。

#### <a name="clean-up-resources"></a>リソースをクリーンアップする

><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not see unexpected charges.

>Contoso は、仮想化されたワークロード用の新しいプラットフォームを見つけることを目的としています。 

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
