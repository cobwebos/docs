---
title: 查看 Azure 虚拟网络拓扑 | Microsoft Docs
description: 了解如何查看虚拟网络中的资源及其相互关系。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: jdial
ms.openlocfilehash: 6ef165ddc481bf84c6189635e36b97eb9518261e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077802"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>查看 Azure 虚拟网络的拓扑

本文介绍如何查看 Microsoft Azure 虚拟网络中的资源及其相互关系。 例如，虚拟网络包含子网。 子网包含资源，例如 Azure 虚拟机 (VM)。 VM 有一个或多个网络接口。 每个子网可以有一个网络安全组，以及一个与之关联的路由表。 可以通过 Azure 网络观察程序的拓扑功能查看虚拟网络中的所有资源、与虚拟网络中的资源相关联的资源，以及这些资源的相互关系。

可以使用 [Azure 门户](#azure-portal)、[Azure CLI](#azure-cli) 或 [PowerShell](#powershell) 查看拓扑。

## <a name = "azure-portal"></a>查看拓扑 - Azure 门户

1. 使用具有必要[权限](required-rbac-permissions.md)的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 在门户左上角选择“所有服务”。
3. 在“所有服务”筛选器框中，输入“网络观察程序”。 结果中出现“网络观察程序”后，将其选中。
4. 选择“拓扑”。 生成拓扑要求在特定区域有网络观察程序，而该特定区域正是需要为其生成拓扑的虚拟网络所在的区域。 如果未在要为其生成拓扑的虚拟网络所在的区域启用网络观察程序，系统会在所有区域为你创建网络观察程序。 网络观察程序在名为 **NetworkWatcherRG** 的资源组中创建。
5. 依次选择订阅、要查看其拓扑的虚拟网络的资源组、虚拟网络。 下图中显示了名为 *MyResourceGroup* 的资源组中名为 *MyVnet* 的虚拟网络的拓扑：

    ![查看拓扑](./media/view-network-topology/view-topology.png)

    如上图所示，此虚拟网络包含三个子网。 一个子网中部署了一个 VM。 该 VM 有一个附加的网络接口和一个关联的公共 IP 地址。 另外两个子网有一个关联的路由表。 每个路由表包含两个路由。 一个子网有一个关联的网络安全组。 仅为符合以下条件的资源显示拓扑信息：- 与 *myVnet* 虚拟网络位于同一资源组和区域中。 例如，不会显示 *MyResourceGroup* 之外的资源组中存在的网络安全组，即使该网络安全组与 *MyVnet* 虚拟网络中的子网相关联。
        - 位于 *myVnet* 虚拟网络中，或与其中的资源相关联。 例如，不会显示与 *myVnet* 虚拟网络中的子网或网络接口不相关联的网络安全组，即使该网络安全组位于 *MyResourceGroup* 资源组中。

    图中显示的拓扑对应的虚拟网络是在部署 **“通过网络虚拟设备路由流量”脚本示例**后创建的，该示例可以通过 [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 或 [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 部署。

6. 选择“下载拓扑”，以 svg 格式将映像下载为可编辑文件。

图中所示资源是虚拟网络中的网络组件子集。 例如，在显示网络安全组时，其中的安全规则不在图中显示。 线条表示以下两种关系中的一种：包含或关联，不过并未在图中进行区分。 若要查看虚拟网络中资源的完整列表以及资源之间关系的类型，请使用 [PowerShell](#powershell) 或 [Azure CLI](#azure-cli) 生成拓扑。

## <a name = "azure-cli"></a>查看拓扑 - Azure CLI

可以运行后续步骤中的命令：
- 在 Azure Cloud Shell 中选择任意命令右上角的“试用”。 Azure Cloud Shell 是免费的交互式 Shell，它预安装有常用 Azure 工具并将其配置为与帐户一起使用。
- 通过在计算机中运行 CLI。 如果在计算机中运行 CLI，则本文中的步骤要求使用 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。

所用帐户必须拥有所需的[权限](required-rbac-permissions.md)。

1. 如果你已在要为其创建拓扑的虚拟网络所在的区域中有一个网络观察程序，请跳至步骤 3。 使用 [az group create](/cli/azure/group#az_group_create) 创建一个包含网络观察程序的资源组。 以下示例在“eastus”区域创建此资源组：

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. 使用 [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) 创建网络观察程序。 以下示例在“eastus”区域创建网络观察程序：

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. 使用 [az network watcher show-topology](/cli/azure/network/watcher#az-network-watcher-show-topology) 查看拓扑。 以下示例查看名为 *MyResourceGroup* 的资源组的拓扑：

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    仅为与 *MyResourceGroup* 资源组具有相同的资源组且与网络观察程序具有相同的区域的资源返回拓扑信息。 例如，不会显示 *MyResourceGroup* 之外的资源组中存在的网络安全组，即使该网络安全组与 *MyVnet* 虚拟网络中的子网相关联。

  详细了解返回的输出中的[关系](#relationhips)和[属性](#properties)。 如果没有现有的可以查看其拓扑的虚拟网络，则可使用[通过网络虚拟设备路由流量](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)脚本示例创建一个。 若要查看拓扑图并以可编辑文件的形式来下载它，请使用[门户](#azure-portal)。

## <a name = "powershell"></a>查看拓扑 - PowerShell

可以运行后续步骤中的命令：
- 在 Azure Cloud Shell 中选择任意命令右上角的“试用”。 Azure Cloud Shell 是免费的交互式 Shell，它预安装有常用 Azure 工具并将其配置为与帐户一起使用。
- 通过在计算机中运行 PowerShell。 如果在计算机中运行 PowerShell，则本文中的步骤要求使用 AzureRm 模块 5.7.0 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。

所用帐户必须拥有所需的[权限](required-rbac-permissions.md)。

1. 如果你已在要为其创建拓扑的虚拟网络所在的区域中有一个网络观察程序，请跳至步骤 3。 使用 [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) 创建一个包含网络观察程序的资源组。 以下示例在“eastus”区域创建此资源组：

    ```azurepowershell-interactive
    New-AzureRmResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. 使用 [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) 创建网络观察程序。 以下示例在“eastus”区域创建网络观察程序：

    ```azurepowershell-interactive
    New-AzureRmNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. 使用 [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher) 检索网络观察程序实例。 以下示例在“美国东部”区域检索网络观察程序：

    ```azurepowershell-interactive
    $nw = Get-AzurermResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzureRmNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. 使用 [Get-AzureRmNetworkWatcherTopology](/powershell/module/azurerm.network/get-azurermnetworkwatchertopology) 检索拓扑。 以下示例在名为 *MyResourceGroup* 的资源组中检索虚拟网络的拓扑：

    ```azurepowershell-interactive
    Get-AzureRmNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   仅为与 *MyResourceGroup* 资源组具有相同的资源组且与网络观察程序具有相同的区域的资源返回拓扑信息。 例如，不会显示 *MyResourceGroup* 之外的资源组中存在的网络安全组，即使该网络安全组与 *MyVnet* 虚拟网络中的子网相关联。

  详细了解返回的输出中的[关系](#relationhips)和[属性](#properties)。 如果没有现有的可以查看其拓扑的虚拟网络，则可使用[通过网络虚拟设备路由流量](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)脚本示例创建一个。 若要查看拓扑图并以可编辑文件的形式来下载它，请使用[门户](#azure-portal)。

## <a name="relationships"></a>关系

在拓扑中返回的所有资源与其他资源的关系属于以下类型之一：

| 关系类型 | 示例                                                                                                |
| ---               | ---                                                                                                    |
| 包含       | 虚拟网络包含子网。 子网包含网络接口。                            |
| 关联        | 网络接口与 VM 关联。 公共 IP 地址与网络接口关联。 |

## <a name="properties"></a>属性

在拓扑中返回的所有资源具有以下属性：

- **名称**：资源的名称。
- **ID**：资源的 URI。
- **位置**：资源所在的 Azure 区域。
- **关联**：引用对象的关联列表。 每个关联包含以下属性：
    - **AssociationType**：引用子对象和父对象之间的关系。 有效值为 *Contains* 或 *Associated*。
    - **名称**：引用资源的名称。
    - **ResourceId**：在关联中引用的资源的 URI。

## <a name="next-steps"></a>后续步骤

- 了解如何使用网络观察程序的“IP 流验证”功能[诊断出入 VM 的网络流量的筛选器问题](diagnose-vm-network-traffic-filtering-problem.md)
- 了解如何使用网络观察程序的“下一跃点”功能[诊断流出 VM 的网络流量的路由问题](diagnose-vm-network-routing-problem.md)