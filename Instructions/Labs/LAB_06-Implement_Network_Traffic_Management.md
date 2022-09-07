---
lab:
  title: 06 - トラフィック管理を実装する
  module: Administer Network Traffic Management
---

# <a name="lab-06---implement-traffic-management"></a>ラボ 06 - トラフィック管理を実装する
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

You were tasked with testing managing network traffic targeting Azure virtual machines in the hub and spoke network topology, which Contoso considers implementing in its Azure environment (instead of creating the mesh topology, which you tested in the previous lab). This testing needs to include implementing connectivity between spokes by relying on user defined routes that force traffic to flow via the hub, as well as traffic distribution across virtual machines by using layer 4 and layer 7 load balancers. For this purpose, you intend to use Azure Load Balancer (layer 4) and Azure Application Gateway (layer 7).

対話型ガイド形式でこのラボをプレビューするには、 **[ここをクリックしてください](https://mslabs.cloudguides.com/en-us/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%2010)** 。

><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This lab, by default, requires total of 8 vCPUs available in the Standard_Dsv3 series in the region you choose for deployment, since it involves deployment of four Azure VMs of Standard_D2s_v3 SKU. If your students are using trial accounts, with the limit of 4 vCPUs, you can use a VM size that requires only one vCPU (such as Standard_B1s).

## <a name="objectives"></a>目標

このラボでは、次のことを行います。

+ タスク 1:ラボ環境をプロビジョニングする
+ タスク 2: ハブ アンド スポーク ネットワーク トポロジを構成する
+ タスク 3: 仮想ネットワーク ピアリングの推移性をテストする
+ タスク 4: ハブ アンド スポーク トポロジでルーティングを構成する
+ タスク 5:Azure Load Balancer を実装する
+ タスク 6:Azure Application Gateway を実装する

## <a name="estimated-timing-60-minutes"></a>推定時間:60 分

## <a name="architecture-diagram"></a>アーキテクチャの図

![image](../media/lab06.png)


## <a name="instructions"></a>Instructions

### <a name="exercise-1"></a>演習 1

#### <a name="task-1-provision-the-lab-environment"></a>タスク 1:ラボ環境をプロビジョニングする

In this task, you will deploy four virtual machines into the same Azure region. The first two will reside in a hub virtual network, while each of the remaining two will reside in a separate spoke virtual network.

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal の右上にあるアイコンをクリックして **Azure Cloud Shell** を開きます。

1. **Bash** または **PowerShell** の選択を求めるメッセージが表示されたら、 **[PowerShell]** を選択します。

    >**注**: **Cloud Shell** の初回起動時に "**ストレージがマウントされていません**" というメッセージが表示された場合は、このラボで使用しているサブスクリプションを選択し、**[ストレージの作成]** を選択します。

1. [Cloud Shell] ペインのツールバーで、 **[ファイルのアップロード/ダウンロード]** アイコンをクリックし、ドロップダウン メニューで **[アップロード]** をクリックして、ファイル **\\Allfiles\\Labs\\06\\az104-06-vms-loop-template.json** と **\\Allfiles\\Labs\\06\\az104-06-vms-loop-parameters.json** を Cloud Shell のホーム ディレクトリにアップロードします。

1. Edit the <bpt id="p1">**</bpt>Parameters<ept id="p1">**</ept> file you just uploaded and change the password. If you need help editing the file in the Shell please ask your instructor for assistance. As a best practice, secrets, like passwords, should be more securely stored in the Key Vault. 

1. [Cloud Shell] ペインから次を実行して、ラボ環境をホストする最初のリソース グループを作成します (`[Azure_region]` プレースホルダーを、Azure Virtual Machines をデプロイする Azure リージョンの名前に置き換えます)("(Get-AzLocation).Location" コマンドレットを使用して、リージョン一覧を取得できます)。

    ```powershell 
    $location = '[Azure_region]'
    ```
    
    ここで、リソース グループ名は次のようになります。
    ```powershell
    $rgName = 'az104-06-rg1'
    ```
    
    最後に、目的の場所にリソース グループを作成します。
    ```powershell
    New-AzResourceGroup -Name $rgName -Location $location
    ```


1. [Cloud Shell] ウィンドウで、次のコマンドを実行して 3 つの仮想ネットワークを作成し、アップロードしたテンプレートとパラメーター ファイルを使用して、4 つの Azure VM を作成します。

   ```powershell
   New-AzResourceGroupDeployment `
      -ResourceGroupName $rgName `
      -TemplateFile $HOME/az104-06-vms-loop-template.json `
      -TemplateParameterFile $HOME/az104-06-vms-loop-parameters.json
   ```

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the deployment to complete before proceeding to the next step. This should take about 5 minutes.

    >**注**:VM サイズが利用できないというエラーが発生した場合、インストラクターにサポートを依頼し、次の手順を試してください。
    > 1. CloudShell で `{}` ボタンをクリックし、左側のバーから **az104-06-vms-loop-parameters.json** を選択して、`vmSize` パラメーターの値をメモしておきます。
    > 1. ハブおよびスポーク ネットワーク トポロジの Azure 仮想マシンを対象としたネットワーク トラフィックの管理テストを担当していましたが、Contoso は Azure 環境での実装を検討しています (前のラボでテストしたメッシュ トポロジを作成する代わりに)。
    > 1. CloudShell で `az vm list-skus --location <Replace with your location> -o table --query "[? contains(name,'Standard_D2s')].name"` を実行します。
    > 1. このテストでは、ハブを経由してトラフィックを強制的に流すユーザー定義ルートに依存してスポーク間の接続を実装する必要があり、また、レイヤ 4 およびレイヤ 7 ロード バランサーを使用して仮想マシン間でのトラフィック分散を行う必要があります。
    > 1. この目的のために、Azure Load Balancer (レイヤー 4) と Azure Application Gateway (レイヤー 7) を使用する予定です。

1. Cloud Shell ウィンドウから、以下を実行して、前の手順でデプロイされた Azure VM に Network Watcher 拡張機能をインストールします。

   ```powershell
   $rgName = 'az104-06-rg1'
   $location = (Get-AzResourceGroup -ResourceGroupName $rgName).location
   $vmNames = (Get-AzVM -ResourceGroupName $rgName).Name

   foreach ($vmName in $vmNames) {
     Set-AzVMExtension `
     -ResourceGroupName $rgName `
     -Location $location `
     -VMName $vmName `
     -Name 'networkWatcherAgent' `
     -Publisher 'Microsoft.Azure.NetworkWatcher' `
     -Type 'NetworkWatcherAgentWindows' `
     -TypeHandlerVersion '1.4'
   }
   ```

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the deployment to complete before proceeding to the next step. This should take about 5 minutes.



1. [Cloud Shell] ペインを閉じます。

#### <a name="task-2-configure-the-hub-and-spoke-network-topology"></a>タスク 2: ハブ アンド スポーク ネットワーク トポロジを構成する

このタスクでは、前のタスクで展開したバーチャル ネットワーク間のローカル ピアリングを構成して、ハブとスポークのネットワーク トポロジを作成します。

1. Azure portal で、 **[仮想ネットワーク]** を検索して選択します。

1. 前のタスクで作成したバーチャル ネットワークを確認します。

    >**注**:3 つのバーチャル ネットワークのデプロイに使用したテンプレートに、3 つのバーチャル ネットワークの IP アドレス範囲が重複しないようにします。

1. 仮想ネットワークの一覧で、**az104-06-vnet2** を選択します。

1. **[az104-06-vnet2]** ウィンドウで **[プロパティ]** を選択します。 

1. **[az104-06-vnet2 \| プロパティ]** ブレードで、 **[リソース ID]** プロパティの値を記録します。

1. 仮想ネットワークの一覧に戻り、**az104-06-vnet3** を選択します。

1. **[az104-06-vnet3]** ブレードで、 **[プロパティ]** を選択します。 

1. **[az104-06-vnet3 \| プロパティ]** ブレードで、 **[リソース ID]** プロパティの値を記録します。

    >**注**:このタスクの後半で、両方のバーチャル ネットワークの ResourceID プロパティの値が必要になります。

    >**注**:これは、バーチャル ネットワーク ピアリングを作成するときに、Azure Portal が新しくプロビジョニングされたバーチャル ネットワークを表示しないことがあるという問題に対処する回避策です。

1. 仮想ネットワークの一覧で、 **[az104-06-vnet01]**

1. **[az104-06-vnet01]** 仮想ネットワーク ブレードの **[設定]** セクションで、 **[ピアリング]** をクリックしてから、 **[+ 追加]** をクリックします。

1. 次の設定でピアリングを追加し (他のユーザーには既定値を残します)、**[追加]** をクリックします。

    | 設定 | 値 |
    | --- | --- |
    | この仮想ネットワーク: ピアリング リンク名 | **az104-06-vnet01_to_az104-06-vnet2** |
    | [Traffic to remote virtual network](リモート仮想ネットワークへのトラフィック) | **許可 (既定)** |
    | [Traffic forwarded from remote virtual network](リモート仮想ネットワークから転送されるトラフィック) | **この仮想ネットワークの外部から発信されるトラフィックをブロックする** |
    | 仮想ネットワーク ゲートウェイ | **なし (既定値)** |
    | リモート仮想ネットワーク: ピアリング リンク名 | **az104-06-vnet2_to_az104-06-vnet01** |
    | 仮想ネットワークのデプロイ モデル | **リソース マネージャー** |
    | リソース ID を知っている | enabled |
    | Resource ID | このタスクの前半で記録した **az104-06-vnet2** の resourceID パラメーターの値 |
    | [Traffic to remote virtual network](リモート仮想ネットワークへのトラフィック) | **許可 (既定)** |
    | [Traffic forwarded from remote virtual network](リモート仮想ネットワークから転送されるトラフィック) | **許可 (既定)** |
    | 仮想ネットワーク ゲートウェイ | **なし (既定値)** |

    >**注**: 操作が完了するまで待ちます。

    >**注**:この手順では、1 つは az104-06-vnet01 から az104-06-vnet2、もう 1 つは az104-06-vnet2 から az104-06-vnet01 への 2 つのグローバル ピアリングを確立します。

    >**注**: このラボで後ほど実装するスポーク仮想ネットワーク間のルーティングを容易にするために、 **[トラフィック転送を許可する]** を有効にする必要があります。

1. **[az104-06-vnet01]** 仮想ネットワーク ブレードの **[設定]** セクションで、 **[ピアリング]** をクリックしてから、 **[+ 追加]** をクリックします。

1. 次の設定でピアリングを追加し (他のユーザーには既定値を残します)、**[追加]** をクリックします。

    | 設定 | 値 |
    | --- | --- |
    | この仮想ネットワーク: ピアリング リンク名 | **az104-06-vnet01_to_az104-06-vnet3** |
    | [Traffic to remote virtual network](リモート仮想ネットワークへのトラフィック) | **許可 (既定)** |
    | [Traffic forwarded from remote virtual network](リモート仮想ネットワークから転送されるトラフィック) | **この仮想ネットワークの外部から発信されるトラフィックをブロックする** |
    | 仮想ネットワーク ゲートウェイ | **なし (既定値)** |
    | リモート仮想ネットワーク: ピアリング リンク名 | **az104-06-vnet3_to_az104-06-vnet01** |
    | 仮想ネットワークのデプロイ モデル | **リソース マネージャー** |
    | リソース ID を知っている | enabled |
    | Resource ID | このタスクの前半で記録した **az104-06-vnet3** の resourceID パラメーターの値 |
    | [Traffic to remote virtual network](リモート仮想ネットワークへのトラフィック) | **許可 (既定)** |
    | [Traffic forwarded from remote virtual network](リモート仮想ネットワークから転送されるトラフィック) | **許可 (既定)** |
    | 仮想ネットワーク ゲートウェイ | **なし (既定値)** |

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This step establishes two local peerings - one from az104-06-vnet01 to az104-06-vnet3 and the other from az104-06-vnet3 to az104-06-vnet01. This completes setting up the hub and spoke topology (with two spoke virtual networks).

    >**注**: このラボで後ほど実装するスポーク仮想ネットワーク間のルーティングを容易にするために、 **[トラフィック転送を許可する]** を有効にする必要があります。

#### <a name="task-3-test-transitivity-of-virtual-network-peering"></a>タスク 3: 仮想ネットワーク ピアリングの推移性をテストする

このタスクでは、Network Watcher を使用してバーチャル ネットワーク ピアリングの推移性をテストします。

1. Azure portal で、「**Network Watcher**」を検索して選びます。

1. **[Network Watcher]** ブレードで、Azure リージョンの一覧を展開し、使用しているリージョンでサービスが有効になっていることを確認します。 

1. **[Network Watcher]** ウィンドウで、**[接続のトラブルシューティング]** に移動します。

1. **[Network Watcher - 接続のトラブルシューティング]** ウィンドウで、次の設定でチェックを開始します (その他の設定は既定値のままにします)。

    | 設定 | [値] |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-06-rg1** |
    | 送信元の種類 | **仮想マシン** |
    | 仮想マシン | **az104-06-vm0** |
    | 宛先 | **手動で指定** |
    | URI、FQDN、または IPv4 | **10.62.0.4** |
    | Protocol | **TCP** |
    | 宛先ポート | **3389** |

    > **注**:**10.62.0.4** は、プライベート IP アドレス **az104-06-vm2** を表します。

1. **注**:このラボでは、デフォルトで、Standard_D2s_v3 SKU の Azure VM のデプロイが 4 つ含まれるため、デプロイ用に選択したリージョンの Standard_Dsv3 シリーズで使用可能な vCPU が合計 8 個必要です。

    > **注**:ハブバーチャル ネットワークは最初のスポークバーチャル ネットワークと直接ピアリングされるため、これは予期されます。

1. **[Network Watcher - 接続のトラブルシューティング]** ウィンドウで、次の設定でチェックを開始します (その他の設定は既定値のままにします)。

    | 設定 | [値] |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-06-rg1** |
    | 送信元の種類 | **仮想マシン** |
    | 仮想マシン | **az104-06-vm0** |
    | 宛先 | **手動で指定** |
    | URI、FQDN、または IPv4 | **10.63.0.4** |
    | Protocol | **TCP** |
    | 宛先ポート | **3389** |

    > **注**:**10.63.0.4** は、プライベート IP アドレス **az104-06-vm3** を表します。

1. 受講生が試用版アカウントを使用しており、vCPU が 4 つに制限されている場合は、1 つの vCPU のみを必要とする VM サイズ (Standard_B1s など) を使用できます。

    > **注**:ハブバーチャル ネットワークは 2 番目のスポークバーチャル ネットワークと直接ピアリングされるため、これは予期されます。

1. **[Network Watcher - 接続のトラブルシューティング]** ウィンドウで、次の設定でチェックを開始します (その他の設定は既定値のままにします)。

    | 設定 | [値] |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-06-rg1** |
    | 送信元の種類 | **仮想マシン** |
    | 仮想マシン | **az104-06-vm2** |
    | 宛先 | **手動で指定** |
    | URI、FQDN、または IPv4 | **10.63.0.4** |
    | Protocol | **TCP** |
    | 宛先ポート | **3389** |

1. Click <bpt id="p1">**</bpt>Check<ept id="p1">**</ept> and wait until results of the connectivity check are returned. Note that the status is <bpt id="p1">**</bpt>Unreachable<ept id="p1">**</ept>.

    > **注**:これは、2 つのスポークバーチャル ネットワークが互いにピアリングされないので、予想されます (バーチャル ネットワーク ピアリングは推移的ではありません)。

#### <a name="task-4-configure-routing-in-the-hub-and-spoke-topology"></a>タスク 4: ハブ アンド スポーク トポロジでルーティングを構成する

このタスクでは、**az104-06-vm0** 仮想マシンのネットワーク インターフェイスで IP 転送を有効にし、オペレーティング システム内でルーティングを有効にし、スポークバーチャル ネットワーク上でユーザー定義のルートを構成することにより、2 つのスポークバーチャル ネットワーク間のルーティングを構成およびテストします。

1. Azure portal で、「**仮想マシン**」を検索して選びます。

1. **[仮想マシン]** ウィンドウの仮想マシンの一覧で、**az104-06-vm0** をクリックします。

1. **[az104-06-vm0]** 仮想マシン ウィンドウの **[設定]** セクションで、**[ネットワーク]** をクリックします。

1. **[ネットワーク インターフェイス]** ラベルの横の **az104-06-nic0** のリンクをクリックし、**[az104-06-nic0]** ネットワーク インターフェイス ウィンドウの **[設定]** セクションで **[IP 構成]** をクリックします。

1. **[IP 転送]** を **[有効]** に設定し、変更を保存します。

   > **注**:この設定は、2 つのスポークバーチャル ネットワーク間でトラフィックをルーティングするルーターとして **az104-06-vm0** が機能するために必要です。

   > **注**:ここで、ルーティングをサポートするように **az104-06-vm0** 仮想マシンのオペレーティング システムを構成する必要があります。

1. Azure portal で、**[az104-06-vm0]** Azure 仮想マシン ウィンドウに戻り、**[概要]** をクリックします。

1. **az104-06-vm0** ブレードの **[操作]** セクションで、 **[コマンドの実行]** をクリックし、コマンドの一覧で **RunPowerShellScript** を選択します。

1. **[コマンド スクリプトの実行]** ウィンドウで、次のコマンドを入力して **[実行]** をクリックし、リモート アクセス Windows サーバー ロールをインストールします。

   ```powershell
   Install-WindowsFeature RemoteAccess -IncludeManagementTools
   ```

   > **注**: コマンドが正常に完了したことの確認を待ちます。

1. **[コマンド スクリプトの実行]** ウィンドウで、次のコマンドを入力して **[実行]** をクリックし、ルーティング ロール サービスをインストールします。

   ```powershell
   Install-WindowsFeature -Name Routing -IncludeManagementTools -IncludeAllSubFeature

   Install-WindowsFeature -Name "RSAT-RemoteAccess-Powershell"

   Install-RemoteAccess -VpnType RoutingOnly

   Get-NetAdapter | Set-NetIPInterface -Forwarding Enabled
   ```

   > **注**: コマンドが正常に完了したことの確認を待ちます。

   > **注**:次に、スポークバーチャル ネットワーク上でユーザー定義のルートを作成および構成する必要があります。

1. Azure portal で「**ルート テーブル**」を検索して選択し、**[ルート テーブル]** ウィンドウで **[+ 作成]** をクリックします。

1. 次の設定でルート テーブルを作成します (その他は既定値のままにします)。

    | 設定 | [値] |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-06-rg1** |
    | 場所 | 仮想ネットワークを作成した Azure リージョンの名前 |
    | 名前 | **az104-06-rt23** |
    | ゲートウェイのルートを伝達する | **No** |

1. Click <bpt id="p1">**</bpt>Review and Create<ept id="p1">**</ept>. Let validation occur, and click <bpt id="p1">**</bpt>Create<ept id="p1">**</ept> to submit your deployment.

   > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the route table to be created. This should take about 3 minutes.

1. **[リソースに移動]** をクリックします。

1. **[az104-06-rt23]** ルート テーブル ウィンドウの **[設定]** セクションで、**[ルート]** をクリックしてから **[+ 追加]** をクリックします。

1. 次の設定を使って新しいルートを追加します。

    | 設定 | 値 |
    | --- | --- |
    | ルート名 | **az104-06-route-vnet2-to-vnet3** |
    | アドレス プレフィックス送信先 | **[IP アドレス]** |
    | 宛先 IP アドレス/CIDR 範囲 | **10.63.0.0/20** |
    | ネクストホップの種類 | **仮想アプライアンス** |
    | 次ホップ アドレス | **10.60.0.4** |

1. **[OK]**

1. **[az104-06-rt23]** ルート テーブル ウィンドウの **[設定]** セクションで **[サブネット]** をクリックしてから、**[+ 関連付け]** をクリックします。

1. ルート テーブル **az104-06-rt23** を次のサブネットに関連付けます。

    | 設定 | 値 |
    | --- | --- |
    | 仮想ネットワーク | **az104-06-vnet2** |
    | サブネット | **subnet0** |

1. **[OK]**

1. **[ルート テーブル]** ウィンドウに戻り、**[+ 作成]** をクリックします。

1. 次の設定でルート テーブルを作成します (その他は既定値のままにします)。

    | 設定 | [値] |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-06-rg1** |
    | リージョン | 仮想ネットワークを作成した Azure リージョンの名前 |
    | 名前 | **az104-06-rt32** |
    | ゲートウェイのルートを伝達する | **No** |

1. Click Review and Create. Let validation occur, and hit Create to submit your deployment.

   > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the route table to be created. This should take about 3 minutes.

1. **[リソースに移動]** をクリックします。

1. **az104-06-rt32** ルート テーブル ブレードの **[設定]** セクションで、 **[ルート]** をクリックし、 **[+ 追加]** をクリックします。

1. 次の設定を使って新しいルートを追加します。

    | 設定 | 値 |
    | --- | --- |
    | ルート名 | **az104-06-route-vnet3-to-vnet2** |
    | アドレス プレフィックス送信先 | **[IP アドレス]** |
    | 宛先 IP アドレス/CIDR 範囲 | **10.62.0.0/20** |
    | ネクストホップの種類 | **仮想アプライアンス** |
    | 次ホップ アドレス | **10.60.0.4** |

1. **[OK]**

1. **az104-06-rt32** ルート テーブル ブレードに戻り、 **[設定]** セクションで **[サブネット]** をクリックし、 **[+ 関連付け]** をクリックします。

1. ルート テーブル **az104-06-rt32** を次のサブネットに関連付けます。

    | 設定 | 値 |
    | --- | --- |
    | 仮想ネットワーク | **az104-06-vnet3** |
    | サブネット | **subnet0** |

1. **[OK]**

1. Azure portal で、**[Network Watcher - 接続のトラブルシューティング]** ウィンドウに戻ります。

1. **[Network Watcher - 接続のトラブルシューティング]** ウィンドウで、次の設定でチェックを開始します (その他の設定は既定値のままにします)。

    | 設定 | [値] |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-06-rg1** |
    | 送信元の種類 | **仮想マシン** |
    | 仮想マシン | **az104-06-vm2** |
    | 宛先 | **手動で指定** |
    | URI、FQDN、または IPv4 | **10.63.0.4** |
    | Protocol | **TCP** |
    | 宛先ポート | **3389** |

1. Click <bpt id="p1">**</bpt>Check<ept id="p1">**</ept> and wait until results of the connectivity check are returned. Verify that the status is <bpt id="p1">**</bpt>Reachable<ept id="p1">**</ept>. Review the network path and note that the traffic was routed via <bpt id="p1">**</bpt>10.60.0.4<ept id="p1">**</ept>, assigned to the <bpt id="p2">**</bpt>az104-06-nic0<ept id="p2">**</ept> network adapter. If status is <bpt id="p1">**</bpt>Unreachable<ept id="p1">**</ept>, you should stop and then start az104-06-vm0.

    > **注**:これは想定どおりの結果です。スポークバーチャル ネットワーク間のトラフィックは、ルーターとして機能するハブバーチャル ネットワークにある仮想マシンを経由してルーティングされるためです。

    > **注**:**Network Watcher** を使用して、ネットワークのトポロジを表示できます。

#### <a name="task-5-implement-azure-load-balancer"></a>タスク 5:Azure Load Balancer を実装する

このタスクでは、ハブ仮想ネットワーク内の 2 つの Azure 仮想マシンの前に Azure ロード バランサーを実装します。

1. Azure portal で「**ロード バランサー**」を検索して選択し、 **[ロード バランサー]** ブレードで **[+ 作成]** をクリックします。

1. 次の設定でロード バランサーを作成し (その他の設定は既定値のままにします)、 **[次へ: フロントエンド IP 構成]** をクリックします。

    | 設定 | [値] |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-06-rg1** |
    | 名前 | **az104-06-lb4** |
    | リージョン | このラボでその他のすべてのリソースをデプロイした Azure リージョンの名前 |
    | SKU  | **Standard** |
    | Type | **Public** |
    | 階層 | **Regional** |
    
1. On the <bpt id="p1">**</bpt>Frontend IP configuration<ept id="p1">**</ept> tab, click <bpt id="p2">**</bpt>Add a frontend IP configuration<ept id="p2">**</ept> and use the following settings before clicking <bpt id="p3">**</bpt>OK<ept id="p3">**</ept> and then <bpt id="p4">**</bpt>Add<ept id="p4">**</ept>. When completed click <bpt id="p1">**</bpt>Next: Backend pools<ept id="p1">**</ept>. 
     
    | 設定 | [値] |
    | --- | --- |
    | 名前 | 任意の一意の名前 |
    | IP バージョン | IPv4 |
    | IP の種類 | IP アドレス |
    | パブリック IP アドレス | **新規作成** |
    | 名前 | **az104-06-pip4** |
    | 可用性ゾーン | **ゾーンなし** | 

1. On the <bpt id="p1">**</bpt>Backend pools<ept id="p1">**</ept> tab, click <bpt id="p2">**</bpt>Add a backend pool<ept id="p2">**</ept> with the following settings (leave others with their default values). Click <bpt id="p1">**</bpt>+ Add<ept id="p1">**</ept> (twice) and then click  <bpt id="p2">**</bpt>Next:Inbound rules<ept id="p2">**</ept>. 

    | 設定 | [値] |
    | --- | --- |
    | 名前 | **az104-06-lb4-be1** |
    | 仮想ネットワーク | **az104-06-vnet01** |
    | バックエンド プールの構成 | **NIC** | 
    | IP バージョン | **IPv4** |
    | **[追加]** をクリックして仮想マシンを追加します |  |
    | az104-06-vm0 | **チェックボックスをオンにします** |
    | az104-06-vm1 | **チェックボックスをオンにします** |


1. On the <bpt id="p1">**</bpt>Inbound rules<ept id="p1">**</ept> tab, click <bpt id="p2">**</bpt>Add a load balancing rule<ept id="p2">**</ept>. Add a load balancing rule with the following settings (leave others with their default values). When completed click <bpt id="p1">**</bpt>Add<ept id="p1">**</ept>.

    | 設定 | [値] |
    | --- | --- |
    | 名前 | **az104-06-lb4-lbrule1** |
    | IP バージョン | **IPv4** |
    | フロントエンド IP アドレス | **az104-06-pip4** |
    | バックエンド プール | **az104-06-lb4-be1** |    
    | Protocol | **TCP** |
    | Port | **80** |
    | バックエンド ポート | **80** |
    | 正常性プローブ | **新規作成** |
    | 名前 | **az104-06-lb4-hp1** |
    | Protocol | **TCP** |
    | Port | **80** |
    | Interval | **5** |
    | 異常のしきい値 | **2** |
    | 正常性プローブの作成ウィンドウを閉じます | **[OK]** | 
    | セッション永続化 | **なし** |
    | アイドル タイムアウト (分) | **4** |
    | TCP リセット | **Disabled** |
    | フローティング IP | **無効** |
    | アウトバウンド送信元ネットワーク アドレス変換 (SNAT) | **推奨** |

1. As you have time, review the other tabs, then click <bpt id="p1">**</bpt>Review and create<ept id="p1">**</ept>. Ensure there are no validation errors, then click <bpt id="p1">**</bpt>Create<ept id="p1">**</ept>. 

1. ロード バランサーがデプロイされるまで待ってから、 **[リソースに移動]** をクリックします。  

1. Select <bpt id="p1">**</bpt>Frontend IP configuration<ept id="p1">**</ept> from the Load Balancer resource page. Copy the IP address.

1. Open another browser tab and navigate to the IP address. Verify that the browser window displays the message <bpt id="p1">**</bpt>Hello World from az104-06-vm0<ept id="p1">**</ept> or <bpt id="p2">**</bpt>Hello World from az104-06-vm1<ept id="p2">**</ept>.

1. Refresh the window to verify the message changes to the other virtual machine. This demonstrates the load balancer rotating through the virtual machines.

    > **注**: 複数回更新するか、InPrivate モードで新しいブラウザー ウィンドウを開く必要がある場合があります。

#### <a name="task-6-implement-azure-application-gateway"></a>タスク 6:Azure Application Gateway を実装する

このタスクでは、スポークバーチャル ネットワーク内の 2 つの Azure 仮想マシンの前に Azure Application Gateway を実装します。

1. Azure portal で、「**仮想ネットワーク**」を検索して選択します。

1. **[仮想ネットワーク]** ウィンドウの仮想ネットワークの一覧で、**az104-06-vnet01** をクリックします。

1. **[az104-06-vnet01]** 仮想ネットワーク ウィンドウの **[設定]** セクションで、**[サブネット]** をクリックし、**[+ サブネット]** をクリックします。

1. 次の設定でサブネットを追加します (その他の設定は既定値のままにします)。

    | 設定 | [値] |
    | --- | --- |
    | 名前 | **subnet-appgw** |
    | サブネットのアドレス範囲 | **10.60.3.224/27** |

1. **[保存]**

    > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This subnet will be used by the Azure Application Gateway instances, which you will deploy later in this task. The Application Gateway requires a dedicated subnet of /27 or larger size.

1. Azure portal で「**アプリケーション ゲートウェイ**」を検索して選択し、**[アプリケーション ゲートウェイ]** ウィンドウで **[+ 作成]** をクリックします。

1. **[基本]** タブで、次の設定を指定します (他の設定は既定値のままにしておきます)。

    | 設定 | [値] |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-06-rg1** |
    | アプリケーション ゲートウェイ名 | **az104-06-appgw5** |
    | リージョン | このラボでその他のすべてのリソースをデプロイした Azure リージョンの名前 |
    | レベル | **標準 V2** |
    | 自動スケーリングを有効にする | **いいえ** |
    | インスタンス数 | **2** |
    | 可用性ゾーン | **なし** |
    | HTTP2 | **Disabled** |
    | 仮想ネットワーク | **az104-06-vnet01** |
    | Subnet | **subnet-appgw (10.60.3.224/27)** |

1. Click <bpt id="p1">**</bpt>Next: Frontends &gt;<ept id="p1">**</ept> and specify the following settings (leave others with their default values). When complete, click <bpt id="p1">**</bpt>OK<ept id="p1">**</ept>. 

    | 設定 | 値 |
    | --- | --- |
    | フロントエンド IP アドレス タイプ | **Public** |
    | パブリック IP アドレス| **[新規追加]** | 
    | 名前 | **az104-06-pip5** |
    | 可用性ゾーン | **なし** |

1. このタスクでは、同じ Azure リージョンに 4 つの仮想マシンをデプロイします。

    | 設定 | [値] |
    | --- | --- |
    | 名前 | **az104-06-appgw5-be1** |
    | [Add backend pool without targets](ターゲットを持たないバックエンド プールを追加する) | **いいえ** |
    | IP アドレスまたは FQDN | **10.62.0.4** | 
    | IP アドレスまたは FQDN | **10.63.0.4** |

    > **注**:ターゲットは、スポークバーチャル ネットワーク **az104-06-vm2** および **az104-06-vm3** 内の仮想マシンのプライベート IP アドレスを表します。

1. 最初の 2 つはハブバーチャル ネットワークに存在し、残りはそれぞれ別個のスポークバーチャル ネットワークに存在します。

    | 設定 | 値 |
    | --- | --- |
    | 規則の名前 | **az104-06-appgw5-rl1** |
    | Priority | "**10**" |
    | リスナー名 | **az104-06-appgw5-rl1l1** |
    | フロントエンド IP | **Public** |
    | Protocol | **HTTP** |
    | Port | **80** |
    | リスナーの種類 | **Basic** |
    | エラー ページの URL | **いいえ** |

1. Switch to the <bpt id="p1">**</bpt>Backend targets<ept id="p1">**</ept> tab and specify the following settings (leave others with their default values). When completed click <bpt id="p1">**</bpt>Add<ept id="p1">**</ept> (twice).  

    | 設定 | 値 |
    | --- | --- |
    | 変換後の型 | **バックエンド プール** |
    | バックエンド ターゲット | **az104-06-appgw5-be1** |
    | バックエンド設定 | **[新規追加]** |
    | バックエンド設定名 | **az104-06-appgw5-http1** |
    | バックエンド プロトコル | **HTTP** |
    | バックエンド ポート | **80** |
    | 追加設定 | **既定値のままにします** |
    | ホスト名 | **既定値のままにします** |

1. **[次へ: タグ >]** をクリックし、 **[次へ: 確認と作成 >]** 、 **[作成]** の順にクリックします。

    > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the Application Gateway instance to be created. This might take about 8 minutes.

1. Azure portal で「**アプリケーション ゲートウェイ**」を検索して選択し、**[アプリケーション ゲートウェイ]** ウィンドウで **az104-06-appgw5** をクリックします。

1. **az104-06-appgw5** アプリケーション ゲートウェイ ブレードで、 **[フロントエンド パブリック IP アドレス]** の値をコピーします。

1. 別のブラウザーのウィンドウを起動し、前の手順で識別した IP アドレスに移動します。

1. ブラウザー ウィンドウに、**Hello World from az104-06-vm2** または **Hello World from az104-06-vm3** というメッセージが表示されることを確認します。

1. ウィンドウを更新して、メッセージが他の仮想マシンに変更されることを確認します。 

    > **注**: 複数回更新するか、InPrivate モードで新しいブラウザー ウィンドウを開く必要がある場合があります。

    > **注**:複数のバーチャル ネットワーク上の仮想マシンを対象とすることは一般的な構成ではありませんが、Application Gateway が複数のバーチャル ネットワーク上の仮想マシンや、他の Azureリージョンのエンドポイント、さらには Azure の外部のエンドポイントを対象とできる点を示すことを目的としています。同じバーチャル ネットワーク内の仮想マシン間で負荷分散を行う Azure Load Balancer とは異なります。

#### <a name="clean-up-resources"></a>リソースをクリーンアップする

><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not see unexpected charges.

><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>:  Don't worry if the lab resources cannot be immediately removed. Sometimes resources have dependencies and take a longer time to delete. It is a common Administrator task to monitor resource usage, so just periodically review your resources in the Portal to see how the cleanup is going. 

1. Azure portal で、**[Cloud Shell]** ペイン内に **PowerShell** セッションを開きます。

1. 次のコマンドを実行して、このモジュールのラボ全体で作成したすべてのリソース グループのリストを表示します。

   ```powershell
   Get-AzResourceGroup -Name 'az104-06*'
   ```

1. 次のコマンドを実行して、このモジュールのラボ全体を通して作成したすべてのリソース グループを削除します。

   ```powershell
   Get-AzResourceGroup -Name 'az104-06*' | Remove-AzResourceGroup -Force -AsJob
   ```

    >**注**:このコマンドは非同期で実行されるため (-AsJob パラメーターによって決定されます)、同じ PowerShell セッション内で直後に別の PowerShell コマンドを実行できますが、リソース グループが実際に削除されるまでに数分かかります。

#### <a name="review"></a>確認

このラボでは、次のことを行いました。

+ ラボ環境をプロビジョニングしました
+ ハブとスポーク ネットワーク トポロジを構成しました
+ バーチャル ネットワーク ピアリングの推移性をテストしました
+ ハブとスポークのトポロジのルーティングを構成しました
+ Azure ロード バランサーを実装しました
+ Azure アプリケーション ゲートウェイを実装しました
