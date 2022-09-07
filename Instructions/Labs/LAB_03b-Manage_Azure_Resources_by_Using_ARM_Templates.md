---
lab:
  title: 03b - ARM テンプレートを使用して Azure リソースを管理する
  module: Administer Azure Resources
---

# <a name="lab-03b---manage-azure-resources-by-using-arm-templates"></a>ラボ 03b - ARM テンプレートを使用して Azure リソースを管理する
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ
Azure portal を使用して、リソースのプロビジョニングとリソース グループに基づく構成に関連する基本的な Azure 管理機能を確認しました。次は、Azure Resource Manager テンプレートを使用して同等のタスクを実行します。

対話型ガイド形式でこのラボをプレビューするには、 **[ここをクリックしてください](https://mslabs.cloudguides.com/en-us/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%205)** 。

## <a name="objectives"></a>目標

このラボでは、次のことを行います。

+ タスク 1:Azure マネージド ディスクのデプロイのために ARM テンプレートを確認する
+ タスク 2:ARM テンプレートを使用して、Azure マネージド ディスクを作成する
+ タスク 3:マネージド ディスクの ARM テンプレートベースのデプロイを確認する

## <a name="estimated-timing-20-minutes"></a>推定時間:20 分

## <a name="architecture-diagram"></a>アーキテクチャの図

![image](../media/lab03b.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1"></a>演習 1

#### <a name="task-1-review-an-arm-template-for-deployment-of-an-azure-managed-disk"></a>タスク 1:Azure マネージド ディスクのデプロイのために ARM テンプレートを確認する

このタスクでは、Azure Resource Manager テンプレートを使用して Azure ディスク リソースを作成します。

1. [**Azure Portal**](http://portal.azure.com) にサインインします。

1. Azure portal で、**[リソース グループ]** を検索して選択します。 

1. リソース グループのリストで、**az104-03a-rg1** をクリックします。

1. **az104-03a-rg1** リソース グループ ブレードの **[設定]** セクションで、**[デプロイ]** をクリックします。

1. **az104-03a-rg1 - デプロイ** ブレードで、デプロイの一覧の最初のエントリをクリックします。

1. **[Microsoft.ManagedDisk-* XXXXXXXXX* \| 概要]* * ブレードで、 **[テンプレート]** をクリックします。

    >**注**:テンプレートのコンテンツを確認し、ローカル コンピューターに**ダウンロード**して、**ライブラリに追加する**か、もう一度**デプロイ**するオプションがあることに注意してください。

1. **[ダウンロード]** をクリックし、テンプレートとパラメーター ファイルを含む圧縮ファイルをラボ コンピューターの **Downloads** フォルダーに保存します。

1. **[Microsoft.ManagedDisk-* XXXXXXXXX* \| テンプレート]* * ブレードで、 **[入力]** をクリックします。

1. Note the value of the <bpt id="p1">**</bpt>location<ept id="p1">**</ept> parameter. You will need it in the next task.

1. ダウンロードしたファイルの内容をラボ コンピューターの **Downloads** フォルダーに抽出します。

    >**注**:これらのファイルは、 **\\Allfiles\\Labs\\03\\az104-03b-md-template.json** および **\\Allfiles\\Labs\\03\\az104-03b-md-parameters.json** でも入手可能です。
    
1. すべての**ファイル エクスプローラー**ウィンドウを閉じます。

#### <a name="task-2-create-an-azure-managed-disk-by-using-an-arm-template"></a>タスク 2:ARM テンプレートを使用して、Azure マネージド ディスクを作成する

1. Azure portal で、**[カスタム テンプレートのデプロイ]** を見つけて選びます。

1. **[カスタム デプロイ]** ブレードで、**[エディターで独自のテンプレートを作成]** をクリックします。

1. **[テンプレートの編集]** ブレードで、**[ファイルの読み込み]** をクリックし、前の手順でダウンロードした **template.json** ファイルをアップロードします。

1. エディター ウィンドウで、次の行を削除します。

   ```json
   "sourceResourceId": {
       "type": "String"
   },
   ```

   ```json
   "hyperVGeneration": {
       "defaultValue": "V1",
       "type": "String"
   },      
   ```

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: These parameters are removed since they are not applicable to the current deployment. In particular, sourceResourceId, sourceUri, osType, and hyperVGeneration parameters are applicable to creating an Azure disk from an existing VHD file.

1. 変更を **[保存]** します。

1. **[カスタム デプロイ]** ブレードに戻って、**[パラメーターの編集]** をクリックします。 

1. **[テンプレートの編集]** ブレードで、**[ファイルの読み込み]** をクリックし、前の手順でダウンロードした **parameters.json** ファイルをアップロードして、変更を**保存**します。

1. **[カスタム デプロイ]** ブレードに戻って、次の設定を指定します。

    | 設定 | [値] |
    | --- |--- |
    | サブスクリプション | *このラボで使用している Azure サブスクリプションの名前* |
    | リソース グループ | **新しい**リソース グループ **az104-03b-rg1** の名前 |
    | リージョン | この課題で使用しているサブスクリプションで使用できる Azure リージョンの名前 |
    | ディスク名 | **az104-03b-disk1** |
    | 場所 | 前のタスクでメモした場所パラメーターの値 |
    | Sku | **Standard_LRS** |
    | ディスク サイズ GB | **32** |
    | オプションを作成する | **empty** |
    | ディスク暗号化セットのタイプ | **EncryptionAtRestWithPlatformKey** |
    | ネットワーク アクセス ポリシー | **AllowAll** |

1. **[確認および作成]** を選択し、次に **[作成]** を選択します。

1. デプロイが正常に完了したことを確認します。

#### <a name="task-3-review-the-arm-template-based-deployment-of-the-managed-disk"></a>タスク 3:マネージド ディスクの ARM テンプレートベースのデプロイを確認する

1. Azure portal で、「**リソース グループ**」を検索して選択します。 

1. リソース グループのリストで、**az104-03b-rg1** をクリックします。

1. **az104-03b-rg1** リソース グループ ブレードの **[設定]** セクションで、**[デプロイ]** をクリックします。

1. **[az104-03b-rg1 - デプロイ]** ブレードから、デプロイのリストの最初のエントリをクリックし、**[入力]** ブレードと **[テンプレート]** ブレードの内容を確認します。

#### <a name="clean-up-resources"></a>リソースをクリーンアップする

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Do not delete resources you deployed in this lab. You will reference them in the next lab of this module.

#### <a name="review"></a>確認

このラボでは、次のことを行いました。

- Azure マネージド ディスクのデプロイに関する ARM テンプレートを確認しました
- ARM テンプレートを使用して Azure マネージド ディスクを作成しました
- マネージド ディスクの ARM テンプレートベースのデプロイを確認しました
