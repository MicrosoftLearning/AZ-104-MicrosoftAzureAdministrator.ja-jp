---
demo:
  title: 'デモ 03: Azure リソースを管理する'
  module: Administer Administer Azure Resources
---
# 03 - Azure リソースを管理する

## デモ - Azure portal

このデモでは、Azure portal について調べます。

**リファレンス**: [Azure portal の設定を管理する](https://docs.microsoft.com/azure/azure-portal/set-preferences)

**リファレンス**: [Azure portal でダッシュボードを作成する](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)

**リファレンス**: [Azure サポート リクエストを作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)

1. Azure portal にアクセスします。

1. 上部のバナーにある  **[サポートとトラブルシューティング]** アイコンを選択します。 **[サポート リソース]** のリンクを確認します。 

1. 上部バナーの**設定**アイコンを選択します。 **[外観とスタートアップ ビュー]** 設定を確認します。 

1. 左側のメニューを使用し、 **[ダッシュボード]** を選択します。 **[タイル ギャラリー]** を使用してダッシュボードを**編集**します。 カスタマイズ オプションについて説明します。

1. リソースを検索して特定する方法を示します。

1. 左上のメニューを使用して、 **[すべてのサービス]** を見つけます。 

1. 時間があれば、他の機能を確認してください。
   
1. 生徒に質問があるかどうかを確認します。

## デモ - Cloud Shell

このデモでは、Cloud Shell を試します。

**リファレンス**: [Azure Cloud Shell のクイック スタート](https://learn.microsoft.com/en-us/azure/cloud-shell/quickstart?tabs=azurecli)

**Cloud Shell を設定する**

1.   **Azure portal** にアクセスします。

1.  上部のバナーにある  **[Cloud Shell]**   アイコンをクリックします。

1.  「Shell へようこそ」ページで、Bash または PowerShell の選択内容を確認します。  **[PowerShell]** を選択します。

1.  Azure Cloud Shell では、Azure ファイル共有によってどのようにファイルを保持する必要があるかを説明します。 必要に応じて、ストレージ共有を構成します。 

**Azure PowerShell と Bash を使用してみる**

1. **PowerShell** シェルを確実に選択し、いくつかのコマンドを試してみます。 たとえば、**Get-AzSubscription**  や **Get-AzResourceGroup** などです。

1. オートコンプリートのしくみを示します。 画面をクリアする方法 (**cls**) を示します。 

1. **Bash** シェルを確実に選択し、いくつかのコマンドを試してみます。 たとえば、**az account list**  や **az resource list** などです。

1. PowerShell または Bash コマンドの使用について学生に質問があるかどうかを尋ねます。 

**Cloud Shell エディターを試す (省略可能)**

1. クラウド エディターを使用するには、 **[中かっこ]** アイコンを選択します。

1. 左側のナビゲーション ペインからファイルを選択します。 たとえば、 **[.profile]** があります。

1. エディターの上部バナーに、設定 (テキスト サイズとフォント) とファイルのアップロード/ダウンロードの選択肢があることに注意してください。

1. **[保存]** 、 **[エディターを閉じる]** 、および **[ファイルを開く]** の右端にある省略記号 ( **[\...]** ) に注意してください。

1. 時間が許す限り試してみてから、Cloud Editor を **閉じます** 。

1. Cloud Shell を閉じます。

## デモ - クイック スタート テンプレート

このデモでは、クイック スタート テンプレートについて説明します。

**リファレンス**: [チュートリアル - テンプレートをデプロイおよび作成する - Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

1. まず、 [Azure クイック スタート テンプレート ギャラリー](https://learn.microsoft.com/en-us/samples/browse/?expanded=azure&products=azure-resource-manager)を参照します。 JSON と Bicep の例があることに注意してください。 

1. 特定のテンプレートに関心があるかどうかを学生に尋ねます。 ない場合は、任意のテンプレートを選択します。 たとえば、[[タグ付きのシンプルな Windows VM をデプロイする]](https://learn.microsoft.com/en-us/samples/azure/azure-quickstart-templates/vm-tags/)  テンプレートを選択します。

1.  **[Azure にデプロイ]**   ボタンを使用して、Azure portal から直接テンプレートをデプロイする方法について説明します。

1. JSON テンプレートを**デプロイ**し、次にテンプレートおよびパラメーター ファイルを編集する方法について説明します。 ファイルの目的を確認します。 時間があれば、構文を確認してください。 

1. コード サンプル ギャラリーに戻り、Bicep テンプレートを見つけます。 たとえば、[[Standard Storage アカウントを作成する]](https://learn.microsoft.com/en-us/samples/azure/azure-quickstart-templates/storage-account-create/) などです。 

1. Bicep テンプレートを**デプロイ**し、次にテンプレートおよびパラメーター ファイルを編集する方法について説明します。 時間があれば、構文を確認してください。 
