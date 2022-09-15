---
lab:
  title: 05 - サイト間接続を実装する
  module: Administer Intersite Connectivity
---

# <a name="lab-05---implement-intersite-connectivity"></a>ラボ 05 - サイト間の接続性を実装する
# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

Contoso has its datacenters in Boston, New York, and Seattle offices connected via a mesh wide-area network links, with full connectivity between them. You need to implement a lab environment that will reflect the topology of the Contoso's on-premises networks and verify its functionality.

<bpt id="p1">**</bpt>Note:<ept id="p1">**</ept> An <bpt id="p2">**</bpt><bpt id="p3">[</bpt>interactive lab simulation<ept id="p3">](https://mslabs.cloudguides.com/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%209)</ept><ept id="p2">**</ept> is available that allows you to click through this lab at your own pace. You may find slight differences between the interactive simulation and the hosted lab, but the core concepts and ideas being demonstrated are the same. 

## <a name="objectives"></a>目標

このラボでは、次のことを行います。

+ タスク 1:ラボ環境をプロビジョニングする
+ タスク 2:ローカルおよびグローバルバーチャル ネットワーク ピアリングを構成する
+ タスク 3:サイト間接続をテストする

## <a name="estimated-timing-30-minutes"></a>推定時間:30 分

## <a name="architecture-diagram"></a>アーキテクチャの図

![image](../media/lab05.png)

### <a name="instructions"></a>Instructions

#### <a name="task-1-provision-the-lab-environment"></a>タスク 1:ラボ環境をプロビジョニングする

このタスクでは、3 つの仮想マシンをそれぞれ別のバーチャル ネットワークにデプロイし、そのうちの 2 つを同じ Azure リージョンに配置し、3 つ目の仮想マシンを別の Azure リージョンにデプロイします。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal の右上にあるアイコンをクリックして **Azure Cloud Shell** を開きます。

1. **Bash** または **PowerShell** の選択を求めるメッセージが表示されたら、 **[PowerShell]** を選択します。

    >**注**: **Cloud Shell** の初回起動時に "**ストレージがマウントされていません**" というメッセージが表示された場合は、このラボで使用しているサブスクリプションを選択し、**[ストレージの作成]** を選択します。

1. [Cloud Shell] ペインのツールバーで、 **[ファイルのアップロード/ダウンロード]** アイコンをクリックし、ドロップダウン メニューで **[アップロード]** をクリックして、ファイル **\\Allfiles\\Labs\\05\\az104-05-vnetvm-loop-template.json** と **\\Allfiles\\Labs\\05\\az104-05-vnetvm-loop-parameters.json** を Cloud Shell のホーム ディレクトリにアップロードします。

1. Edit the <bpt id="p1">**</bpt>Parameters<ept id="p1">**</ept> file you just uploaded and change the password. If you need help editing the file in the Shell please ask your instructor for assistance. As a best practice, secrets, like passwords, should be more securely stored in the Key Vault. 

1. From the Cloud Shell pane, run the following to create the resource group that will be hosting the lab environment. The first two virtual networks and a pair of virtual machines will be deployed in [Azure_region_1]. The third virtual network and the third virtual machine will be deployed in the same resource group but another [Azure_region_2]. (replace the [Azure_region_1] and [Azure_region_2] placeholder, including the square brackets, with the names of two different Azure regions where you intend to deploy these Azure virtual machines. An example is $location1 = 'eastus'. You can use Get-AzLocation to list all locations.):

   ```powershell
   $location1 = 'eastus'

   $location2 = 'westus'

   $rgName = 'az104-05-rg1'

   New-AzResourceGroup -Name $rgName -Location $location1
   ```

   ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: The regions used above were tested and known to work when this lab was last officially reviewed. If you would prefer to use different locations, or they no longer work, you will need to identify two different regions that Standard D2Sv3 virtual machines can be deployed into.
   >
   >Azure リージョンを識別するには、Cloud Shell の PowerShell セッションから **(Get-AzLocation).Location** を実行します。
   >
   >使う 2 つのリージョンを決めたら、それぞれのリージョンに対して Cloud Shell で次のコマンドを実行し、Standard D2Sv3 仮想マシンをデプロイできることを確認します。
   >
   >```az vm list-skus --location <Replace with your location> -o table --query "[? contains(name,'Standard_D2s')].name" ```
   >
   >Contoso では、ボストン、ニューヨーク、シアトルの各オフィスを、メッシュ ワイドエリア ネットワーク リンクを介して接続しており、各オフィスの間に完全な接続性を備えています。

1. [Cloud Shell] ウィンドウで、次のコマンドを実行して 3 つのバーチャル ネットワークを作成し、アップロードしたテンプレートとパラメーター ファイルを使用して仮想マシンをデプロイします。

   ```powershell
   New-AzResourceGroupDeployment `
      -ResourceGroupName $rgName `
      -TemplateFile $HOME/az104-05-vnetvm-loop-template.json `
      -TemplateParameterFile $HOME/az104-05-vnetvm-loop-parameters.json `
      -location1 $location1 `
      -location2 $location2
   ```

    >Contoso のオンプレミス ネットワークのトポロジを反映したラボ環境を実装して、その機能を検証します。

1. [Cloud Shell] ペインを閉じます。

#### <a name="task-2-configure-local-and-global-virtual-network-peering"></a>タスク 2:ローカルおよびグローバルバーチャル ネットワーク ピアリングを構成する

このタスクでは、前のタスクでデプロイしたバーチャル ネットワーク間で、ローカル ピアリングとグローバル ピアリングを構成します。

1. Azure portal で、 **[仮想ネットワーク]** を検索して選択します。

1. 前のタスクで作成したバーチャル ネットワークを確認し、最初の 2 つが同じ Azure リージョンに配置され、3 番目が別の Azure リージョンに存在することを確認します。

    >**注**:3 つのバーチャル ネットワークのデプロイに使用したテンプレートに、3 つのバーチャル ネットワークの IP アドレス範囲が重複しないようにします。

1. バーチャル ネットワークのリストで、**[az104-05-vnet0]** をクリックします。

1. **[az104-05-vnet0** バーチャル ネットワーク] ブレードの **[設定]** セクションで **[ピアリング]** をクリックしてから、**[+ 追加]** をクリックします。

1. 次の設定でピアリングを追加し (他のユーザーには既定値を残します)、**[追加]** をクリックします。

    | 設定 | 値|
    | --- | --- |
    | この仮想ネットワーク: ピアリング リンク名 | **az104-05-vnet0_to_az104-05-vnet1** |
    | このバーチャル ネットワーク:リモート バーチャル ネットワークのトラフィック | **許可 (既定)** |
    | このバーチャル ネットワーク:リモート バーチャル ネットワークから転送されたトラフィック | **この仮想ネットワークの外部から発信されるトラフィックをブロックする** |
    | 仮想ネットワーク ゲートウェイ | **なし** |
    | リモート仮想ネットワーク: ピアリング リンク名 | **az104-05-vnet1_to_az104-05-vnet0** |
    | 仮想ネットワークのデプロイ モデル | **リソース マネージャー** |
    | リソース ID を知っている | 未選択 |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | 仮想ネットワーク | **az104-05-vnet1** |
    | [Traffic to remote virtual network](リモート仮想ネットワークへのトラフィック) | **許可 (既定)** |
    | [Traffic forwarded from remote virtual network](リモート仮想ネットワークから転送されるトラフィック) | **この仮想ネットワークの外部から発信されるトラフィックをブロックする** |
    | 仮想ネットワーク ゲートウェイ | **なし** |

    >**注**:この手順では、az104-05-vnet0 から az104-05-vnet1、az104-05-vnet1 から az104-05-vnet0 までの 2 つのローカル ピアリングを確立します。

    >**注**:前のタスクで作成したバーチャル ネットワークが表示されない Azure Portal インターフェイスで問題が発生した場合は、Cloud Shell から次のPowerShell コマンドを実行して、ピアリングを構成できます。
    
   ```powershell
   $rgName = 'az104-05-rg1'

   $vnet0 = Get-AzVirtualNetwork -Name 'az104-05-vnet0' -ResourceGroupName $rgname

   $vnet1 = Get-AzVirtualNetwork -Name 'az104-05-vnet1' -ResourceGroupName $rgname

   Add-AzVirtualNetworkPeering -Name 'az104-05-vnet0_to_az104-05-vnet1' -VirtualNetwork $vnet0 -RemoteVirtualNetworkId $vnet1.Id

   Add-AzVirtualNetworkPeering -Name 'az104-05-vnet1_to_az104-05-vnet0' -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet0.Id
   ``` 

1. **[az104-05-vnet0** バーチャル ネットワーク] ブレードの **[設定]** セクションで **[ピアリング]** をクリックしてから、**[+ 追加]** をクリックします。

1. 次の設定でピアリングを追加し (他のユーザーには既定値を残します)、**[追加]** をクリックします。

    | 設定 | 値|
    | --- | --- |
    | この仮想ネットワーク: ピアリング リンク名 | **az104-05-vnet0_to_az104-05-vnet2** |
    | このバーチャル ネットワーク:リモート バーチャル ネットワークのトラフィック | **許可 (既定)** |
    | このバーチャル ネットワーク:リモート バーチャル ネットワークから転送されたトラフィック | **この仮想ネットワークの外部から発信されるトラフィックをブロックする** |
    | 仮想ネットワーク ゲートウェイ | **なし** |
    | リモート仮想ネットワーク: ピアリング リンク名 | **az104-05-vnet2_to_az104-05-vnet0** |
    | 仮想ネットワークのデプロイ モデル | **リソース マネージャー** |
    | リソース ID を知っている | 未選択 |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | 仮想ネットワーク | **az104-05-vnet2** |
    | [Traffic to remote virtual network](リモート仮想ネットワークへのトラフィック) | **許可 (既定)** |
    | [Traffic forwarded from remote virtual network](リモート仮想ネットワークから転送されるトラフィック) | **この仮想ネットワークの外部から発信されるトラフィックをブロックする** |
    | 仮想ネットワーク ゲートウェイ | **なし** |

    >**注**:このステップでは、az104-05-vnet0 から az104-05-vnet2、az104-05-vnet2 から az104-05-vnet0 までの 2 つのグローバル ピアリングを確立します。

    >**注**:前のタスクで作成したバーチャル ネットワークが表示されない Azure Portal インターフェイスで問題が発生した場合は、Cloud Shell から次のPowerShell コマンドを実行して、ピアリングを構成できます。
    
   ```powershell
   $rgName = 'az104-05-rg1'

   $vnet0 = Get-AzVirtualNetwork -Name 'az104-05-vnet0' -ResourceGroupName $rgname

   $vnet2 = Get-AzVirtualNetwork -Name 'az104-05-vnet2' -ResourceGroupName $rgname

   Add-AzVirtualNetworkPeering -Name 'az104-05-vnet0_to_az104-05-vnet2' -VirtualNetwork $vnet0 -RemoteVirtualNetworkId $vnet2.Id

   Add-AzVirtualNetworkPeering -Name 'az104-05-vnet2_to_az104-05-vnet0' -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet0.Id
   ``` 

1. **[バーチャル ネットワーク]** ブレードに戻り、バーチャル ネットワークの一覧で **[az104-05-vnet1]** をクリックします。

1. **az104-05-vnet1** 仮想ネットワーク ブレードの **[設定]** セクションで、 **[ピアリング]** をクリックしてから、 **[+ 追加]** をクリックします。

1. 次の設定でピアリングを追加し (他のユーザーには既定値を残します)、**[追加]** をクリックします。

    | 設定 | 値|
    | --- | --- |
    | この仮想ネットワーク: ピアリング リンク名 | **az104-05-vnet1_to_az104-05-vnet2** |
    | このバーチャル ネットワーク:リモート バーチャル ネットワークのトラフィック | **許可 (既定)** |
    | このバーチャル ネットワーク:リモート バーチャル ネットワークから転送されたトラフィック | **この仮想ネットワークの外部から発信されるトラフィックをブロックする** |
    | 仮想ネットワーク ゲートウェイ | **なし** |
    | リモート仮想ネットワーク: ピアリング リンク名 | **az104-05-vnet2_to_az104-05-vnet1** |
    | 仮想ネットワークのデプロイ モデル | **リソース マネージャー** |
    | リソース ID を知っている | 未選択 |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | 仮想ネットワーク | **az104-05-vnet2** |
    | [Traffic to remote virtual network](リモート仮想ネットワークへのトラフィック) | **許可 (既定)** |
    | [Traffic forwarded from remote virtual network](リモート仮想ネットワークから転送されるトラフィック) | **この仮想ネットワークの外部から発信されるトラフィックをブロックする** |
    | 仮想ネットワーク ゲートウェイ | **なし** |

    >**注**:このステップでは、az104-05-vnet1 から az104-05-vnet2、az104-05-vnet2 から az104-05-vnet1 までの 2 つのグローバル ピアリングを確立します。

    >**注**:前のタスクで作成したバーチャル ネットワークが表示されない Azure Portal インターフェイスで問題が発生した場合は、Cloud Shell から次のPowerShell コマンドを実行して、ピアリングを構成できます。
    
   ```powershell
   $rgName = 'az104-05-rg1'

   $vnet1 = Get-AzVirtualNetwork -Name 'az104-05-vnet1' -ResourceGroupName $rgname

   $vnet2 = Get-AzVirtualNetwork -Name 'az104-05-vnet2' -ResourceGroupName $rgname

   Add-AzVirtualNetworkPeering -Name 'az104-05-vnet1_to_az104-05-vnet2' -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id

   Add-AzVirtualNetworkPeering -Name 'az104-05-vnet2_to_az104-05-vnet1' -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
   ``` 

#### <a name="task-3-test-intersite-connectivity"></a>タスク 3:サイト間接続をテストする

このタスクでは、前のタスクでローカル ピアリングとグローバル ピアリングを介して接続した 3 つのバーチャル ネットワーク上の仮想マシン間の接続性をテストします。

1. Azure portal で、**[仮想マシン]** を検索して選択します。

1. 仮想マシンのリストで、**[az104-05-vm0]** をクリックします。

1. **az104-05-vm0** ブレードで、 **[接続]** をクリックし、ドロップダウン メニューで **[RDP]** をクリックし、 **[RDP を使用して接続する]** ブレードで **[RDP ファイルのダウンロード]** をクリックし、プロンプトに従ってリモート デスクトップ セッションを開始します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This step refers to connecting via Remote Desktop from a Windows computer. On a Mac, you can use Remote Desktop Client from the Mac App Store and on Linux computers you can use an open source RDP client software.

    >**注**:ターゲット仮想マシンに接続する際は、警告メッセージを無視できます。

1. メッセージが表示されたら、 **[学生]** のユーザー名とパラメーター ファイルのパスワードを使用してサインインします。 

1. **az104-05-vm0** へのリモート デスクトップ セッション内で、**[スタート]** ボタンを右クリックし、右クリック メニューで **[Windows PowerShell (管理者)]** をクリックします。

1. Windows PowerShell コンソール ウィンドウで、次のコマンドを実行して、TCP ポート 3389 での **az104-05-vm1** (プライベート IP アドレスが **10.51.0.4**) への接続性をテストします。

   ```powershell
   Test-NetConnection -ComputerName 10.51.0.4 -Port 3389 -InformationLevel 'Detailed'
   ```

    >**注**:このテストで TCP 3389 を使用するのは、このポートがオペレーティング システムのファイアウォールによって既定で許可されているためです。

1. コマンドの出力を調べて、接続が正常に行われたことを確認します。

1. Windows PowerShell コンソール ウィンドウで次のコマンドを実行して、**az104-05-vm2** (プライベート IP アドレスが **10.52.0.4**) への接続性をテストします。

   ```powershell
   Test-NetConnection -ComputerName 10.52.0.4 -Port 3389 -InformationLevel 'Detailed'
   ```

1. ラボ コンピューターの Azure portal に戻り、**[仮想マシン]** ブレードに戻ります。

1. 仮想マシンの一覧で、**az104-05-vm1** をクリックします。

1. **az104-05-vm1** ブレードで、 **[接続]** をクリックし、ドロップダウン メニューで **[RDP]** をクリックし、 **[RDP を使用して接続する]** ブレードで **[RDP ファイルのダウンロード]** をクリックし、プロンプトに従ってリモート デスクトップ セッションを開始します。

    >                **メモ:** このラボをご自分のペースでクリックして進めることができる、 **[ラボの対話型シミュレーション](https://mslabs.cloudguides.com/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%209)** が用意されています。

    >**注**:ターゲット仮想マシンに接続する際は、警告メッセージを無視できます。

1. メッセージが表示されたら、 **[学生]** のユーザー名とパラメーター ファイルのパスワードを使用してサインインします。 

1. **az104-05-vm1** へのリモート デスクトップ セッション内で、 **[スタート]** ボタンを右クリックし、右クリック メニューで **[Windows PowerShell (管理者)]** をクリックします。

1. Windows PowerShell コンソール ウィンドウで、次のコマンドを実行して、TCP ポート 3389 での **az104-05-vm2** (プライベート IP アドレスが **10.52.0.4**) への接続性をテストします。

   ```powershell
   Test-NetConnection -ComputerName 10.52.0.4 -Port 3389 -InformationLevel 'Detailed'
   ```

    >**注**:このテストで TCP 3389 を使用するのは、このポートがオペレーティング システムのファイアウォールによって既定で許可されているためです。

1. コマンドの出力を調べて、接続が正常に行われたことを確認します。

#### <a name="clean-up-resources"></a>リソースをクリーンアップする

>対話型シミュレーションとホストされたラボの間に若干の違いがある場合がありますが、示されている主要な概念とアイデアは同じです。

><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>:  Don't worry if the lab resources cannot be immediately removed. Sometimes resources have dependencies and take a longer time to delete. It is a common Administrator task to monitor resource usage, so just periodically review your resources in the Portal to see how the cleanup is going. 

1. Azure portal で、**[Cloud Shell]** ペイン内に **PowerShell** セッションを開きます。

1. 次のコマンドを実行して、このモジュールのラボ全体で作成したすべてのリソース グループのリストを表示します。

   ```powershell
   Get-AzResourceGroup -Name 'az104-05*'
   ```

1. 次のコマンドを実行して、このモジュールのラボ全体を通して作成したすべてのリソース グループを削除します。

   ```powershell
   Get-AzResourceGroup -Name 'az104-05*' | Remove-AzResourceGroup -Force -AsJob
   ```

    >**注**:このコマンドは非同期で実行されるため (-AsJob パラメーターによって決定されます)、同じ PowerShell セッション内で直後に別の PowerShell コマンドを実行できますが、リソース グループが実際に削除されるまでに数分かかります。

#### <a name="review"></a>確認

このラボでは、次のことを行いました。

+ ラボ環境をプロビジョニングしました
+ ローカルバーチャル ネットワーク ピアリングおよびグローバルバーチャル ネットワーク ピアリングを構成しました。
+ サイト間の接続性をテストしました
