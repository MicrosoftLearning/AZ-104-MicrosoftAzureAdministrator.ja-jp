---
lab:
  title: 02a - サブスクリプションと RBAC を管理する
  module: Administer Governance and Compliance
---

# <a name="lab-02a---manage-subscriptions-and-rbac"></a>ラボ 02a - サブスクリプションと RBAC を管理する
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-requirements"></a>ラボの要件

This lab requires permissions to create Azure Active Directory (Azure AD) users, create custom Azure Role Based Access Control (RBAC) roles, and assign these roles to Azure AD users. Not all lab hosters may provide this capability. Ask your instructor for the availability of this lab.

## <a name="lab-scenario"></a>ラボのシナリオ

Contoso の Azure リソースの管理を強化するために、次の機能を実装する任務を負いました。

- Contoso のすべての Azure サブスクリプションを含む管理グループを作成する

- granting permissions to submit support requests for all subscriptions in the management group to a designated Azure Active Directory user. That user's permissions should be limited only to: 

    - サポート要求チケットの作成
    - リソース グループの表示

<bpt id="p1">**</bpt>Note:<ept id="p1">**</ept> An <bpt id="p2">**</bpt><bpt id="p3">[</bpt>interactive lab simulation<ept id="p3">](https://mslabs.cloudguides.com/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%202)</ept><ept id="p2">**</ept> is available that allows you to click through this lab at your own pace. You may find slight differences between the interactive simulation and the hosted lab, but the core concepts and ideas being demonstrated are the same.

## <a name="objectives"></a>目標

このラボでは、次のことを行います。

+ タスク 1:管理グループを実装する
+ タスク 2:カスタム RBAC ロールを作成する 
+ タスク 3:RBAC ロールを割り当てる


## <a name="estimated-timing-30-minutes"></a>推定時間:30 分

## <a name="architecture-diagram"></a>アーキテクチャの図

![image](../media/lab02a.png)


## <a name="instructions"></a>Instructions

### <a name="exercise-1"></a>演習 1

#### <a name="task-1-implement-management-groups"></a>タスク 1:管理グループを実装する

このタスクでは、管理グループを作成および構成します。 

1. [**Azure Portal**](http://portal.azure.com) にサインインします。

1. 「**管理グループ**」を検索して選択し、**[管理グループ]** ブレードに移動します。

1. Review the messages at the top of the <bpt id="p1">**</bpt>Management groups<ept id="p1">**</ept> blade. If you are seeing the message stating <bpt id="p1">**</bpt>You are registered as a directory admin but do not have the necessary permissions to access the root management group<ept id="p1">**</ept>, perfom the following sequence of steps:

    1. Azure portal で、 **[Azure Active Directory]** を検索して選択します。
    
    1.  Azure Active Directory テナントのプロパティを表示しているブレードの、左側の垂直方向に配置されたメニュー内で **[管理]** セクションから **[プロパティ]** を選択します。
    
    1.  Azure Active Directory テナントの **[プロパティ]** ブレードの、**[Azure リソースのアクセス管理]** セクションで、**[はい]** を選択し、さらに **[保存]** を選択します。
    
    1.  **[管理グループ]** ブレードに戻り、**[更新​​]** をクリックします。

1. **[管理グループ]** ブレードで、**[+ 作成]** をクリックします。

    >**注**: 以前に管理グループを作成していない場合は、**[管理グループの使用を開始]** を選択します

1. 次の設定で管理グループを作成します。

    | 設定 | [値] |
    | --- | --- |
    | 管理グループ ID | **az104-02-mg1** |
    | 管理グループの表示名 | **az104-02-mg1** |

1. 管理グループのリストで、新しく作成された管理グループを表すエントリをクリックしします。

1. **[az104-02-mg1]** ブレードで、**[サブスクリプション]** をクリックします。 

1. **[az104-02-mg1 \| サブスクリプション]** ブレードで、 **[+ 追加]** をクリックし、 **[サブスクリプションの追加]** ブレードの **[サブスクリプション]** ドロップダウンリストから、このラボで使用するサブスクリプションを選択して、 **[保存]** をクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: On the <bpt id="p2">**</bpt>az104-02-mg1 <ph id="ph1">\|</ph> Subscriptions<ept id="p2">**</ept> blade, copy the ID of your Azure subscription into Clipboard. You will need it in the next task.

#### <a name="task-2-create-custom-rbac-roles"></a>タスク 2:カスタム RBAC ロールを作成する

このタスクでは、カスタム RBAC ロールの定義を作成します。

1. ラボのコンピューターから **\\Allfiles\\Labs\\02\\az104-02a-customRoleDefinition.json** ファイルをメモ帳で開き、その内容を確認します。

   ```json
   {
      "Name": "Support Request Contributor (Custom)",
      "IsCustom": true,
      "Description": "Allows to create support requests",
      "Actions": [
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
          "/providers/Microsoft.Management/managementGroups/az104-02-mg1",
          "/subscriptions/SUBSCRIPTION_ID"
      ]
   }
   ```
    > **注**:ファイルがラボ環境内のローカルのどこに格納されているかが分からない場合は、インストラクターに確認してください。

1. JSON ファイルの `SUBSCRIPTION_ID` プレースホルダーを、クリップボードにコピーしたサブスクリプション ID に置き換えて、変更を保存します。

1. Azure portal で、**[Cloud Shell]** ウィンドウを開くには、検索テキストボックスの右側にあるツールバー アイコンを直接クリックします。

1. **Bash** または **PowerShell** の選択を求めるメッセージが表示されたら、 **[PowerShell]** を選択します。 

    >**注**: **Cloud Shell** の初回起動時に **"ストレージがマウントされていません"** というメッセージが表示された場合は、このラボで使用しているサブスクリプションを選択し、**[ストレージの作成]** を選択します。 

1. [Cloud Shell] ペインのツールバーで、 **[ファイルのアップロード/ダウンロード]** アイコンをクリックし、ドロップダウン メニューの **[アップロード]** をクリックして、ファイル **\\Allfiles\\Labs\\02\\az104-02a-customRoleDefinition.json** を Cloud Shell のホーム ディレクトリにアップロードします。

1. Cloud Shell ウィンドウから、次の操作を実行して、カスタム ロール定義を作成します。

   ```powershell
   New-AzRoleDefinition -InputFile $HOME/az104-02a-customRoleDefinition.json
   ```

1. [Cloud Shell] ペインを閉じます。

#### <a name="task-3-assign-rbac-roles"></a>タスク 3:RBAC ロールを割り当てる

このタスクでは、Azure Active Directory ユーザーを作成し、前のタスクで作成した RBAC ロールをそのユーザーに割り当て、ユーザーが RBAC ロール定義で指定されたタスクを実行できることを確認します。

1. Azure portal の [Azure Active Directory] ブレードで、「**Azure Active Directory**」を検索して選択し、**[ユーザー]** をクリックして、**[新しいユーザー]** をクリックします。

1. 次の設定で、新しいユーザーを作成します (他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | ユーザー名 | **az104-02-aaduser1**|
    | 名前 | **az104-02-aaduser1**|
    | 自分でパスワードを作成する | enabled |
    | 初期パスワード | **セキュリティで保護されたパスワードを指定する** |

    >このラボでは、Azure Active Directory (Azure AD) ユーザーを作成し、カスタムの Azure ロール ベースのアクセス制御 (RBAC) ロールを作成し、これらのロールを Azure AD ユーザーに割り当てるためのアクセス許可が必要です。

1. Azure portal で、**az104-02-mg1** 管理グループに戻り、その**詳細**を表示します。

1. すべてのラボ ホスティング業者がこの機能を提供できるわけではありません。 

    >**注**: カスタム役割が表示されない場合、作成後にカスタム役割が表示されるまでに最大 10 分かかることがあります。

1. このラボの可用性については、インストラクターに問い合わせてください。

1. Open an <bpt id="p1">**</bpt>InPrivate<ept id="p1">**</ept> browser window and sign in to the <bpt id="p2">[</bpt>Azure portal<ept id="p2">](https://portal.azure.com)</ept> using the newly created user account. When prompted to update the password, change the password for the user.

    >**注**:ユーザー名を入力するのではなく、クリップボードの内容を貼り付けることができます。

1. Azure portal の **[InPrivate]** ブラウザー ウィンドウで、**[リソース グループ]** を検索して選択し、az104-02-aaduser1 ユーザーがすべてのリソース グループを表示できることを確認します。

1. Azure portal の **[InPrivate]** ブラウザー ウィンドウで、**[すべてのリソース]** を検索して選択し、az104-02-aaduser1 ユーザーにリソースを表示できないことを確認します。

1. Azure portal の **[InPrivate]** ブラウザー ウィンドウで、**[ヘルプとサポート]** を検索して選択し、**[サポート要求の作成]** をクリックします。 

1. In the <bpt id="p1">**</bpt>InPrivate<ept id="p1">**</ept> browser window, on the <bpt id="p2">**</bpt>Problem Desription/Summary<ept id="p2">**</ept> tab of the <bpt id="p3">**</bpt>Help + support - New support request<ept id="p3">**</ept> blade, type <bpt id="p4">**</bpt>Service and subscription limits<ept id="p4">**</ept> in the Summary field and select the <bpt id="p5">**</bpt>Service and subscription limits (quotas)<ept id="p5">**</ept> issue type. Note that the subscription you are using in this lab is listed in the <bpt id="p1">**</bpt>Subscription<ept id="p1">**</ept> drop-down list.

    >**注**: このラボで使用しているサブスクリプションが **[サブスクリプション]** ドロップダウン リストに表示されている場合、使用しているアカウントに、サブスクリプション固有のサポート要求を作成するために必要なアクセス許可があることを示しています。

    >**注**: **[サービスとサブスクリプションの制限 (クォータ)]** オプションが表示されない場合は、Azure portal からサインアウトしてログインし直します。

1. Do not continue with creating the support request. Instead, sign out as the az104-02-aaduser1 user from the Azure portal and close the InPrivate browser window.

#### <a name="task-4-clean-up-resources"></a>タスク 4: リソースをクリーンアップする

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not see unexpected charges, although, resources created in this lab do not incur extra cost.

   >管理グループ内のすべてのサブスクリプションに対するサポート要求を、指定された Azure Active Directory ユーザーに提出するアクセス許可を付与する。

1. Azure portal で **[Azure Active Directory]** を検索して選択し、[Azure Active Directory] ブレードで、**[ユーザー]** をクリックします。

1. **[ユーザー - すべてのユーザー]** ブレードで **[az104-02-aaduser1]** をクリックします。

1. **[az104-02-aaduser1 - プロファイル]** ブレードで、**[オブジェクト ID]** 属性の値をコピーします。

1. Azure portal 内の **[Cloud Shell]** で **PowerShell** セッションを開始します。

1. [Cloud Shell] ペインで、次の手順を実行してカスタム ロール定義の割り当てを削除します (`[object_ID]` プレースホルダーを、このタスクの前半でコピーした **az104-02-aaduser1** の Azure Active Directory ユーザー アカウントの **object ID** 属性の値で置き換えます)。

   ```powershell
   
   $scope = (Get-AzRoleDefinition -Name 'Support Request Contributor (Custom)').AssignableScopes[0]

   Remove-AzRoleAssignment -ObjectId '[object_ID]' -RoleDefinitionName 'Support Request Contributor (Custom)' -Scope $scope
   ```

1. [Cloud Shell] ウィンドウから次の手順を実行して、カスタム ロールの定義を削除します。

   ```powershell
   Remove-AzRoleDefinition -Name 'Support Request Contributor (Custom)' -Force
   ```

1. Azure portal で、**Azure Active Directory** の **[ユーザー - すべてのユーザー]** ブレードに戻り、**az104-02-aaduser1** ユーザー アカウントを削除します。

1. Azure portal で、**[管理グループ]** ブレードに戻ります。 

1. **[管理グループ]** ブレードで、**az104-02-mg1** 管理グループの下のサブスクリプションの横にある**省略記号**アイコンを選択し、**[移動]** を選択してサブスクリプションを **[テナント ルート管理グループ]** に移動します。

   >**注**:このラボを実行する前にカスタム管理グループ階層を作成した場合を除き、ターゲットの管理グループが**テナント ルート管理グループ**であると考えられます。
   
1. **[更新]** を選択して、サブスクリプションが **[テナント ルート管理グループ]** に正常に移動したことを確認します。

1. **[管理グループ]** ブレードに戻り、 **[az104-02-mg1]** 管理グループの右側にある**省略記号**アイコンをクリックし、 **[削除]** をクリックします。
  >そのユーザーのアクセス許可は、次の場合に限定する必要があります。

#### <a name="review"></a>確認

このラボでは、次のことを行いました。

- 管理グループを作成しました
- カスタム RBAC ロールを作成しました 
- RBAC ロールを割り当てました
