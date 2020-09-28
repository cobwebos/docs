---
title: 将网络观察程序扩展更新到最新版本
description: 了解如何将网络观察程序扩展更新到最新版本
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: c386685d63894472623ffc4392a529541a91391c
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410301"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>如何将网络观察程序扩展更新到最新版本 

## <a name="overview"></a>概述

[Azure 网络观察程序](../../network-watcher/network-watcher-monitoring-overview.md)是一项网络性能监视、诊断和分析服务，可以对 Azure 网络进行监视。 网络观察程序代理虚拟机扩展是按需捕获网络流量和运行 Azure 虚拟机上的其他高级功能所必需的。 网络观察程序扩展由连接监视器、连接监视器 (预览) 、连接故障排除和数据包捕获等功能使用。   

## <a name="prerequisites"></a>先决条件
本文档假定你已在虚拟机中安装了网络观察程序扩展，并提供有关将其更新到最新版本的说明。 

## <a name="latest-version"></a>最新版本
最新版本的网络观察程序扩展目前正在进行 `1.4.1654.1` 。

## <a name="updating-your-extension"></a>更新扩展 

### <a name="check-your-extension-version"></a>检查扩展版本  

**使用 Azure 门户**

1. 在 Azure 门户中转到 VM 的 "扩展" 边栏选项卡。   
2. 单击 "AzureNetworkWatcher" 扩展，查看详细信息窗格。  
3. 在 "版本" 字段中找到版本号。  

**使用 Azure CLI** 在 Azure CLI 提示符下运行以下命令。   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

找到输出中的 AzureNetworkWatcher 扩展，并从输出中的 "TypeHandlerVersion" 字段标识版本号。  


**使用 PowerShell** 在 PowerShell 提示符下运行以下命令：   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

找到输出中的 AzureNetworkWatcher 扩展，并从输出中的 "TypeHandlerVersion" 字段标识版本号。   


### <a name="update-your-extension"></a>更新扩展

如果你的版本低于 `1.4.1654.1` 当前最新版本)  (，请使用以下任一选项更新你的扩展。 

**选项1：使用 PowerShell**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**选项2：使用 Azure CLI**  

强制升级 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

如果这不起作用。 使用以下步骤重新删除并安装扩展。 这会自动添加最新版本。 

删除扩展 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

再次安装扩展

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**选项3：重新启动 Vm**

如果对 NetworkWatcher 扩展将自动升级设置为 true，则为。 重新启动 VM 将安装最新扩展。


## <a name="support"></a>支持

如果在本文的任何位置需要更多帮助，可以参阅网络观察程序扩展文档 ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux)、 [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)) 或与 MSDN azure 上的 azure 专家联系 [Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
