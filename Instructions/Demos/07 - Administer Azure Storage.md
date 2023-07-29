---
demo:
  title: 'デモ 07: Azure Storage を管理する'
  module: Administer Azure Storage
---


# 07 - Azure Storage を管理する

## ストレージ アカウントを構成する

このデモでは、ストレージ アカウントを作成します。

**参照**: [ストレージ アカウントを作成する](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)

1. Azure portal を使用します。

1. ストレージ アカウントの目的を確認します。 
   
1. **ストレージ アカウント**を検索して選択します。 
 
1. 基本的なストレージ アカウントを作成します。 

    - ストレージ アカウントの名前付けに関する要件と、名前が Azure で一意であることの必要性について説明します。 

    - さまざまなストレージの種類を確認します。 たとえば、汎用 v2 です。 

    - アクセス層の選択肢を確認します。 たとえば、クールおよびホット層です。 

    - その他のタブと設定については、他のデモで説明します。 

1. ストレージ アカウントを作成し、リソースがデプロイされるまで待ちます。 


## Blob Storage を構成する

このデモでは、Blob Storage について説明します。

**注:**  これらの手順では、ストレージ アカウントが必要です。

**参照**: [クイック スタート: BLOB をアップロード、ダウンロード、および一覧表示する](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

1. Azure portal で、ストレージ アカウントに移動します。

1. Blob Storage の目的を確認します。 

1. BLOB コンテナーを作成します。 コンテナーのアクセス レベルを確認します。 たとえば、プライベート (匿名アクセスなし) です。 

1. BLOB をコンテナーにアップロードします。 時間があれば、詳細設定を確認してください。 たとえば、BLOB の種類や BLOB サイズです。 

## Azure Files を構成する 

このデモでは、ファイル共有とスナップショットを操作します。

**注:**  これらの手順では、ストレージ アカウントが必要です。

**参照**: [Azure ファイル共有を管理するためのクイックスタート](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-portal?tabs=azure-portal)

1. ファイル共有の目的を確認します。 

1. ストレージ アカウントにアクセスし、 **[ファイル]** をクリックします。

1. ファイル共有を作成します。 クォータを確認し、ファイルをアップロードし、ディレクトリを追加して情報を整理します。 

1. ファイル共有スナップショットを作成します。 スナップショットを使用するタイミングと、スナップショットとバックアップの違いを確認します。 時間があれば、ファイルのアップロード、スナップショットの取得、ファイルの削除、スナップショットの復元を行います。 


## ストレージ セキュリティを構成する

このデモでは、Shared Access Signature を作成します。

**注:**  このデモでは、BLOB コンテナーとアップロードされたファイルを含むストレージ アカウントが必要です。

**参照**: [ストレージ コンテナーの SAS トークンを作成する](https://learn.microsoft.com/azure/applied-ai-services/form-recognizer/create-sas-tokens?source=recommendations&view=form-recog-3.0.0)

1. セキュリティで保護する BLOB またはファイルを選択します。 

1. Shared Access Signature (SAS) トークンを生成します。 アクセス許可、開始時刻と有効期限、許可されているプロトコルを確認します。

1. SAS URL を使用して、リソースが確実に表示されるようにします。 


## Storage ツール (省略可能)

このデモでは、いくつかの一般的な Azure Storage ツールについて確認します。 

**参照:** [Storage Explorer の概要](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)

1. Storage Explorer をインストールするか、Storage Browser を使用します。

1. ストレージ リソースを参照して作成する方法を確認します。 たとえば、BLOB コンテナーを追加します。 

**参照**: [AzCopy v10 を使用して Azure Storage にデータをコピーまたは移動する](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=/azure/storage/files/toc.json)

1. AzCopy を使用するタイミングについて説明します。 ヘルプを表示します (**azcopy /?** )。

1.  **[サンプル]**  セクションを下へスクロールします。 時間があれば、いずれかの例を試してみてください。 
    



