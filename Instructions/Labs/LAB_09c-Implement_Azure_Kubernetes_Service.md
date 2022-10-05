---
lab:
  title: 09c - Azure Kubernetes Service を実装する
  module: Administer Serverless Computing
---

# <a name="lab-09c---implement-azure-kubernetes-service"></a>ラボ 09c - Azure Kubernetes Service を実装する
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

Contoso には、Azure Container Instances を使用して実行するのに適していない多層アプリケーションが多数あります。 コンテナー化されたワークロードとして実行できるかどうかを判断するには、Kubernetes をコンテナー オーケストレーターとして使用して評価します。 管理オーバーヘッドをさらに最小限に抑えるには、簡単なデプロイ エクスペリエンスやスケーリング機能などを含めた Azure Kubernetes Service をテストします。

                **メモ:** このラボをご自分のペースでクリックして進めることができる、 **[ラボの対話型シミュレーション](https://mslabs.cloudguides.com/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%2015)** が用意されています。 対話型シミュレーションとホストされたラボの間に若干の違いがある場合がありますが、示されている主要な概念とアイデアは同じです。 

## <a name="objectives"></a>目標

このラボでは、次のことを行います。

+ タスク 1:Microsoft.Kubernetes および Microsoft.KubernetesConfiguration リソース プロバイダーを登録します。
+ タスク 2:Azure Kubernetes Service クラスターをデプロイする
+ タスク 3:Azure Kubernetes Service クラスターにポッドをデプロイする
+ タスク 4:Azure Kubernetes Service クラスターでコンテナー化されたワークロードをスケーリングする

## <a name="estimated-timing-40-minutes"></a>推定時間:40 分

## <a name="architecture-diagram"></a>アーキテクチャの図

![image](../media/lab09c.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1"></a>演習 1

#### <a name="task-1-register-the-microsoftkubernetes-and-microsoftkubernetesconfiguration-resource-providers"></a>タスク 1:Microsoft.Kubernetes および Microsoft.KubernetesConfiguration リソース プロバイダーを登録します。

このタスクでは、Azure Kubernetes Service クラスターをデプロイするために必要なリソース プロバイダーを登録します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal の右上にあるアイコンをクリックして **Azure Cloud Shell** を開きます。

1. **Bash** または **PowerShell** の選択を求めるメッセージが表示されたら、 **[PowerShell]** を選択します。

    >**注**: **Cloud Shell** の初回起動時に "**ストレージがマウントされていません**" というメッセージが表示された場合は、このラボで使用しているサブスクリプションを選択し、**[ストレージの作成]** を選択します。

1. [Cloud Shell] ペインから、次のコマンドを実行して、Microsoft.Kubernetes と Microsoft.KubernetesConfiguration のリソース プロバイダーを登録します。

   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes

   Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
   ```

1. [Cloud Shell] ペインを閉じます。

#### <a name="task-2-deploy-an-azure-kubernetes-service-cluster"></a>タスク 2:Azure Kubernetes Service クラスターをデプロイする

このタスクでは、Azure portal を使用して Azure Kubernetes Service クラスターをデプロイします。

1. Azure portal で、**[Kubernetes サービス]** を検索してから、**[Kubernetes サービス]** ブレードで **[作成]** をクリックし、**[Kubernetes クラスターの作成]** をクリックします。

1. **[Kubernetes クラスターの作成]** ブレードの **[基本]** タブで、次の設定を指定します (他の設定は既定値のままにします)。

    | 設定 | 値 |
    | ---- | ---- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | 新しいリソース グループ **az104-09c-rg1** の名前 |
    | クラスターのプリセット構成 | **Dev/Test ($)** |
    | Kubernetes クラスター名 | **az104-9c-aks1** |
    | リージョン | Kubernetes クラスターをプロビジョニングできるリージョンの名前 |
    | 可用性ゾーン | **なし** (すべてのチェックボックスをオフにする) |
    | Kubernetes バージョン | 既定値を受け入れる |
    | API サーバーの可用性 | 既定値を受け入れる |
    | ノード サイズ | 既定値を受け入れる |
    | スケーリング方法 | **[手動]** |
    | ノード数 | **1** |

1. **[次へ: ノード プール >]** をクリックし **[Kubernetes クラスターを作成]** ブレードの **[ノード プール]** タブで、次の設定を指定します (他の設定は既定値のままにします)。

    | 設定 | 値 |
    | ---- | ---- |
    | 仮想ノードを有効にする | **無効** (既定) |

1. **[次へ: アクセス >]** をクリックし、 **[Kubernetes クラスターの作成]** ブレードの **[アクセス]** タブの設定は既定値のままにします。

    | 設定 | 値 |
    | ---- | ---- |
    | リソース ID | **システム割り当てマネージド ID** |
    | 認証方法 | **Kubernetes RBAC を使用したローカル アカウント** |

1. **[次へ: ネットワーク >]** をクリックし、 **[Kubernetes クラスターの作成]** ブレードの **[ネットワーク]** タブで、次の設定を指定します (その他の設定は既定値のままにします)。

    | 設定 | 値 |
    | ---- | ---- |
    | ネットワーク構成 | **kubenet** |
    | DNS 名プレフィックス | 有効な、グローバルに一意の DNS プレフィックス|

1. **[次へ: 統合 >]** をクリックし、 **[Kubernetes クラスターの作成]** ブレードの **[統合]** タブで、 **[コンテナーの監視]** を **[無効]** に設定し、 **[確認および作成]** をクリックし、検証が成功したことを確認して、 **[作成]** をクリックします。

    >**注**:運用シナリオでは、監視を有効にします。 この場合、監視はラボでカバーされていないので無効になります。

    >**注**: デプロイが完了するまで待ちます。 これにはおよそ 10 分かかります。

#### <a name="task-3-deploy-pods-into-the-azure-kubernetes-service-cluster"></a>タスク 3:Azure Kubernetes Service クラスターにポッドをデプロイする

このタスクでは、ポッドを Azure Kubernetes Service クラスターにデプロイします。

1. デプロイ ブレードで、**[リソースに移動]** リンクをクリックします。

1. **[az104-9c-aks1** Kubernetes サービス] ブレードの **[設定]** セクションで、**[ノード プール]** をクリックします。

1. **[az104-9c-aks1 - ノード プール]** ブレードで、クラスターが 1 つのノードを持つ単一プールで構成されていることを確認します。

1. Azure portal の右上にあるアイコンをクリックして **Azure Cloud Shell** を開きます。

1. **Azure Cloud Shell** を **Bash** (黒の背景) に切り替えます。

1. [Cloud Shell] ウィンドウから、次のコマンドを実行して、AKS クラスターにアクセスする資格情報を取得します。

    ```sh
    RESOURCE_GROUP='az104-09c-rg1'

    AKS_CLUSTER='az104-9c-aks1'

    az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_CLUSTER
    ```

1. **Cloud Shell** ウィンドウから次のコマンドを実行して、AKS クラスターへの接続を確認します。

    ```sh
    kubectl get nodes
    ```

1. **Cloud Shell** ウィンドウで出力を確認し、この時点でクラスターが構成する 1 つのノードが**準備完了**状態を報告していることを確認します。

1. **Cloud Shell** ウィンドウから次のコマンドを実行して、Docker Hub から **nginx** イメージをデプロイします。

    ```sh
    kubectl create deployment nginx-deployment --image=nginx
    ```

    > **注**:デプロイの名前を入力するときは、必ず小文字を使用してください (nginx-deployment)。

1. **Cloud Shell** ウィンドウから次のコマンドを実行して、Kubernetes ポッドが作成されたことを確認します。

    ```sh
    kubectl get pods
    ```

1. **Cloud Shell** ウィンドウから次のコマンドを実行して、デプロイの状態を識別します。

    ```sh
    kubectl get deployment
    ```

1. **Cloud Shell** ウィンドウから次のコマンドを実行して、ポッドがインターネットから使用できるようにします。

    ```sh
    kubectl expose deployment nginx-deployment --port=80 --type=LoadBalancer
    ```

1. **Cloud Shell** ウィンドウから次のコマンドを実行して、パブリック IP アドレスがプロビジョニングされているかどうか識別します。

    ```sh
    kubectl get service
    ```

1. **nginx-deployment** エントリの **EXTERNAL-IP** 列の値が **\<pending\>** からパブリック IP アドレスに変わるまで、コマンドを再実行します。 **nginx-deployment** の **EXTERNAL-IP** 列のパブリック IP アドレスをメモします。

1. ブラウザーの画面を開き、前の手順で特定した IP アドレスに移動します。 ブラウザーのページに "**nginx へようこそ!** " のメッセージが表示されていることを確認します。 メッセージで応答します。

#### <a name="task-4-scale-containerized-workloads-in-the-azure-kubernetes-service-cluster"></a>タスク 4:Azure Kubernetes Service クラスターでコンテナー化されたワークロードをスケーリングする

このタスクでは、ポッドの数とクラスター ノードの数を水平方向にスケーリングします。

1. **Cloud Shell** ウィンドウから次のコマンドを実行し、ポッドの数を 2 に増やしてデプロイをスケーリングします。

    ```sh
    kubectl scale --replicas=2 deployment/nginx-deployment
    ```

1. **Cloud Shell** ウィンドウから次のコマンドを実行して、デプロイのスケーリング結果を確認します。

    ```sh
    kubectl get pods
    ```

    > **注**:コマンドの出力を確認し、ポッドの数が 2 個に増加したことを確認します。

1. **Cloud Shell** ウィンドウから次のコマンドを実行し、ノード数を 2 に増やしてクラスターをスケールアウトします。

    ```sh
    RESOURCE_GROUP='az104-09c-rg1'

    AKS_CLUSTER='az104-9c-aks1'

    az aks scale --resource-group $RESOURCE_GROUP --name $AKS_CLUSTER --node-count 2
    ```

    > **注**:追加ノードのプロビジョニングが完了するまで待ちます。 これには 3 分ほどかかる場合があります。 失敗した場合は、`az aks scale` コマンドを再実行します。

1. **Cloud Shell** ウィンドウから次の操作を実行して、クラスターのスケーリング結果を確認します。

    ```sh
    kubectl get nodes
    ```

    > **注**:コマンドの出力を確認し、ノード数が 2 個に増加したことを確認します。

1. **Cloud Shell** ウィンドウから次のコマンドを実行して、デプロイをスケーリングします。

    ```sh
    kubectl scale --replicas=10 deployment/nginx-deployment
    ```

1. **Cloud Shell** ウィンドウから次のコマンドを実行して、デプロイのスケーリング結果を確認します。

    ```sh
    kubectl get pods
    ```

    > **注**:コマンドの出力を確認し、ポッドの数が 10 個に増加したことを確認します。

1. **[Cloud Shell]** ウィンドウから次のコマンドを実行して、クラスター ノードにまたがるポッドの配分を確認します。

    ```sh
    kubectl get pod -o=custom-columns=NODE:.spec.nodeName,POD:.metadata.name
    ```

    > **注**:コマンドの出力を確認し、ポッドが両方のノードに分散されていることを確認します。

1. **Cloud Shell** ウィンドウから次のコマンドを実行して、デプロイを削除します。

    ```sh
    kubectl delete deployment nginx-deployment
    ```

1. **[Cloud Shell]** ペインを閉じます。

#### <a name="clean-up-resources"></a>リソースをクリーンアップする

>**注**:新規に作成し、使用しなくなったすべての Azure リソースを削除することを忘れないでください。 使用していないリソースを削除することで、予期しない料金が発生しなくなります。

>**注**:ラボのリソースをすぐに削除できなくても心配する必要はありません。 リソースに依存関係が存在し、削除に時間がかかる場合があります。 リソースの使用状況を監視することは管理者の一般的なタスクであるため、ポータルでリソースを定期的にチェックして、クリーンアップの進捗を確認するようにしてください。 

1. Azure portal で、**Cloud Shell** ウィンドウ内で **Bash** シェル セッションを開きます。

1. 次のコマンドを実行して、このモジュールのラボ全体で作成したすべてのリソース グループのリストを表示します。

   ```sh
   az group list --query "[?starts_with(name,'az104-09c')].name" --output tsv
   ```

1. 次のコマンドを実行して、このモジュールのラボ全体を通して作成したすべてのリソース グループを削除します。

   ```sh
   az group list --query "[?starts_with(name,'az104-09c')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

    >**注**:コマンドは非同期に実行されるので (--nowait パラメーターで決定される)、同じ Bash セッション内ですぐに別の Azure CLI コマンドを実行できますが、リソース グループが実際に削除されるまでに数分かかります。

#### <a name="review"></a>確認

このラボでは、次のことを行いました。

+ Azure Kubernetes Service クラスターをデプロイしました
+ Azure Kubernetes Service クラスターにポッドをデプロイしました
+ Azure Kubernetes Service クラスターでコンテナー化されたワークロードをスケーリングしました
