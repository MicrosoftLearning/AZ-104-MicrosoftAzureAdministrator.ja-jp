---
lab:
  title: 11 - 監視の実装
  module: Administer Monitoring
---

# <a name="lab-11---implement-monitoring"></a>ラボ 11 - 監視を実装する
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

You need to evaluate Azure functionality that would provide insight into performance and configuration of Azure resources, focusing in particular on Azure virtual machines. To accomplish this, you intend to examine the capabilities of Azure Monitor, including Log Analytics.

対話型ガイド形式でこのラボをプレビューするには、 **[ここをクリックしてください](https://mslabs.cloudguides.com/en-us/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%2017)** 。

## <a name="objectives"></a>目標

このラボでは、次のことを行います。

+ タスク 1:ラボ環境をプロビジョニングする
+ タスク 2: Microsoft.Insights と Microsoft.AlertsManagement リソース プロバイダーを登録する
+ タスク 3:Azure Log Analytics ワークスペースと Azure 自動化ベースのソリューションを作成して構成する
+ タスク 4:Azure 仮想マシンの既定の監視設定をレビューする
+ タスク 5:Azure 仮想マシンの診断設定を構成する
+ タスク 6:Azure Monitor の機能をレビューする
+ タスク 7:Azure ログ分析機能をレビューする

## <a name="estimated-timing-45-minutes"></a>推定時間:45 分

## <a name="instructions"></a>Instructions

### <a name="exercise-1"></a>演習 1

#### <a name="task-1-provision-the-lab-environment"></a>タスク 1:ラボ環境をプロビジョニングする

このタスクでは、監視シナリオのテストに使用する仮想マシンをデプロイします。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal の右上にあるアイコンをクリックして **Azure Cloud Shell** を開きます。

1. **Bash** または **PowerShell** の選択を求めるメッセージが表示されたら、 **[PowerShell]** を選択します。

    >**注**: **Cloud Shell** の初回起動時に **"ストレージがマウントされていません"** というメッセージが表示された場合は、このラボで使用しているサブスクリプションを選択し、**[ストレージの作成]** を選択します。

1. Cloud Shell ウィンドウのツールバーで、 **[ファイルのアップロード/ダウンロード]** アイコンをクリックし、ドロップダウン メニューで **[アップロード]** を選択して、ファイル **\\Allfiles\\Labs\\11\\az104-11-vm-template.json** および **\\Allfiles\\Labs\\11\\az104-11-vm-parameters.json** を Cloud Shell ホーム ディレクトリにアップロードします。

1. Edit the Parameters file you just uploaded and change the password. If you need help editing the file in the Shell please ask your instructor for assistance. As a best practice, secrets, like passwords, should be more securely stored in the Key Vault. 

1. Cloud Shell ウィンドウから次のコマンドを実行して、仮想マシンをホストするリソース グループを作成します (`[Azure_region]` プレースホルダーを、Azure 仮想マシンをデプロイする Azure リージョンの名前に置き換えます)。

    >**注**:**ワークスペース マッピングのドキュメント**で参照されている [Log Analytics ワークスペースリージョン](https://docs.microsoft.com/en-us/azure/automation/how-to/region-mappings)としてリストされているリージョンのいずれかを選択してください

   ```powershell
   $location = '[Azure_region]'

   $rgName = 'az104-11-rg0'

   New-AzResourceGroup -Name $rgName -Location $location
   ```

1. [Cloud Shell] ウィンドウで、次のコマンドを実行して最初の仮想ネットワークを作成し、アップロードしたテンプレートとパラメーター ファイルを使用して仮想マシンをデプロイします。

   ```powershell
   New-AzResourceGroupDeployment `
      -ResourceGroupName $rgName `
      -TemplateFile $HOME/az104-11-vm-template.json `
      -TemplateParameterFile $HOME/az104-11-vm-parameters.json `
      -AsJob
   ```

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Do not wait for the deployment to complete but instead proceed to the next task. The deployment should take about 3 minutes.

#### <a name="task-2-register-the-microsoftinsights-and-microsoftalertsmanagement-resource-providers"></a>タスク 2:Microsoft.Insights および Microsoft.AlertsManagement リソース プロバイダーを登録します。

1. [Cloud Shell] ウィンドウから、次のコマンドを実行して、Microsoft.Insights および Microsoft.AlertsManagement リソース プロバイダーを登録します。

   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Insights

   Register-AzResourceProvider -ProviderNamespace Microsoft.AlertsManagement
   ```

1. [Cloud Shell] ウィンドウを最小化します (ただし、閉じないでください)。

#### <a name="task-3-create-and-configure-an-azure-log-analytics-workspace-and-azure-automation-based-solutions"></a>タスク 3:Azure Log Analytics ワークスペースと Azure 自動化ベースのソリューションを作成して構成する

このタスクでは、Azure Log Analytics ワークスペースと Azure 自動化ベースのソリューションを作成して構成します。

1. Azure portal で、**[Log Analytics ワークスペース]** を検索して選択し、**[Log Analytics ワークスペース]** ブレードで **[作成]** を選択します。

1. **[Log Analytics ワークスペースの作成]** ブレードの **[基本]** タブで、次の設定を入力して、**[確認および作成]** をクリックし、**[作成]** をクリックします。

    | 設定 | 値 |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | 新しいリソース グループ **az104-11-rg1** の名前 |
    | Log Analytics ワークスペース | 任意の一意の名前 |
    | リージョン | 前のタスクで仮想マシンをデプロイした Azure リージョンの名前 |

    >**注**: 前のタスクで仮想マシンをデプロイしたリージョンを必ず指定してください。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the deployment to complete. The deployment should take about 1 minute.

1. Azure portal で **[Automation アカウント]** を検索して選択し、**[Automation アカウント]** ブレードで **[作成]** をクリックします。

1. **[自動アカウントの作成]** ブレードで、次の設定を指定し、検証時に **[レビュー + 作成]** をクリックして、 **[作成]** をクリックします。

    | 設定 | 値 |
    | --- | --- |
    | Automation アカウント名 | 任意の一意の名前 |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-11-rg1** |
    | リージョン | [ワークスペース マッピングに関するドキュメント](https://docs.microsoft.com/en-us/azure/automation/how-to/region-mappings)に基づいて決定された Azure リージョンの名前 |

    >**注**:[ワークスペース マッピングのドキュメント](https://docs.microsoft.com/en-us/azure/automation/how-to/region-mappings)に基づいて Azure リージョンを指定していることを確認します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the deployment to complete. The deployment might take about 3 minutes.

1. **[リソースに移動]** をクリックします。

1. [Automation アカウント] ブレードの **[構成管理]** セクションで、**[インベントリ]** をクリックします。

1. **[インベントリ]** ウィンドウの **[Log Analytics ワークスペース]** ドロップダウン リストで、このタスクで前に作成した Log Analytics ワークスペースを選択し、**[有効]** をクリックします。

    >Azure リソースのパフォーマンスと構成に関する分析情報を提供する Azure の機能を、特に 仮想マシンに焦点を当てて評価します。

    >**注**: これにより、**変更の追跡**ソリューションも自動的にインストールされます。

1. [Automation アカウント] ブレードの **[更新管理]** セクションで、**[更新管理]** をクリックし、**[有効]** をクリックします。

    >これを実現するために、Log Analytics を含む、Azure Monitor の機能を調べる予定です。

#### <a name="task-4-review-default-monitoring-settings-of-azure-virtual-machines"></a>タスク 4:Azure 仮想マシンの既定の監視設定をレビューする

このタスクでは、Azure 仮想マシンの既定の監視設定を確認します。

1. Azure portal で **[仮想マシン]** を検索して選択し、**[仮想マシン]** ブレードで **[az104-11-vm0]** を選択します。

1. **[az104-11-vm0]** ブレードの **[監視]** セクションで、**[指標]** をクリックします。

1. **[az104-11-vm0 \| 指標]** ブレードの既定のグラフでは、使用可能な**指標名前空間**は**仮想マシン ホスト**のみです。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This is expected, since no guest-level diagnostic settings have been configured yet. You do have, however, the option of enabling guest memory metrics directly from the <bpt id="p1">**</bpt>Metrics Namespace<ept id="p1">**</ept> drop down-list. You will enable it later in this exercise.

1. **[メトリック]** ドロップダウン リストで、使用可能なメトリックの一覧を確認します。

    >**注**: この一覧には、ゲストレベルのメトリックにアクセスすることなく、仮想マシンのホストから収集できる CPU、ディスク、ネットワーク関連の幅広いメトリックが含まれています。

1. **[メトリック]** ドロップダウン リストで **[CPU 使用率]** を選択し、**[集計]** ドロップダウン リストで **[平均]** を選択し、結果のグラフを確認します。

#### <a name="task-5-configure-azure-virtual-machine-diagnostic-settings"></a>タスク 5:Azure 仮想マシンの診断設定を構成する

このタスクでは、Azure 仮想マシンの診断設定を構成します。

1. **[az104-11-vm0]** ブレードの **[監視]** セクションで、**[診断設定]** をクリックします。

1. **[az104-11-vm0 \| 診断設定]** ブレードの **[概要]** タブで、 **[ゲスト レベルの監視を有効にする]** をクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the operation to take effect. This might take about 3 minutes.

1. **[az104-11-vm0 \| 診断設定]** ブレードの **[パフォーマンス カウンター]** タブに切り替え、使用可能なカウンターを確認します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: By default, CPU, memory, disk, and network counters are enabled. You can switch to the <bpt id="p1">**</bpt>Custom<ept id="p1">**</ept> view for more detailed listing.

1. **[az104-11-vm0 \| 診断設定]** ブレードの **[ログ]** タブに切り替え、使用可能なイベント ログの収集オプションを確認します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: By default, log collection includes critical, error, and warning entries from the Application Log and System log, as well as Audit failure entries from the Security log. Here as well you can switch to the <bpt id="p1">**</bpt>Custom<ept id="p1">**</ept> view for more detailed configuration settings.

1. **[az104-11-vm0]** ブレードの **[監視]** セクションで、 **[Log Analytics エージェント]** をクリックしてから **[有効化]** をクリックします。

1. **[az104-11-vm0 - ログ]** ブレードで、以前このタブで作成した Log Analytics ワークスペースが **[Log Analytics ワークスペースの選択]** ドロップダウン リストで選択されていることを確認して、**[有効]** をクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Do not wait for the operation to complete but instead proceed to the next step. The operation might take about 5 minutes.

1. **[az104-11-vm0 \| ログ]** ブレードの **[監視]** セクションで、 **[メトリック]** をクリックします。

1. **[az104-11-vm0 \| 指標]** ブレードの既定のグラフでは、この時点で **[メトリックスの名前空間]** ドロップダウン リストに、 **[仮想マシン ホスト]** エントリに加えて**ゲスト (クラシック)** エントリも含まれていることに注意してください。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This is expected, since you enabled guest-level diagnostic settings. You also have the option to <bpt id="p1">**</bpt>Enable new guest memory metrics<ept id="p1">**</ept>.

1. **[メトリック名前空間]** ドロップダウン リストで、**[ゲスト (クラシック)]** エントリを選択します。

1. **[メトリック]** ドロップダウン リストで、使用可能なメトリックの一覧を確認します。

    >**注**: この一覧には、ホストレベルの監視のみに依存している場合には使用できない追加のゲストレベルのメトリックが含まれています。

1. **[メトリック]** ドロップダウン リストの、 **[集計]** ドロップダウン リストで **[メモリ\\使用可能バイト数]** を選択し、 **[最大]** を選択して、結果のグラフを確認します。

#### <a name="task-6-review-azure-monitor-functionality"></a>タスク 6:Azure Monitor の機能をレビューする

1. Azure portal で、 **[モニター]** を検索して選択し、 **[モニター \| 概要]** ブレードで、 **[メトリック]** をクリックします。

1. **[スコープの選択]** ブレードの **[参照]** タブで **az104-11-rg0** リソース グループに移動してそれを展開し、そのリソース グループ内にある **az104-11-vm0** 仮想マシンの横にあるチェックボックスを選択し、**[適用]** をクリックします。

    >**注**: これにより、**[az104-11-vm0 - メトリック]** ブレードで使用できるビューやオプションと同じものが提供されます。

1. **[メトリック]** ドロップダウン リストで **[CPU 使用率]** を選択し、**[集計]** ドロップダウン リストで **[平均]** を選択し、結果のグラフを確認します。

1. **[モニター \| メトリック]** ブレードの **[az104-11-vm0 の平均 CPU 使用率]** ウィンドウで、 **[新しいアラート ルール]** をクリックします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Creating an alert rule from Metrics is not supported for metrics from the Guest (classic) metric namespace. This can be accomplished by using Azure Resource Manager templates, as described in the document <bpt id="p1">[</bpt>Send Guest OS metrics to the Azure Monitor metric store using a Resource Manager template for a Windows virtual machine<ept id="p1">](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm)</ept>

1. **[アラート ルールの作成]** ブレードの **[条件]** セクションで、既存の条件エントリをクリックします。

1. **[シグナル ロジックの構成]** ブレードのシグナルのリスト内にある **[アラート ロジック]** セクションで、次の設定を指定し (他の設定は既定値のままにします)、**[完了]** をクリックします。

    | 設定 | 値 |
    | --- | --- |
    | しきい値 | **静的** |
    | 演算子 | **より大きい** |
    | 集計の種類 | **Average** |
    | しきい値 | **2** |
    | 集計の粒度 (期間) | **1 分** |
    | 評価の頻度 | **1 分ごと** |

1. **次へ: アクション >** をクリックし、**アラート ルールの作成** ブレードの **アクション グループ** セクションで、 **+ アクション グループの作成** ボタンをクリックします。

1. **[アクション グループの作成]** ブレードの **[基本]** タブで、次の設定を指定し (他の設定は既定値のままにします)、 **[次へ:通知 >]** をクリックします。

    | 設定 | 値 |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-11-rg1** |
    | アクション グループ名 | **az104-11-ag1** |
    | Display name | **az104-11-ag1** |

1. On the <bpt id="p1">**</bpt>Notifications<ept id="p1">**</ept> tab of the <bpt id="p2">**</bpt>Create an action group<ept id="p2">**</ept> blade, in the <bpt id="p3">**</bpt>Notification type<ept id="p3">**</ept> drop-down list, select <bpt id="p4">**</bpt>Email/SMS message/Push/Voice<ept id="p4">**</ept>. In the <bpt id="p1">**</bpt>Name<ept id="p1">**</ept> text box, type <bpt id="p2">**</bpt>admin email<ept id="p2">**</ept>. Click the <bpt id="p1">**</bpt>Edit details<ept id="p1">**</ept> (pencil) icon.

1. **[電子メール/SMS/プッシュ/音声]** ブレードで、 **[電子メール]** チェックボックスをオンにし、 **[電子メール]** テキストボックスに自分のメール アドレスを入力し、他のユーザーに既定値を残して **[OK]** をクリックし、 **[アクション グループの作成]** ブレードの **[通知]** タブに戻って **[次へ:操作 >]** を選択します。

1. **[アクション グループの作成]** ブレードの **[アクション]** タブにある **[アクションの種類]** ドロップダウン リストで、使用可能なアイテムを確認し (変更は行いません)、**[確認および作成]** を選択します。

1. **[アクション グループの作成]** ブレードの **[レビュー + 作成]** タブで、**[作成]** を選択します。

1. **[アラート ルールの作成]** ブレードに戻って **[次へ: 詳細 >]** をクリックし、 **[アラート ルールの詳細]** セクションで、次の設定を指定します (他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | アラート ルール名 | **テストしきい値を超えた CPU の割合** |
    | アラート ルールの説明 | **テストしきい値を超えた CPU の割合** |
    | 重大度 | **重大度 3** |
    | 作成時に有効化 | **あり** |

1. **[レビューと作成]** をクリックし、 **[レビューと作成]** タブで **[作成]** をクリックします。

    >**注**: メトリックの警告ルールがアクティブになるまでに最大 10 分かかることがあります。

1. Azure portal で **[仮想マシン]** を検索して選択し、**[仮想マシン]** ブレードで **[az104-11-vm0]** を選択します。

1. **[az104-11-vm0]** ブレードで **[接続]** をクリックし、ドロップダウン メニューで **[RDP]** をクリックし、**[RDP で接続する]** ブレードで **[RDP ファイルのダウンロード]** をクリックして、プロンプトに従ってリモート デスクトップ セッションを開始します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This step refers to connecting via Remote Desktop from a Windows computer. On a Mac, you can use Remote Desktop Client from the Mac App Store and on Linux computers you can use an open source RDP client software.

    >**注**:ターゲット仮想マシンに接続する際は、警告メッセージを無視できます。

1. メッセージが表示されたら、 **[学生]** のユーザー名とパラメーター ファイルのパスワードを使用してサインインします。

1. リモート デスクトップ セッション内で、**[スタート]** をクリックし、**"Windows システム"** フォルダーを展開して、**[コマンド プロンプト]** をクリックします。

1. コマンド プロンプトから次のコマンドを実行して、**az104-11-vm0** Azure VMの CPU 使用率の増加をトリガーします。

   ```sh
   for /l %a in (0,0,1) do echo a
   ```

    >**注**:これにより、新しく作成されたアラート ルールのしきい値を超える CPU 使用率を増加させる無限ループが開始されます。

1. リモート デスクトップ セッションを開いたままにして、ラボ コンピューターに Azure portal を表示するブラウザーの画面に戻ります。

1. Azure portal で、**[監視]** ブレードに戻り、**[アラート]** をクリックします。

1. **Sev 3** アラートの数をメモし、**Sev 3** 行をクリックします。

    >**注**: 数分待ってから、**[更新]** をクリックする必要がある場合があります。

1. **[すべてのアラート]** ブレードで、生成されたアラートを確認します。

#### <a name="task-7-review-azure-log-analytics-functionality"></a>タスク 7:Azure ログ分析機能をレビューする

1. Azure portal で、**[監視]** ブレードに戻り、**[ログ]** をクリックします。

    >**注**: 初めて Log Analytics にアクセスする場合は、**[開始]** をクリックする必要がある場合があります。

1. 必要に応じて、 **[スコープの選択]** をクリックして、 **[スコープの選択]** ブレードで、 **[最近]** タブを選択し、**a104-11-vm0** を選択して、 **[適用]** をクリックします。

1. クエリ ウィンドウで、次のクエリを貼り付け、**[実行]** をクリックして、結果のグラフを確認します。

   ```sh
   // Virtual Machine available memory
   // Chart the VM's available memory over the last hour.
   InsightsMetrics
   | where TimeGenerated > ago(1h)
   | where Name == "AvailableMB"
   | project TimeGenerated, Name, Val
   | render timechart
   ```

    > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The query should not have any errors (indicated by red blocks on the right scroll bar). If the query will not paste without errors directly from the instructions, paste the query code into a text editor such as Notepad, and then copy and paste it into the query window from there.


1. ツールバーの **[クエリ]** をクリックし、 **[クエリ]** ペインで、 **[VM 可用性の追跡]** というタイトルを見つけ、ダブルクリックしてクエリ ウィンドウに入力し、タイトルの **[実行]** コマンド ボタンをクリックして、結果を確認します。

1. **[新しいクエリ 1]** タブで **[テーブル]** ヘッダーを選択し、 **[仮想マシン]** セクションのテーブルの一覧を確認します。

    >**注**: いくつかのテーブルの名前は、このラボで以前にインストールしたソリューションに対応しています。

1. **[VMComputer]** エントリの上にマウスを移動し、**[プレビュー プレビューの表示]** アイコンをクリックします。

1. データが使用可能な場合は、**[更新]** ウィンドウで **[エディターで使用]** をクリックします。

    >**注**: 更新データが使用可能になる前に、数分待つ必要がある場合があります。

#### <a name="clean-up-resources"></a>リソースをクリーンアップする

><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not see unexpected charges.

><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>:  Don't worry if the lab resources cannot be immediately removed. Sometimes resources have dependencies and take a longer time to delete. It is a common Administrator task to monitor resource usage, so just periodically review your resources in the Portal to see how the cleanup is going. 

1. Azure portal で、**[Cloud Shell]** ペイン内に **PowerShell** セッションを開きます。

1. 次のコマンドを実行して、このモジュールのラボ全体で作成したすべてのリソース グループのリストを表示します。

   ```powershell
   Get-AzResourceGroup -Name 'az104-11*'
   ```

1. 次のコマンドを実行して、このモジュールのラボ全体を通して作成したすべてのリソース グループを削除します。

   ```powershell
   Get-AzResourceGroup -Name 'az104-11*' | Remove-AzResourceGroup -Force -AsJob
   ```

    >**注**:このコマンドは非同期で実行されるため (-AsJob パラメーターによって決定されます)、同じ PowerShell セッション内で直後に別の PowerShell コマンドを実行できますが、リソース グループが実際に削除されるまでに数分かかります。

#### <a name="review"></a>確認

このラボでは、次のことを行いました。

+ ラボ環境をプロビジョニングしました
+ Azure Log Analytics ワークスペースと Azure 自動化ベースのソリューションを作成して構成しました
+ Azure 仮想マシンの既定の監視設定をレビューしました
+ Azure 仮想マシンの診断設定を構成しました
+ Azure Monitor の機能をレビューしました
+ Azure Log Analytics の機能をレビューしました
