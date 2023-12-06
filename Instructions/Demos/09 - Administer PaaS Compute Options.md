---
demo:
  title: 'デモ 09: PaaS Compute オプションを管理する'
  module: Administer PaaS Compute Options
---

# 09 - PaaS Compute オプションを管理する

## Azure App Service プランを構成する

このデモでは、Azure App Service プランを作成して操作します。

**参照**: [App Service プランを管理する - Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-plan-manage)

**参照**: [Azure App Service でアプリをスケールアップする](https://learn.microsoft.com/azure/app-service/manage-scale-up)

**参照**: [Azure App Service での自動スケーリング](https://learn.microsoft.com/azure/app-service/manage-automatic-scaling?tabs=azure-portal)

1. Azure portal を使用します。 

1.  **App Service プラン**を検索して選択します。

1. シンプルな App Service プランを作成します。 Windows または Linux を選択する必要性について説明します。 ここでまたは次の手順で価格プランについて説明します。 

1. 新しい App Service プランをデプロイします。 

1. **[スケールアップ (App Service プラン)]** ブレードを確認します。 **Dev/Test** と**運用**プランの違いについて説明します。 機能の一覧を確認します。 

1. **[スケールアウト (App Service プラン)]** ブレードを確認します。 **手動**と**ルールベース**の違いを確認します。 

## Azure App Service を構成する

このデモでは、Docker コンテナーを実行する新しい Web アプリを作成します。 コンテナーにウェルカム メッセージが表示されます。

**参照**: [Web アプリを作成する](https://learn.microsoft.com/training/modules/host-a-web-app-with-azure-app-service/3-exercise-create-a-web-app-in-the-azure-portal?pivots=csharp)

このタスクでは、Azure App Service Web アプリを作成します。

1. Azure portal を使用します。 

1. [App Services] を **検索**して選択します。

1. **Web アプリ**を**作成**します。

    - 公開: **コード**。 その他の選択肢を確認します。
    - ランタイム スタック: **.Net**。 その他の選択肢を確認します。
    - オペレーティング システム: **Linux**

1. **Free F1** サービス プランを選択します。

1. Web アプリを**確認して作成**します。 リソースがデプロイされるまで待ちます。

1. **[概要]** ページで、 **[状態]** が **[実行中]** になっていることを確かめます。

1. **[URL]** を選択し、既定のプレースホルダー ページが読み込まれることを確かめます。

1. 時間があれば、 **[デプロイ スロット]** オプションを調べます。
   
## Azure Container Instances を構成する

このデモでは、Azure portal で Azure Container Instances (ACI) を使用してコンテナーを作成、構成、デプロイします。 ACI アプリケーションには、パブリック Microsoft Hello World イメージを含む静的 HTML ページが表示されます。 

**参照**: [クイックスタート - コンテナー インスタンスに Docker コンテナーをデプロイする](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-quickstart-portal)

1. Azure portal を使用します。

1.  **コンテナー インスタンス**を検索して選択します。

1. 新しいコンテナー インスタンスを**作成**します。 

1. **[リソース グループ]** と **[コンテナー名]** を入力します。 

1. **[イメージのソース]** オプションについて説明します。 **クイックスタート イメージ**を使用します。

1. **コンテナー イメージ**の場合は、**mcr.microsoft.com/azuredocs/aci-helloworld:latest (Linux)** を使用します。 このサンプル Linux イメージには、静的な HTML ページを返す、Node.js で作成された小さな Web アプリがパッケージ化されています。

1. **[Networking](ネットワーク)** ページで、コンテナーの **DNS 名ラベル**を指定します。 

1. その他の設定はすべて既定値のままにして、**[確認と作成]** を選択します。

1. リソースがデプロイされるまで待ちます。

1. リソースの [**概要**] ページで、[**状態**] が [**実行中**] であることを確認します。

1. コンテナー インスタンスの **[FQDN]** に移動し、ウェルカム ページが表示されていることを確かめます。 

**注**: 追加コストを回避するには、リソースを削除します。 

## Azure Container Apps を構成する

このデモでは、Azure Container Apps を作成して操作します。 

**参照**: [クイックスタート: Azure portal を使用して最初のコンテナー アプリをデプロイする](https://learn.microsoft.com/azure/container-apps/quickstart-portal)

1. **[コンテナー アプリ**] を検索して選択します。

1. **[プロジェクトの詳細]** を完了し、コンテナー アプリ**環境**を作成します。

1. コンテナー アプリを**確認して作成**します。

1. **アプリケーションの URL** のリンクを使用して、アプリケーションを表示します。

1. ブラウザーに **[Azure Container Apps へようこそ]** メッセージが表示されていることを確認します。 






