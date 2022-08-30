---
lab:
  title: 04 - 仮想ネットワークを実装する
  module: Module 04 - Virtual Networking
---

# <a name="lab-04---implement-virtual-networking"></a>ラボ 04 - 仮想ネットワークを実装する

# <a name="student-lab-manual"></a>受講生用ラボ マニュアル

## <a name="lab-scenario"></a>ラボのシナリオ

You need to explore Azure virtual networking capabilities. To start, you plan to create a virtual network in Azure that will host a couple of Azure virtual machines. Since you intend to implement network-based segmentation, you will deploy them into different subnets of the virtual network. You also want to make sure that their private and public IP addresses will not change over time. To comply with Contoso security requirements, you need to protect public endpoints of Azure virtual machines accessible from Internet. Finally, you need to implement DNS name resolution for Azure virtual machines both within the virtual network and from Internet.

## <a name="objectives"></a>目標

このラボでは、次のことを行います。

+ タスク 1:バーチャル ネットワークを作成および構成する
+ タスク 2:仮想マシンをバーチャル ネットワークにデプロイする
+ タスク 3:Azure VM のプライベート IP アドレスとパブリック IP アドレスを構成する
+ タスク 4:ネットワーク セキュリティ グループを構成する
+ タスク 5:内部の名前解決に Azure DNS を構成する
+ タスク 6:外部の名前解決に Azure DNS を構成する

## <a name="estimated-timing-40-minutes"></a>推定時間:40 分

## <a name="architecture-diagram"></a>アーキテクチャの図

