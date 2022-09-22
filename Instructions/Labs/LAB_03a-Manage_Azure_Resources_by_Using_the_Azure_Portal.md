---
lab:
  title: 03a - Azure portal を使用して Azure リソースを管理する
  module: Administer Azure Resources
---

# <a name="lab-03a---manage-azure-resources-by-using-the-azure-portal"></a>ラボ 03a - Azure portal を使用して Azure リソースを管理する
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

You need to explore the basic Azure administration capabilities associated with provisioning resources and organizing them based on resource groups, including moving resources between resource groups. You also want to explore options for protecting disk resources from being accidentally deleted, while still allowing for modifying their performance characteristics and size.

対話型ガイド形式でこのラボをプレビューするには、 **[ここをクリックしてください](https://mslabs.cloudguides.com/en-us/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%204)** 。

## <a name="objectives"></a>目標

このラボでは次の内容を学習します。

+ タスク 1:リソース グループを作成し、リソース グループにリソースをデプロイする
+ タスク 2:リソース グループ間でリソースを移動する
+ タスク 3:リソース ロックを実装してテストする

## <a name="estimated-timing-20-minutes"></a>推定時間:20 分

## <a name="architecture-diagram"></a>アーキテクチャの図

![image](../media/lab03a.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1"></a>演習 1

#### <a name="task-1-create-resource-groups-and-deploy-resources-to-resource-groups"></a>タスク 1:リソース グループを作成し、リソース グループにリソースをデプロイする

このタスクでは、Azure portal を使用してリソース グループを作成し、そのリソース グループにディスクを作成します。

1. [**Azure Portal**](http://portal.azure.com) にサインインします。

1. Azure portal で、「**ディスク**」と検索してそれを選択し、**[+ 作成]** をクリックして、次の設定を指定します。

    |設定|[値]|
    |---|---|
    |サブスクリプション| リソース グループを作成した Azure サブスクリプションの名前 |
    |リソース グループ| 新しいリソース グループ **az104-03a-rg1** の名前 |
    |ディスク名| **az104-03a-disk1** |
    |リージョン| **(米国) 米国東部** |
    |可用性ゾーン| **なし** |
    |変換元の型| **なし** |

    >**注**:リソースを作成するときに、新しいリソース グループを作成するか、既存のリソース グループを使用するかのいずれかを選ぶことができます。

1. ディスク タイプとサイズをそれぞれ **Standard HDD** と **32 GiB** に変更します。

1. **[確認と作成]** をクリックしてから、**[作成]** をクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait until the disk is created. This should take less than a minute.

#### <a name="task-2-move-resources-between-resource-groups"></a>タスク 2:リソース グループ間でリソースを移動する 

このタスクでは、前のタスクで作成したディスク リソースを新しいリソース グループに移動します。 

1. **リソース グループ**を検索して選択します。 

1. **[リソース グループ]** ブレードで、前のタスクで作成した **az104-03a-rg1** リソース グループを表すエントリをクリックします。

1. リソース グループの **[概要]** ブレードで、リソース グループのリソース リストから、新しく作成されたディスクを表すエントリを選択し、ツールバーの **[移動]** をクリックし、ドロップダウン リストで、**[別のリソース グループに移動]** を選択します。

    >**注**:この方法により、複数のリソースを同時に移動できます。 

1. Below the <bpt id="p1">**</bpt>Resource group<ept id="p1">**</ept> text box, click <bpt id="p2">**</bpt>Create new<ept id="p2">**</ept> then type <bpt id="p3">**</bpt>az104-03a-rg2<ept id="p3">**</ept> in the text box. On the Review tab, select the checkbox <bpt id="p1">**</bpt>I understand that tools and scripts associated with moved resources will not work until I update them to use new resource IDs<ept id="p1">**</ept>, and click <bpt id="p2">**</bpt>Move<ept id="p2">**</ept>.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Do not wait for the move to complete but instead proceed to the next task. The move might take about 10 minutes. You can determine that the operation was completed by monitoring activity log entries of the source or target resource group. Revisit this step once you complete the next task.

#### <a name="task-3-implement-resource-locks"></a>タスク 3:リソース ロックの実装

このタスクでは、ディスク リソースを含む Azure リソース グループにリソース ロックを適用します。

1. Azure portal で、「**ディスク**」と検索してそれを選択し、**[+ 作成]** をクリックして、次の設定を指定します。

    |設定|[値]|
    |---|---|
    |サブスクリプション| このラボで使用するサブスクリプションの名前 |
    |リソース グループ| **[新しいリソースグループの作成]** をクリックして、**az104-03a-rg3** という名前を付けます |
    |ディスク名| **az104-03a-disk2** |
    |リージョン| このラボで他のリソース グループを作成した Azure リージョンの名前 |
    |可用性ゾーン| **なし** |
    |変換元の型| **なし** |

1. ディスクのタイプとサイズをそれぞれ **Standard HDD** と **32 GiB** に設定します。

1. **[確認と作成]** をクリックしてから、**[作成]** をクリックします。

1. **[リソースに移動]** をクリックします。

1. [ディスク] の [概要] ページで、リソース グループの名前 **[az104-03a-rg3]** をクリックします。

1. **[az104-03a-rg3** リソース グループ] ブレードで、**[ロック]** をクリックしてから、**[+ 追加]** をクリックして、次の設定を指定します。

    |設定|値|
    |---|---|
    |ロック名| **az104-03a-delete-lock** |
    |ロックの種類| **削除** |
    
1. **[OK]**    

1. **az104-03a-rg3** リソース グループ ブレードの、リソース グループ リソースのリストで **[概要]** をクリックし、このタスクで前に作成したディスクを表すエントリを選択し、ツールバーの **[削除]** をクリックします。 

1. **[選択したリソースをすべて削除しますか?]** というメッセージが表示されたら、**[削除の確認]** テキスト ボックスに「**yes**」と入力し、**[削除]** をクリックします。

1. 失敗した削除操作を通知するエラー メッセージが表示されます。 

    >**注**:エラー メッセージが表示される場合、これは、リソース グループ レベルに適用された削除ロックが原因であると予想されます。

1. **az104-03a-rg3** リソース グループのリソースのリストに戻り、**az104-03a-disk2** リソースを表すエントリをクリックします。 

1. On the <bpt id="p1">**</bpt>az104-03a-disk2<ept id="p1">**</ept> blade, in the <bpt id="p2">**</bpt>Settings<ept id="p2">**</ept> section, click <bpt id="p3">**</bpt>Size + performance<ept id="p3">**</ept>, set the disk type and size to <bpt id="p4">**</bpt>Premium SSD<ept id="p4">**</ept> and <bpt id="p5">**</bpt>64 GiB<ept id="p5">**</ept>, respectively, and click <bpt id="p6">**</bpt>Resize<ept id="p6">**</ept> to apply the change. Verify that the change was successful.

    >**注**:リソース グループ レベルのロックは削除操作にのみ適用されるため、これは予想されることです。 

#### <a name="clean-up-resources"></a>リソースをクリーンアップする

   >リソース グループ間のリソースの移動などを含む、リソース グループに基づいてリソースをプロビジョニングしました。次に、それらを編成に関連する基本的な Azure 管理機能を学習します。

1. **az104-03a-rg3** リソース グループ ブレードに移動し、**[ロック]** ブレードを表示し **[削除]** ロック エントリの右側にある **[削除]** リンクをクリックして **az104-03a-delete-lock** のロックを削除します。

#### <a name="review"></a>確認

このラボでは、次のことを行いました。

- リソース グループを作成し、リソース グループにリソースをデプロイしました
- リソース グループ間でリソースを移動しました
- リソース ロックを実装およびテストしました
