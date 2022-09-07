---
lab:
  title: 01 - Azure Active Directory ID を管理する
  module: Administer Identity
---

# <a name="lab-01---manage-azure-active-directory-identities"></a>ラボ 01 - Azure Active Directory ID を管理する

# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

In order to allow Contoso users to authenticate by using Azure AD, you have been tasked with provisioning users and group accounts. Membership of the groups should be updated automatically based on the user job titles. You also need to create a test Azure AD tenant with a test user account and grant that account limited permissions to resources in the Contoso Azure subscription.

対話型ガイド形式でこのラボをプレビューするには、 **[ここをクリックしてください](https://mslabs.cloudguides.com/en-us/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%201)** 。

## <a name="objectives"></a>目標

このラボでは、次のことを行います。

+ タスク 1:Azure AD ユーザーを作成および構成する
+ タスク 2:割り当て済みで動的なメンバーシップを持つ Azure AD グループを作成する
+ タスク 3:Azure Active Directory (AD) テナントを作成する (オプション - ラボ環境の問題)
+ タスク 4:Azure AD ゲスト ユーザーを管理する (オプション - ラボ環境の問題)

## <a name="estimated-timing-30-minutes"></a>推定時間:30 分

## <a name="architecture-diagram"></a>アーキテクチャの図
![image](../media/lab01.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1"></a>演習 1

#### <a name="task-1-create-and-configure-azure-ad-users"></a>タスク 1:Azure AD ユーザーを作成および構成する

このタスクでは、Azure AD ユーザーを作成および構成します。

>**注**:以前にこの Azure AD テナントで Azure AD Premium の試用版ライセンスを使用したことがある場合は、新しい Azure AD テナントが必要になり、その新しい Azure AD テナントで、タスク 3 の後にタスク 2 を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal で、 **[Azure Active Directory]** を検索して選択します。

1. [Azure Active Directory] ブレードで、**[管理]** セクションまでスクロールダウンし、**[ユーザー設定]** をクリックして、使用可能な構成オプションを確認します。

1. [Azure Active Directory] ブレードの **[管理]** セクションで **[ユーザー]** をクリックし、ユーザー アカウントをクリックして **[プロファイル]** の設定を表示します。 

1. **[編集]** をクリックし、**[設定]** セクションで **[利用場所]** を **[米国]** に設定し、**[保存]** をクリックして、変更を適用します。

    >**注**:これは、このラボの後半で Azure AD Premium P2 ライセンスをユーザー アカウントに割り当てるために必要です。

1. **[ユーザー - すべてのユーザー]** ブレードに戻り、**[+ 新しいユーザー]** を選択します。

1. 次の設定で、新しいユーザーを作成します (他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | ユーザー名 | **az104-01a-aaduser1** |
    | 名前 | **az104-01a-aaduser1** |
    | 自分でパスワードを作成する | enabled |
    | 初期パスワード | **セキュリティで保護されたパスワードを指定する** |
    | 利用場所 | **米国** |
    | 役職 | **クラウド管理者** |
    | Department | **IT** |

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: <bpt id="p2">**</bpt>Copy to clipboard<ept id="p2">**</ept> the full <bpt id="p3">**</bpt>User Principal Name<ept id="p3">**</ept> (user name plus domain). You will need it later in this task.

1. ユーザーのリストで、新しく作成したユーザー アカウントをクリックして、そのブレードを表示します。

1. **[管理]** セクションで使用できるオプションを確認し、ユーザー アカウントに割り当てられた Azure AD のロールと、Azure リソースに対するユーザー アカウントのアクセス許可を識別できることに注意してください。

1. **[管理]** セクションで、**[割り当てられたロール]** をクリックし、**[+ 割り当ての追加]** ボタンをクリックして、**ユーザー管理者**ロールを **az104-01a-aaduser1** に割り当てます。

    >**注**:新しいユーザーをプロビジョニングするときに、Azure AD の役割を割り当てるオプションもあります。

1. Open an <bpt id="p1">**</bpt>InPrivate<ept id="p1">**</ept> browser window and sign in to the <bpt id="p2">[</bpt>Azure portal<ept id="p2">](https://portal.azure.com)</ept> using the newly created user account. When prompted to update the password, change the password to a secure password of your choosing. 

    >**注**:ユーザー名 (ドメイン名を含む) を入力するのではなく、クリップボードの内容を貼り付けることができます。

1. Azure portal の **InPrivate** ブラウザー ウィンドウで、"**Azure Active Directory**" を検索して選択します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: While this user account can access the Azure Active Directory tenant, it does not have any access to Azure resources. This is expected, since such access would need to be granted explicitly by using Azure Role-Based Access Control. 

1. **InPrivate** ブラウザー ウィンドウの Azure AD ブレードで、**[管理]** セクションまでスクロールダウンし、**[ユーザー設定]** をクリックします。また、構成オプションを変更するアクセス許可がないことに注意してください。

1. **InPrivate** ブラウザー ウィンドウの Azure AD ブレードの **[管理]** セクションで、**[ユーザー]** をクリックし、**[+ 新しいユーザー]** をクリックします。

1. 次の設定で、新しいユーザーを作成します (他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | ユーザー名 | **az104-01a-aaduser2** |
    | 名前 | **az104-01a-aaduser2** |
    | 自分でパスワードを作成する | enabled |
    | 初期パスワード | **セキュリティで保護されたパスワードを指定する** |
    | 利用場所 | **米国** |
    | 役職 | **システム管理者** |
    | Department | **IT** |

1. Azure portal から az104-01a-aaduser1 ユーザーとしてサインアウトし、InPrivate ブラウザー ウィンドウを閉じます。

#### <a name="task-2-create-azure-ad-groups-with-assigned-and-dynamic-membership"></a>タスク 2:割り当て済みで動的なメンバーシップを持つ Azure AD グループを作成する

このタスクでは、割り当てられた動的メンバーシップを持つ Azure Active Directory グループを作成します。

1. **ユーザー アカウント**でサインインしている Azure portal に戻り、Azure AD テナントの **[概要]** ブレードに戻り、**[管理]** セクションの **[ライセンス]** をクリックします。

    >**注**:動的グループを実装するには、Azure AD Premium P1 または P2 ライセンスが必要です。

1. **[管理]** セクションで、**[すべての製品]** をクリックします。

1. **[+ 試用または購入]** をクリックし、Azure AD Premium P2 の無料試用版をアクティブにします。

1. ブラウザー ウィンドウを更新して、アクティブ化が成功したことを確認します。 

 ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: It can take up to 10 minutes for the licenses to activate. Continue refreshing the page until it appears. Do not proceed until the licenses have been activated.

1. **[ライセンス - すべての製品]** ブレードで、**[Azure Active Directory Premium P2]** エントリを選択し、Azure AD Premium P2 のすべてのライセンス オプションをユーザー アカウントと新しく作成した 2 つのユーザー アカウントに割り当てます。

1. Azure portal で、Azure AD テナント ブレードに戻り、**[グループ]** をクリックします。

1. **[+ 新しいグループ]** ボタンを使用して、次の設定で新しいグループを作成します。

    | 設定 | 値 |
    | --- | --- |
    | グループの種類 | **Security** |
    | グループ名 | **IT クラウド管理者** |
    | グループの説明 | **Contoso IT クラウド管理者** |
    | メンバーシップの種類 | **動的ユーザー** |

    >**注**: **[メンバーシップの種類]** ドロップダウン リストがグレー表示されている場合は、数分待ってからブラウザーのページを更新します。

1. **[動的クエリの追加]** をクリックします。

1. **[動的メンバーシップ ルール]** ブレードの **[ルールの構成]** タブで、次の設定を使用して新しいルールを作成します。

    | 設定 | 値 |
    | --- | --- |
    | プロパティ | **jobTitle** |
    | 演算子 | **[等しい]** |
    | 値 | **クラウド管理者** |

1. Contoso のユーザーが Azure AD を使用して認証できるようにするには、ユーザーおよびグループ アカウントのプロビジョニングを行う必要があります。 

1. Azure AD テナントの **[グループ - すべてのグループ]** ブレードに戻り、**[+ 新しいグループ]** ボタンをクリックし、次の設定で新しいグループを作成します。

    | 設定 | 値 |
    | --- | --- |
    | グループの種類 | **Security** |
    | グループ名 | **IT システム管理者** |
    | グループの説明 | **Contoso IT システム管理者** |
    | メンバーシップの種類 | **動的ユーザー** |

1. **[動的クエリの追加]** をクリックします。

1. **[動的メンバーシップ ルール]** ブレードの **[ルールの構成]** タブで、次の設定を使用して新しいルールを作成します。

    | 設定 | 値 |
    | --- | --- |
    | プロパティ | **jobTitle** |
    | 演算子 | **[等しい]** |
    | 値 | **システム管理者** |

1. グループのメンバーシップは、ユーザーの役職に基づいて自動的に更新されます。 

1. Azure AD テナントの **[グループ - すべてのグループ]** ブレードに戻り、**[+ 新しいグループ]** ボタンをクリックし、次の設定で新しいグループを作成します。

    | 設定 | 値 |
    | --- | --- |
    | グループの種類 | **Security** |
    | グループ名 | **IT ラボ管理者** |
    | グループの説明 | **Contoso IT ラボ管理者** |
    | メンバーシップの種類 | **割り当て済み** |
    
1. **[メンバーが選択されていません]** をクリックします。

1. **[メンバーの追加]** ブレードで、**[IT クラウド管理者]** グループと **[IT システム管理者]** グループを見つけて選択し、**[新しいグループ]** ブレードに戻って **[作成]** をクリックします。

1. テスト ユーザー アカウントを使用してテスト Azure AD テナントを作成し、Contoso Azure サブスクリプションのリソースに対する制限付きアクセス許可をそのアカウントに付与する必要もあります。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: You might experience delays with updates of the dynamic membership groups. To expedite the update, navigate to the group blade, display its <bpt id="p1">**</bpt>Dynamic membership rules<ept id="p1">**</ept> blade, <bpt id="p2">**</bpt>Edit<ept id="p2">**</ept> the rule listed in the <bpt id="p3">**</bpt>Rule syntax<ept id="p3">**</ept> textbox by adding a whitespace at the end, and <bpt id="p4">**</bpt>Save<ept id="p4">**</ept> the change.

1. Navigate back to the <bpt id="p1">**</bpt>Groups - All groups<ept id="p1">**</ept> blade, click the entry representing the <bpt id="p2">**</bpt>IT System Administrators<ept id="p2">**</ept> group and, on then display its <bpt id="p3">**</bpt>Members<ept id="p3">**</ept> blade. Verify that the <bpt id="p1">**</bpt>az104-01a-aaduser2<ept id="p1">**</ept> appears in the list of group members.

#### <a name="task-3-create-an-azure-active-directory-ad-tenant-optional---lab-environment-issue"></a>タスク 3:Azure Active Directory (AD) テナントを作成する (オプション - ラボ環境の問題)

このタスクでは、新しい Azure AD テナントを作成します。

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: There is a known issue with the Captcha verification in the lab environment. If you experience this issue, please skip both this task and the next. We are working on a solution.

1. Azure portal で、 **[Azure Active Directory]** を検索して選択します。

1. **[テナントの管理]** をクリックし、次の画面で **[作成]** をクリックして、次の設定を指定します。

    | 設定 | 値 |
    | --- | --- |
    | ディレクトリ タイプ | **Azure Active Directory** |
    
1. ページの下部の **次へ: 構成**

    | 設定 | 値 |
    | --- | --- |
    | 組織名 | **Contoso Lab** |
    | 初期ドメイン名 | 小文字と数字で構成され、文字で始まる有効な DNS 名 | 
    | 国/リージョン | **米国** |

   > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The <bpt id="p2">**</bpt>Initial domain name<ept id="p2">**</ept> should not be a legitimate name that potentially matches your organization or another. The green check mark in the <bpt id="p1">**</bpt>Initial domain name<ept id="p1">**</ept> text box will indicate that the domain name you typed in is valid and unique.

1. **[Review + create](レビュー + 作成)** をクリックし、 **[作成]** をクリックします。

1. Azure portal のツールバーにある **[ここをクリックして新しいテナントに移動する:Contoso Lab]** リンク、または **[ディレクトリとサブスクリプション]** ボタン ([Cloud Shell] ボタンの右側) を使用して、新しく作成した Azure AD テナントのブレードを表示します。

#### <a name="task-4-manage-azure-ad-guest-users"></a>タスク 4:Azure AD ゲスト ユーザーを管理する

このタスクでは、Azure AD ゲスト ユーザーを作成し、Azure サブスクリプション内のリソースへのアクセスを許可します。

1. Contoso Lab Azure AD テナントを表示する Azure portal の **[管理]** セクションで、**[ユーザー]** をクリックし、**[+ 新しいユーザー]** をクリックします。

1. 次の設定で、新しいユーザーを作成します (他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | ユーザー名 | **az104-01b-aaduser1** |
    | 名前 | **az104-01b-aaduser1** |
    | 自分でパスワードを作成する | enabled |
    | 初期パスワード | **セキュリティで保護されたパスワードを指定する** |
    | 役職 | **システム管理者** |
    | Department | **IT** |

1. 新しく作成されたプロファイルをクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: <bpt id="p2">**</bpt>Copy to clipboard<ept id="p2">**</ept> the full <bpt id="p3">**</bpt>User Principal Name<ept id="p3">**</ept> (user name plus domain). You will need it later in this task.

1. Azure portal のツール バーにある **[ディレクトリとサブスクリプション]** ボタン ([Cloud Shell] ボタンの右側) を使用して、既定の Azure AD テナントに切り替えます。

1. **[ユーザー - すべてのユーザー]** ブレードに戻り、**[+ 新しいゲスト ユーザー]** をクリックします。

1. 次の設定で新しいゲスト ユーザーを招待します (他の設定は既定値のままにします)。

    | 設定 | [値] |
    | --- | --- |
    | 名前 | **az104-01b-aaduser1** |
    | 電子メール アドレス | このタスクで前にコピーしたユーザー プリンシパル名 |
    | 利用場所 | **米国** |
    | 役職 | **ラボ管理者** |
    | Department | **IT** |

1. **[招待]** をクリックします。 

1. **[ユーザー - すべてのユーザー]** ブレードに戻り、新しく作成されたゲスト ユーザー アカウントを表すエントリをクリックします。

1. **[az104-01b-aaduser1 - プロファイル]** ブレードで、**[グループ]** をクリックします。

1. **[+ メンバーシップを追加します]** をクリックし、ゲスト ユーザー アカウントを **IT ラボ管理者**グループに追加します。


#### <a name="task-5-clean-up-resources"></a>タスク 5: リソースをクリーンアップする

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not incur unexpected costs. While, in this case, there are no additional charges associated with Azure Active Directory tenants and their objects, you might want to consider removing the user accounts, the group accounts, and the Azure Active Directory tenant you created in this lab.

 > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>:  Don't worry if the lab resources cannot be immediately removed. Sometimes resources have dependencies and take a longer time to delete. It is a common Administrator task to monitor resource usage, so just periodically review your resources in the Portal to see how the cleanup is going. 

1. In the <bpt id="p1">**</bpt>Azure Portal<ept id="p1">**</ept> search for <bpt id="p2">**</bpt>Azure Active Directory<ept id="p2">**</ept> in the search bar. Within <bpt id="p1">**</bpt>Azure Active Directory<ept id="p1">**</ept> under <bpt id="p2">**</bpt>Manage<ept id="p2">**</ept> select <bpt id="p3">**</bpt>Licenses<ept id="p3">**</ept>. Once at <bpt id="p1">**</bpt>Licenses<ept id="p1">**</ept> under <bpt id="p2">**</bpt>Manage<ept id="p2">**</ept> select <bpt id="p3">**</bpt>All Products<ept id="p3">**</ept> and then select <bpt id="p4">**</bpt>Azure Active Directory Premium P2<ept id="p4">**</ept> item in the list. Proceed by then selecting <bpt id="p1">**</bpt>Licensed Users<ept id="p1">**</ept>. Select the user accounts <bpt id="p1">**</bpt>az104-01a-aaduser1<ept id="p1">**</ept> and <bpt id="p2">**</bpt>az104-01a-aaduser2<ept id="p2">**</ept> to which you assigned licenses in this lab, click <bpt id="p3">**</bpt>Remove license<ept id="p3">**</ept>, and, when prompted to confirm, click <bpt id="p4">**</bpt>Yes<ept id="p4">**</ept>.

1. Azure portal で、**[ユーザー - すべてのユーザー]** ブレードに移動し、**az104-01b-aaduser1** ゲスト ユーザー アカウントを表すエントリをクリックします。**[az104-01b-aaduser1 - プロファイル]** ブレードで **[削除]** をクリックし、確認を求められたら、**[OK]** をクリックします。

1. 同じ手順を繰り返して、このラボで作成した残りのユーザー アカウントを削除します。

1. **[グループ - すべてのグループ]** ブレードに移動し、このラボで作成したグループを選択し、**[削除]** をクリックします。確認を求められたら、**[OK]** をクリックします。

1. Azure portal でツール バーの **[ディレクトリとサブスクリプション]** ボタン ([Cloud Shell] ボタンの右側) を使用して、Contoso Lab Azure AD テナントのブレードを表示します。

1. **[ユーザー - すべてのユーザー]** ブレードに移動し、**az104-01b-aaduser1** ユーザー アカウントを表すエントリをクリックします。**[az104-01b-aaduser1 - プロファイル]** ブレードで **[削除]** をクリックし、確認を求められたら、**[OK]** をクリックします。

1. Navigate to the <bpt id="p1">**</bpt>Contoso Lab - Overview<ept id="p1">**</ept> blade of the Contoso Lab Azure AD tenant, click <bpt id="p2">**</bpt>Manage tenants<ept id="p2">**</ept> and then on the next screen, select the box next to <bpt id="p3">**</bpt>Contoso Lab<ept id="p3">**</ept>, click <bpt id="p4">**</bpt>Delete<ept id="p4">**</ept>, on the <bpt id="p5">**</bpt>Delete tenant 'Contoso Labs'?<ept id="p5">**</ept> blade, click the <bpt id="p1">**</bpt>Get permission to delete Azure resources<ept id="p1">**</ept> link, on the <bpt id="p2">**</bpt>Properties<ept id="p2">**</ept> blade of Azure Active Directory, set <bpt id="p3">**</bpt>Access management for Azure resources<ept id="p3">**</ept> to <bpt id="p4">**</bpt>Yes<ept id="p4">**</ept> and click <bpt id="p5">**</bpt>Save<ept id="p5">**</ept>.

1. **[テナント 'Contoso Lab' の削除]** ブレードに戻り、 **[更新]** をクリックして、 **[削除]** をクリックします。

> <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: If a tenant has a trial license, then you would have to wait for the trial license expiration before you could delete the tenant. This would not incur any additional cost.

#### <a name="review"></a>確認

このラボでは、次のことを行いました。

- Azure AD ユーザーを作成および設定しました
- 割り当て済みおよび動的メンバーシップを持つ Azure AD グループを作成しました
- Azure Active Directory (AD) テナントを作成しました
- Azure AD ゲスト ユーザーを管理しました 