![image](../media/lab04.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1"></a>演習 1

#### <a name="task-1-create-and-configure-a-virtual-network"></a>タスク 1:バーチャル ネットワークを作成および構成する

このタスクでは、Azure portal を使用して、複数のサブネットを持つバーチャル ネットワークを作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal で「**仮想ネットワーク**」を検索して選択し、**[仮想ネットワーク]** ブレードで **[+ 作成]** をクリックします。

1. バーチャル ネットワークを次の設定で作成します (その他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | サブスクリプション | このラボで使用する Azure サブスクリプションの名前 |
    | リソース グループ | **新しい**リソース グループ **az104-04-rg1** の名前 |
    | 名前 | **az104-04-vnet1** |
    | リージョン | このラボで使用するサブスクリプションで使用できる Azure リージョンの名前 |

1. ページの下部の **[次へ: IP アドレス]** をクリックして、次の値を入力します

    | 設定 | 値 |
    | --- | --- |
    | IPv4 アドレス空間 | **10.40.0.0/20** |

1. **[+ サブネットの追加]** をクリックして、次の値を入力してから、**[追加]** をクリックします

    | 設定 | 値 |
    | --- | --- |
    | サブネット名 | **subnet0** |
    | サブネットのアドレス範囲 | **10.40.0.0/24** |

1. Accept the defaults and click <bpt id="p1">**</bpt>Review and Create<ept id="p1">**</ept>. Let validation occur, and hit <bpt id="p1">**</bpt>Create<ept id="p1">**</ept> again to submit your deployment.

    ><bpt id="p1">**</bpt>Note:<ept id="p1">**</ept> Wait for the virtual network to be provisioned. This should take less than a minute.

1. **[リソースに移動]** をクリックします

1. **[az104-04-vnet1** 仮想ネットワーク] ブレードで、**[サブネット]** をクリックし、**[+ サブネット]** をクリックします。

1. サブネットを次の設定で作成します (その他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | 名前 | **subnet1** |
    | アドレス範囲 (CIDR ブロック) | **10.40.1.0/24** |
    | ネットワーク セキュリティ グループ | **なし** |
    | ルート テーブル | **なし** |

1. **[保存]**

#### <a name="task-2-deploy-virtual-machines-into-the-virtual-network"></a>タスク 2:仮想マシンをバーチャル ネットワークにデプロイする

このタスクでは、ARM テンプレートを使用して、Azure 仮想マシンをバーチャル ネットワークの異なるサブネットにデプロイします。

1. Azure portal の右上にあるアイコンをクリックして **Azure Cloud Shell** を開きます。

1. **Bash** または **PowerShell** の選択を求めるメッセージが表示されたら、 **[PowerShell]** を選択します。

    >**注**: **Cloud Shell** の初回起動時に **"ストレージがマウントされていません"** というメッセージが表示された場合は、このラボで使用しているサブスクリプションを選択し、**[ストレージの作成]** を選択します。

1. [Cloud Shell] ペインのツールバーで、 **[ファイルのアップロード/ダウンロード]** アイコンをクリックし、ドロップダウン メニューで **[アップロード]** をクリックして、ファイル **\\Allfiles\\Labs\\04\\az104-04-vms-loop-template.json** と **\\Allfiles\\Labs\\04\\az104-04-vms-loop-parameters.json** を Cloud Shell のホーム ディレクトリにアップロードします。

    >**注**:各ファイルを別々にアップロードする必要がある場合があります。

1. Edit the Parameters file, and change the password. If you need help editing the file in the Shell please ask your instructor for assistance. As a best practice, secrets, like passwords, should be more securely stored in the Key Vault. 

1. [Cloud Shell] ペインで、次のコマンドを実行し、テンプレート ファイルとパラメーター ファイルを使用して、2 つの仮想マシンをデプロイします。

   ```powershell
   $rgName = 'az104-04-rg1'

   New-AzResourceGroupDeployment `
      -ResourceGroupName $rgName `
      -TemplateFile $HOME/az104-04-vms-loop-template.json `
      -TemplateParameterFile $HOME/az104-04-vms-loop-parameters.json
   ```

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This method of deploying ARM templates uses Azure PowerShell. You can perform the same task by running the equivalent Azure CLI command <bpt id="p1">**</bpt>az deployment create<ept id="p1">**</ept> (for more information, refer to <bpt id="p2">[</bpt>Deploy resources with Resource Manager templates and Azure CLI<ept id="p2">](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-cli)</ept>.

    >Azure バーチャル ネットワークの機能について学習します。

    >**注**:VM サイズが利用できないというエラーが発生した場合、インストラクターにサポートを依頼し、次の手順を試してください。
    > 1. CloudShell で `{}` ボタンをクリックし、左側のバーから **az104-04-vms-loop-parameters.json** を選択して、`vmSize` パラメーターの値をメモしておきます。
    > 1. まず、Azure でいくつかの Azure 仮想マシンをホストするバーチャル ネットワークを作成するプランを立てます。
    > 1. ネットワーク ベースのセグメンテーションを実装するため、バーチャル ネットワークの異なるサブネットにデプロイします。
    > 1. `vmSize` パラメーターの値を、先ほど実行したコマンドによって返された値のいずれかに置き換えます。
    > 1. また、プライベート IP アドレスとパブリック IP アドレスが時間の経過とともに変更されないようにする必要もあります。

1. [Cloud Shell] ペインを閉じます。

#### <a name="task-3-configure-private-and-public-ip-addresses-of-azure-vms"></a>タスク 3:Azure VM のプライベート IP アドレスとパブリック IP アドレスを構成する

このタスクでは、Azure 仮想マシンのネットワーク インターフェイスに割り当てられたパブリック IP アドレスとプライベート IP アドレスの静的割り当てを構成します。

   >**注**:プライベート IP アドレスとパブリック IP アドレスは、実際にネットワーク インターフェイスに割り当てられ、次に Azure 仮想マシンに接続されますが、Azure VM に割り当てられた IP アドレスを代わりに参照することもよくあります。

1. Azure portal で「**リソース グループ**」を検索して選択し、**[リソース グループ]** ブレードで **[az104-04-rg1]** をクリックします。

1. **az104-04-rg1** リソース グループのブレードで、そのリソースのリスト内にある **[az104-04-vnet1]** をクリックします。

1. **[az104-04-vnet1** 仮想ネットワーク] ブレードで、**[接続デバイス]** セクションを確認し、仮想ネットワークに接続されている 2 つのネットワーク インターフェイス **az104-04-nic0** と **az104-04-nic1** があることを確認します。

1. **[az104-04-nic0]** をクリックし、**[az104-04-nic0]** ブレードで **[IP 構成]** をクリックします。

    >**注**:**ipconfig1** が動的プライベート IP アドレスで現在設定されていることを確認します。

1. IP 構成のリストで、**[ipconfig1]** をクリックします。

1. **[ipconfig1]** ブレードの **[パブリック IP アドレス設定]** セクションで、**[関連付け]** を選択し、**[+ 新規作成]** をクリックし、次の設定を指定して **[OK]** をクリックします。

    | 設定 | 値 |
    | --- | --- |
    | 名前 | **az104-04-pip0** |
    | SKU | **Standard** |

1. **[ipconfig1]** ブレードで **[割り当て]** を **[静的]** に設定し、**[IP アドレス]** の既定値を **10.40.0.4** のままにします。

1. Contoso のセキュリティ要件に準拠するには、インターネットからアクセスできる Azure 仮想マシンのパブリック エンドポイントを保護する必要があります。

1. **[az104-04-vnet1]** ブレードに戻ります

1. **[az104-04-nic1]** をクリックし、 **[az104-04-nic1]** ブレードで **[IP 構成]** をクリックします。

    >**注**:**ipconfig1** が動的プライベート IP アドレスで現在設定されていることを確認します。

1. IP 構成のリストで、**[ipconfig1]** をクリックします。

1. **[ipconfig1]** ブレードの **[パブリック IP アドレス設定]** セクションで、**[関連付け]** を選択し、**[+ 新規作成]** をクリックし、次の設定を指定して **[OK]** をクリックします。

    | 設定 | 値 |
    | --- | --- |
    | 名前 | **az104-04-pip1** |
    | SKU | **Standard** |

1. **[ipconfig1]** ブレードで **[割り当て]** を **[静的]** に設定し、 **[IP アドレス]** の既定値を **[10.40.1.4]** のままにします。

1. **[ipconfig1]** ブレードに戻り、変更を保存します。

1. **az104-04-rg1** リソース グループのブレードに戻り、そのリソースのリストで **[az104-04-vm0]** をクリックし、**az104-04-vm0** 仮想マシンのブレードでパブリック IP アドレスのエントリをメモします。

1. **[az104-04-rg1]** の [リソース グループ] ブレードに戻り、そのリソースのリストで **[az104-04-vm1]** をクリックし、 **[az104-04-vm1]** の [仮想マシン] ブレードでパブリック IP アドレスのエントリをメモします。

    >**注**:このラボの最後のタスクで、両方の IP アドレスが必要になります。

#### <a name="task-4-configure-network-security-groups"></a>タスク 4:ネットワーク セキュリティ グループを構成する

このタスクでは、Azure 仮想マシンへの接続を制限できるように、ネットワーク セキュリティ グループを構成します。

1. Azure portal で **az104-04-rg1** リソース グループのブレードに戻り、そのリソースのリストで **[az104-04-vm0]** をクリックします。

1. **az104-04-vm0** の概要のブレードで **[接続]** をクリックし、ドロップダウン メニューで **[RDP]** をクリックし、**[RDP で接続する]** ブレードで **[パブリック IP アドレスを使用して RDP ファイルのダウンロード]** をクリックして、プロンプトに従ってリモート デスクトップ セッションを開始します。

1. 接続の試行が失敗することに注意してください。

    >最後に、バーチャル ネットワーク内とインターネットからの両方で、Azure 仮想マシンの DNS 名前解決を実装する必要があります。

1. 仮想マシン **az104-04-vm0** と **az104-04-vm1** を停止します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This is done for lab expediency. If the virtual machines are running when a network security group is attached to their network interface, it can can take over 30 minutes for the attachment to take effect. Once the network security group has been created and attached, the virtual machines will be restarted, and the attachment will be in effect immediately.

1. Azure portal で「**ネットワーク セキュリティ グループ**」を検索して選択し、**[ネットワーク セキュリティ グループ]** ブレードで **[+ 作成]** をクリックします。

1. ネットワーク セキュリティ グループを次の設定で作成します (その他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-04-rg1** |
    | 名前 | **az104-04-nsg01** |
    | リージョン | このラボで他のすべてのリソースをデプロイする Azure リージョンの名前 |

1. Click <bpt id="p1">**</bpt>Review and Create<ept id="p1">**</ept>. Let validation occur, and hit <bpt id="p1">**</bpt>Create<ept id="p1">**</ept> to submit your deployment.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the deployment to complete. This should take about 2 minutes.

1. [デプロイ] ブレードで **[リソースに移動]** をクリックして、**az104-04-nsg01** ネットワーク セキュリティ グループのブレードを開きます。

1. **az104-04-nsg01** ネットワーク セキュリティ グループのブレードの **[設定]** セクションで、**[受信セキュリティ ルール]** をクリックします。

1. 次の設定を使用して受信ルールを追加します (その他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | source | **[任意]** |
    | Source port ranges | * |
    | 到着地 | **[任意]** |
    | サービス | **RDP** |
    | アクション | **許可** |
    | Priority | **300** |
    | 名前 | **AllowRDPInBound** |

1. **az104-04-nsg01** ネットワーク セキュリティ グループのブレードの **[設定]** セクションで、**[ネットワーク インターフェイス]** をクリックし、**[+ 関連付け]** をクリックします。

1. **az104-04-nsg01** ネットワーク セキュリティ グループを **az104-04-nic0** ネットワーク インターフェイスと **az104-04-nic1** ネットワーク インターフェイスに関連付けます。

    >**注**:新しく作成されたネットワーク セキュリティ グループのルールがネットワーク インターフェイス カードに適用されるまで、最大 5 分かかる場合があります。

1. 仮想マシン **az104-04-vm0** と **az104-04-vm1** を開始します。

1. **az104-04-vm0** 仮想マシンのブレードに戻ります。

    >**注**:後続のステップで、ターゲット仮想マシンに正常に接続できることを確認します。

1. **[az104-04-vm0]** ブレードで **[接続]**、**[RDP]** の順にクリックし、**[RDP で接続する]** ブレードで、パブリック IP アドレスを使用して **[RDP ファイルのダウンロード]** をクリックし、プロンプトに従ってリモート デスクトップ セッションを開始します。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: This step refers to connecting via Remote Desktop from a Windows computer. On a Mac, you can use Remote Desktop Client from the Mac App Store and on Linux computers you can use an open source RDP client software.

    >**注**:ターゲット仮想マシンに接続する際は、警告メッセージを無視できます。

1. プロンプトが表示されたら、パラメーター ファイルのユーザーとパスワードを使用してサインインします。

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Leave the Remote Desktop session open. You will need it in the next task.

#### <a name="task-5-configure-azure-dns-for-internal-name-resolution"></a>タスク 5:内部の名前解決に Azure DNS を構成する

このタスクでは、Azure プライベート DNS ゾーンを使用して、バーチャル ネットワーク内で DNS 名前解決を構成します。

1. Azure portal で、「**プライベート DNS ゾーン**」を検索して選択し、**[プライベート DNS ゾーン]** ブレードで **[+ 作成]** をクリックします。

1. プライベート DNS ゾーンを次の設定で作成します (その他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-04-rg1** |
    | 名前 | **contoso.org** |

1. Click <bpt id="p1">**</bpt>Review and Create<ept id="p1">**</ept>. Let validation occur, and hit <bpt id="p1">**</bpt>Create<ept id="p1">**</ept> again to submit your deployment.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the private DNS zone to be created. This should take about 2 minutes.

1. **[リソースに移動]** をクリックして **contoso.org** DNS プライベート ゾーンのブレードを開きます。

1. **contoso.org** プライベート DNS ゾーンのブレードの **[設定]** セクションで、**[仮想ネットワークのリンク]** をクリックします

1. **[+ 追加]** をクリックして、仮想ネットワークのリンクを次の設定で作成します (他の設定は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | リンク名 | **az104-04-vnet1-link** |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | 仮想ネットワーク | **az104-04-vnet1** |
    | 自動登録を有効にする | enabled |

1. **[OK]** をクリックします。

    ><bpt id="p1">**</bpt>Note:<ept id="p1">**</ept> Wait for the virtual network link to be created. This should take less than 1 minute.

1. **contoso.org** プライベート DNS ゾーンのブレードのサイドバーで、**[概要]** をクリックします

1. **az104-04-vm0** および **az104-04-vm1** の DNS レコードが、レコード セットのリストに**自動登録**として表示されることを確認 します。

    >**注:** レコード セットが一覧に表示されない場合は、必要に応じて数分待ってからページを更新してください。

1. リモート デスクトップ セッションを **[az104-04-vm0]** に切り替えて、**[スタート]** ボタンを右クリックし、右クリック メニューで **[Windows PowerShell (管理者)]** をクリックします。

1. Windows PowerShell コンソール ウィンドウで次のコマンドを実行して、新しく作成したプライベート DNS ゾーン内の内部名前解決をテストします。

   ```powershell
   nslookup az104-04-vm0.contoso.org
   nslookup az104-04-vm1.contoso.org
   ```

1. コマンドの出力に、**az104-04-vm1** (**10.40.1.4**) のプライベート IP アドレスが含まれていることを確認します。

#### <a name="task-6-configure-azure-dns-for-external-name-resolution"></a>タスク 6:外部の名前解決に Azure DNS を構成する

このタスクでは、Azure パブリック DNS ゾーンを使って外部 DNS 名前解決を構成します。

1. Web ブラウザーで新しいタブを開き、<https://www.godaddy.com/domains/domain-name-search> に移動します。

1. ドメイン名検索を使用して、使用されていないドメイン名を識別します。

1. Azure portal で「**DNS ゾーン**」と検索してそれを選択し、**[DNS ゾーン]** ブレードで **[+ 追加]** をクリックします。

1. 次の設定で DNS ゾーンを作成します (その他は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | サブスクリプション | このラボで使用している Azure サブスクリプションの名前 |
    | リソース グループ | **az104-04-rg1** |
    | 名前 | このタスクの先ほど確認した DNS ドメイン名 |

1. Click <bpt id="p1">**</bpt>Review and Create<ept id="p1">**</ept>. Let validation occur, and hit <bpt id="p1">**</bpt>Create<ept id="p1">**</ept> again to submit your deployment.

    ><bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Wait for the DNS zone to be created. This should take about 2 minutes.

1. **[リソースに移動]** をクリックして、新しく作成した DNS ゾーンのブレードを開きます。

1. [DNS ゾーン] ブレードで、**[+ レコード セット]** をクリックします。

1. 次の設定でレコード セットを追加します (その他は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | 名前 | **az104-04-vm0** |
    | Type | **A** |
    | エイリアスのレコード セット | **No** |
    | TTL | **1** |
    | TTL の単位 | **Hours** |
    | IP アドレス | このラボの 3 番目の演習で特定した **az104-04-vm0** のパブリック IP アドレス |

1. **[OK]**

1. [DNS ゾーン] ブレードで、**[+ レコード セット]** をクリックします。

1. 次の設定でレコード セットを追加します (その他は既定値のままにします)。

    | 設定 | 値 |
    | --- | --- |
    | 名前 | **az104-04-vm1** |
    | Type | **A** |
    | エイリアスのレコード セット | **No** |
    | TTL | **1** |
    | TTL の単位 | **Hours** |
    | IP アドレス | このラボの 3 番目の演習で特定した **az104-04-vm1** のパブリック IP アドレス |

1. **[OK]**

1. [DNS ゾーン] ブレードで、**[Name server 1]** エントリの名前をメモします。

1. Azure portal で、右上にあるアイコンをクリックし、**[Cloud Shell]** で **[PowerShell]** セッションを開きます。

1. [Cloud Shell] ペインで次のコマンドを実行して、新しく作成された DNS ゾーンに設定されている **az104-04-vm0** DNS レコード セットの外部名前解決をテストします (プレースホルダー `[Name server 1]` をこのタスクで前にメモした **[ネーム サーバー 1]** の名前に、プレースホルダー `[domain name]` をこのタスクで前に作成した DNS ドメインの名前に置き換えます)。

   ```powershell
   nslookup az104-04-vm0.[domain name] [Name server 1]
   ```

1. コマンドの出力に、**az104-04-vm0** のパブリック IP アドレスが含まれていることを確認します。

1. [Cloud Shell] ペインで次のコマンドを実行して、新しく作成された DNS ゾーンに設定されている **az104-04-vm1** DNS レコード セットの外部名前解決をテストします (プレースホルダー `[Name server 1]` をこのタスクで前にメモした **[ネーム サーバー 1]** の名前に、プレースホルダー `[domain name]` をこのタスクで前に作成した DNS ドメインの名前に置き換えます)。

   ```powershell
   nslookup az104-04-vm1.[domain name] [Name server 1]
   ```

1. コマンドの出力に、**az104-04-vm1** のパブリック IP アドレスが含まれていることを確認します。

#### <a name="clean-up-resources"></a>リソースをクリーンアップする

 > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>: Remember to remove any newly created Azure resources that you no longer use. Removing unused resources ensures you will not see unexpected charges.

 > <bpt id="p1">**</bpt>Note<ept id="p1">**</ept>:  Don't worry if the lab resources cannot be immediately removed. Sometimes resources have dependencies and take a longer time to delete. It is a common Administrator task to monitor resource usage, so just periodically review your resources in the Portal to see how the cleanup is going. 

1. Azure portal で、**[Cloud Shell]** ペイン内に **PowerShell** セッションを開きます。

1. 次のコマンドを実行して、このモジュールのラボ全体で作成したすべてのリソース グループのリストを表示します。

   ```powershell
   Get-AzResourceGroup -Name 'az104-04*'
   ```

1. 次のコマンドを実行して、このモジュールのラボ全体を通して作成したすべてのリソース グループを削除します。

   ```powershell
   Get-AzResourceGroup -Name 'az104-04*' | Remove-AzResourceGroup -Force -AsJob
   ```

    >**注**:このコマンドは非同期で実行されるため (-AsJob パラメーターによって決定されます)、同じ PowerShell セッション内で直後に別の PowerShell コマンドを実行できますが、リソース グループが実際に削除されるまでに数分かかります。

#### <a name="review"></a>確認

このラボでは、次のことを行いました。

+ バーチャル ネットワークを作成して構成しました
+ 仮想マシンをバーチャル ネットワークにデプロイしました
+ Azure VM のプライベート IP アドレスとパブリック IP アドレスを構成しました
+ ネットワーク セキュリティ グループを構成しました
+ Azure DNS を内部の名前解決用に構成しました
+ Azure DNS を外部の名前解決用に構成しました
