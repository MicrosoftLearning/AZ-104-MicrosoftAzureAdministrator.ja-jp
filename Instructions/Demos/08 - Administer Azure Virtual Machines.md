---
demo:
  title: 'デモ 08: Azure Virtual Machines を管理する'
  module: Administer Azure Virtual Machines
---


# 08 - Azure Virtual Machines を管理する

## デモ -- ポータルで仮想マシンを作成する

このデモでは、ポータルで Azure 仮想マシンを作成し、それにアクセスします。

**参照**

[クイック スタート - Azure Portal で Windows VM を作成する](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

[クイック スタート - Azure portal で Linux VM を作成する](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)

[Bastion を使用して仮想マシンに接続する](https://learn.microsoft.com/azure/bastion/tutorial-create-host-portal#connect)

**仮想マシンを作成する**

**注:** これらの手順では、数個の仮想マシン パラメーターのみを説明します。 他の領域についても説明してください。 対象ユーザーに応じて、Windows または Linux の仮想マシンを作成できます。

1. Azure portal を使用します。

1. **仮想マシン**を検索します。 

1. 基本的な仮想マシンを作成します。 可用性オプション、イメージ、インバウンド規則を確認します。

1. セキュリティで保護された管理者アカウントを作成することの重要性について説明します。

1. 仮想マシンを作成し、リソースがデプロイされるまで待ちます。  

**仮想マシンへの接続**

1. 仮想マシンに**接続**する方法がいくつかあります。 

1. Windows サーバーの場合、クイックスタートに示すように **RDP** を使用できます。 

1. Linux サーバーの場合は、クイックスタートに示すように **SSH** を使用できます。 

1. どちらのサーバーでも、**Bastion** サービスに接続できます (クイックスタート)。 Bastion が RDP や SSH より優先される理由を確認します。 

## 仮想マシンの可用性を構成する

このデモでは、仮想マシンのスケーリング オプションについて説明します。

**参照**

[Azure portal を使用してスケール セットに仮想マシンを作成する](https://learn.microsoft.com/azure/virtual-machine-scale-sets/flexible-virtual-machine-scale-sets-portal)

1. Azure portal を使用します。

1. **Virtual Machine Scale Sets** を検索して選択します。 

1. **Virtual Machine Scale Sets** を作成します。 仮想マシン スケール セットの目的を確認します。 **均一**と**フレキシブル** オーケストレーション モードの API の違いについて説明します。 選択内容がスケーリング オプションに影響を与える可能性があることを説明します。 

1.  **[スケーリング]** タブに移動します。 

1.  **[手動スケール]**  と **[スケールイン ポリシー]** の使用方法を確認します。 

1. **カスタム** スケーリング ポリシーに変更します。 

1. 仮想マシン インスタンスのスケールアウトとスケールインに **[CPU しきい値 (%)]** を使用する方法を確認します。 

