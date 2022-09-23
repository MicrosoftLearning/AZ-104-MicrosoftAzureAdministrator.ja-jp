---
lab:
  title: 02b - Azure Policy を介してガバナンスを管理する
  module: Administer Governance and Compliance
---

# <a name="lab-02b---manage-governance-via-azure-policy"></a>ラボ 02b - Azure Policy を介してガバナンスを管理する
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

Contoso の Azure リソースの管理を強化するために、次の機能を実装する任務を負いました。

- Cloud Shell ストレージ アカウントなどインフラストラクチャ リソースのみを含むリソース グループにタグを付ける

- 適切にタグ付けされたインフラストラクチャ リソースのみを、インフラストラクチャ リソース グループに追加できるようにする

- 非準拠リソースを修復する 

## <a name="objectives"></a>目標

このラボでは次の内容を学習します。

+ タスク 1:Azure portal を介して タグを作成し、割り当てる
+ タスク 2:Azure Policy を使用してタグ付けを強制する
+ タスク 3:Azure Policy を使用してタグ付けを適用する

## <a name="estimated-timing-30-minutes"></a>推定時間:30 分

## <a name="architecture-diagram"></a>アーキテクチャの図

![image](../media/lab02b.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1"></a>演習 1

#### <a name="task-1-assign-tags-via-the-azure-portal"></a>タスク 1:Azure portal を使用してタグを割り当てる

このタスクでは、Azure portal を介してタグを作成し、Azure リソース グループに割り当てます。

1. Azure portal 内の **[Cloud Shell]** で **PowerShell** セッションを開始します。

    >**注**: **Cloud Shell** の初回起動時に **"ストレージがマウントされていません"** というメッセージが表示された場合は、このラボで使用しているサブスクリプションを選択し、**[ストレージの作成]** を選択します。 

1. [Cloud Shell] ウィンドウで次のコマンドを実行し、Cloud Shell で使用されるストレージ アカウントの名前を確認します。

   ```powershell
   df
   ```

1. コマンドの出力で、Cloud Shell ホーム ドライブ マウントを指定する完全修飾パスの最初の部分に注意してください (ここでは `xxxxxxxxxxxxxx` とマークされています)。

   ```
   //xxxxxxxxxxxxxx.file.core.windows.net/cloudshell   (..)  /usr/csuser/clouddrive
   ```

1. Azure portal で、「**ストレージ アカウント**」を検索して選択し、ストレージ アカウントのリストで、前の手順で特定したストレージ アカウントを表すエントリをクリックします。

1. [ストレージ アカウント] ブレードで、ストレージ アカウントを含むリソース グループの名前を表すリンクをクリックします。

    **注**: ストレージ アカウントがどのリソース グループにあるかをメモします。後でラボで必要になります。

1. [リソース グループ] ブレードで、**[タグ]** の横にある **[編集]** をクリックして新しいタグを作成します。

1. 次の設定でタグを作成し、変更を適用します。

    | 設定 | 値 |
    | --- | --- |
    | 名前 | **ロール** |
    | [値] | **インフラストラクチャ** |

1. Navigate back to the storage account blade. Review the <bpt id="p1">**</bpt>Overview<ept id="p1">**</ept> information and note that the new tag was not automatically assigned to the storage account. 

#### <a name="task-2-enforce-tagging-via-an-azure-policy"></a>タスク 2:Azure Policy を使用してタグ付けを強制する

このタスクでは、*[リソースでタグとその値が必要]* という組み込みポリシーをリソース グループに割り当て、結果を評価します。 

1. Azure portal で、「**ポリシー**」と検索して選択します。 

1. In the <bpt id="p1">**</bpt>Authoring<ept id="p1">**</ept> section, click <bpt id="p2">**</bpt>Definitions<ept id="p2">**</ept>. Take a moment to browse through the list of built-in policy definitions that are available for you to use. List all built-in policies that involve the use of tags by selecting the <bpt id="p1">**</bpt>Tags<ept id="p1">**</ept> entry (and de-selecting all other entries) in the <bpt id="p2">**</bpt>Category<ept id="p2">**</ept> drop-down list. 

1. **[リソースでタグとその値が必要]** という組み込みポリシーを表すエントリをクリックし、その定義を確認します。

1. **[リソースでタグとその値が必要]** という組み込みポリシーの定義のブレードで、**[割り当て]** をクリックします。

1. 省略記号ボタンをクリックして次の値を選択し、**[スコープ]** を指定します。

    | 設定 | 値 |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | 前のタスクで識別した Cloud Shell アカウントを含むリソース グループの名前 |

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: A scope determines the resources or resource groups where the policy assignment takes effect. You could assign policies on the management group, subscription, or resource group level. You also have the option of specifying exclusions, such as individual subscriptions, resource groups, or resources (depending on the assignment scope). 

1. 次の設定を指定して、割り当ての **基本**プロパティを構成します (その他は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | 割り当て名 | **Infra 値を持つ Role タグが必要**|
    | 説明 | **Cloud Shell リソース グループ内のすべてのリソースに Infra 値を持つ Role タグが必要**|
    | ポリシーの適用 | Enabled |

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The <bpt id="p2">**</bpt>Assignment name<ept id="p2">**</ept> is automatically populated with the policy name you selected, but you can change it. You can also add an optional <bpt id="p1">**</bpt>Description<ept id="p1">**</ept>. <bpt id="p1">**</bpt>Assigned by<ept id="p1">**</ept> is automatically populated based on the user name creating the assignment. 

1. **[次へ]** をクリックして、**[パラメーター]** に次の値を設定します。

    | 設定 | [値] |
    | --- | --- |
    | タグ名 | **ロール** |
    | タグ値 | **インフラストラクチャ** |

1. **[次へ]** をクリックして、**[修復]** タブを確認します。**[マネージド ID の作成]** チェックボックスのチェックは外したままにしておきます。 

    >**注**:この設定は、ポリシーまたはイニシアチブに **deployIfNotExists** または **Modify** の効果が含まれている場合に使用できます。

1. **[確認と作成]** をクリックしてから、**[作成]** をクリックします。

    >**注**:ここで、必要なタグを明示的に追加せずに、リソース グループに別の Azure Storage ストレージ アカウントを作成することによって、新しいポリシーが有効に割り当てられていることを確認します。 
    
    >**注**:ポリシーが有効になるまでに 5 分から 15 分かかる場合があります。

1. Cloud Shell ホーム ドライブに使用されているストレージ アカウントをホストしているリソース グループのブレードに戻ります。これは前のタスクで特定したものです。

1. [リソース グループ] ブレードで、**[+ 作成]** をクリックしてから「**ストレージ アカウント**」と検索し、**[+ 作成]** をクリックします。 

1. **[ストレージ アカウントの作成]** ブレードの **[基本]** タブで、ポリシーが適用されたリソース グループを使用していることを確認し、次の設定を指定して **[確認および作成]** をクリックし (その他の設定は既定値のままにします)、**[作成]** をクリックします。

    | 設定 | 値 |
    | --- | --- |
    | ストレージ アカウント名 | 英字で始まる、グローバルに一意な、3 個から 24 個の小文字と数字の任意の組み合わせ |

1. Once you create the deployment, you should see the <bpt id="p1">**</bpt>Deployment failed<ept id="p1">**</ept> message in the <bpt id="p2">**</bpt>Notifications<ept id="p2">**</ept> list of the portal. From the <bpt id="p1">**</bpt>Notifications<ept id="p1">**</ept> list, navigate to the deployment overview and click the <bpt id="p2">**</bpt>Deployment failed. Click here for details<ept id="p2">**</ept> message to identify the reason for the failure. 

    >**注**:エラー メッセージが、ポリシーによってリソースのデプロイが許可されなかったことを示しているかどうか確認します。 

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: By clicking the <bpt id="p2">**</bpt>Raw Error<ept id="p2">**</ept> tab, you can find more details about the error, including the name of the role definition <bpt id="p3">**</bpt>Require Role tag with Infra value<ept id="p3">**</ept>. The deployment failed because the storage account you attempted to create did not have a tag named <bpt id="p1">**</bpt>Role<ept id="p1">**</ept> with its value set to <bpt id="p2">**</bpt>Infra<ept id="p2">**</ept>.

#### <a name="task-3-apply-tagging-via-an-azure-policy"></a>タスク 3:Azure Policy を使用してタグ付けを適用する

このタスクでは、別のポリシー定義を使用して、非準拠のリソースを修復します。 

1. Azure portal で、「**ポリシー**」と検索して選択します。 

1. **[作成]** セクションで、**[割り当て]** をクリックします。 

1. 割り当てのリストで、**[インフラ値を持つロール タグを必要とする]** ポリシーの割り当てを表す行の省略記号アイコンをクリックし、**[割り当ての削除]** メニューの項目を使用してこの割り当てを削除します。

1. **[ポリシーの割り当て]** をクリックし、省略記号ボタンをクリックして次の値を選択し、**[スコープ]** を指定します。

    | 設定 | 値 |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | 最初のタスクで特定した Cloud Shell アカウントを含むリソース グループの名前 |

1. **[ポリシー定義]** を指定するには、省略記号ボタンをクリックし、「**存在しない場合は、リソース グループからタグを継承する**」と検索して選択します。

1. 次の設定を指定して、その割り当ての残りの **[基本]** プロパティを構成します (その他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | 割り当て名 | **Role タグとその Infra 値がなければ、Cloud Shell リソース グループから継承する**|
    | 説明 | **Role タグとその Infra 値がなければ、Cloud Shell リソース グループから継承する**|
    | ポリシーの適用 | Enabled |

1. **[次へ]** をクリックして、**[パラメーター]** に次の値を設定します。

    | 設定 | [値] |
    | --- | --- |
    | タグ名 | **ロール** |

1. **[次へ]** をクリックし、**[修復]** タブで次の設定を構成します (その他の設定は既定値のままにします)。

    | 設定 | [値] |
    | --- | --- |
    | 修復タスクを作成する | enabled |
    | 修復するポリシー | **存在しない場合は、サブスクリプションからタグを継承する** |

    >**注**: このポリシー定義には **[変更]** の効果が含まれます。

1. **[確認と作成]** をクリックしてから、**[作成]** をクリックします。

    >**注**:新しいポリシーの割り当てが有効であることを確認するには、必要なタグを明示的に追加せずに、同じリソース グループに別の Azure ストレージ アカウントを作成します。 
    
    >**注**:ポリシーが有効になるまでに 5 分から 15 分かかる場合があります。

1. Cloud Shell ホーム ドライブに使用されるストレージ アカウントをホストしているリソース グループのブレードに戻ります。これは前のタスクで特定したものです。

1. [リソース グループ] ブレードで、**[+ 作成]** をクリックしてから「**ストレージ アカウント**」と検索し、**[+ 作成]** をクリックします。 

1. **[ストレージ アカウントの作成]** ブレードの **[基本]** タブで、ポリシーが適用されたリソース グループを使用していることを確認し、次の設定を指定して **[確認および作成]** をクリックします (その他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | ストレージ アカウント名 | 英字で始まる、グローバルに一意な、3 個から 24 個の小文字と数字の任意の組み合わせ |

1. 今度は検証に成功したことを確認し、**[作成]** をクリックします。

1. 新しいストレージ アカウントがプロビジョニングされたら、**[リソースに移動]** ボタンをクリックし、新しく作成したストレージ アカウントの **[概要]** ブレードで、値 **Infra** のタグ **Role** が自動的にリソースに割り当てられていること確認します。

#### <a name="task-4-clean-up-resources"></a>タスク 4: リソースをクリーンアップする

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not see unexpected charges, although keep in mind that Azure policies do not incur extra cost.
   
   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>:  Don't worry if the lab resources cannot be immediately removed. Sometimes resources have dependencies and take a longer time to delete. It is a common Administrator task to monitor resource usage, so just periodically review your resources in the Portal to see how the cleanup is going. 

1. ポータルで、「**ポリシー**」と検索して選択します。

1. **[作成]** セクションで **[割り当て]** をクリックし、前のタスクで作成した割り当ての右側にある省略記号アイコンをクリックし、**[割り当ての削除]** をクリックします。 

1. ポータルで、「**ストレージ アカウント**」と検索して選択します。

1. In the list of storage accounts, select the resource group corresponding to the storage account you created in the last task of this lab. Select <bpt id="p1">**</bpt>Tags<ept id="p1">**</ept> and click <bpt id="p2">**</bpt>Delete<ept id="p2">**</ept> (Trash can to the right) to the <bpt id="p3">**</bpt>Role:Infra<ept id="p3">**</ept> tag and press <bpt id="p4">**</bpt>Apply<ept id="p4">**</ept>. 

1. Click <bpt id="p1">**</bpt>Overview<ept id="p1">**</ept> and click <bpt id="p2">**</bpt>Delete<ept id="p2">**</ept> on the top of the storage account blade. When prompted for the confirmation, in the <bpt id="p1">**</bpt>Delete storage account<ept id="p1">**</ept> blade, type the name of the storage account to confirm and click <bpt id="p2">**</bpt>Delete<ept id="p2">**</ept>. 

#### <a name="review"></a>確認

このラボでは、次のことを行いました。

- Azure portal でタグを作成して割り当てました
- Azure Policy でタグ付けを強制しました
- Azure Policy でタグ付けを適用しました
