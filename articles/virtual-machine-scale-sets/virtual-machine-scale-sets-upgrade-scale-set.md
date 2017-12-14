---
title: "升级 Azure 虚拟机规模集 | Microsoft Docs"
description: "升级 Azure 虚拟机规模集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: guybo
ms.openlocfilehash: aef243e34f1d5fc8240576a9803bb8b08693a7b7
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="upgrade-a-virtual-machine-scale-set"></a>升级虚拟机规模集
本文介绍了如何在不停机的情况下为 Azure 虚拟机规模集推出 OS 更新。 在此上下文中，OS 更新涉及到更改 OS 的版本或 SKU，或者更改自定义映像的 URI。 在不停机的情况下更新意味着一次只更新一台虚拟机或分组更新（如一次更新一个容错域），而不是一次更新所有虚拟机。 这样做能使没有进行升级的虚拟机继续运行。

为了避免混淆，我们来区分一下可能要执行的操作系统更新的四种类型：

* 更改平台映像的版本或 SKU。 例如，将 Ubuntu 14.04.2-LTS 版本从 14.04.201506100 更改为 14.04.201507060，或者将 Ubuntu 15.10/最新 SKU 更改为 16.04.0-LTS/最新。 本文中介绍了此方案。
* 更改指向生成的自定义映像的新版本的 URI（“属性” > “virtualMachineProfile” > “storageProfile” > “osDisk” > “映像” > “URI”）。 本文中介绍了此方案。
* 更改使用 Azure 托管磁盘创建的规模集的映像引用。
* 从虚拟机内部修补 OS（这样的示例包括安装安全修补程序以及运行 Windows 更新）。 尽管此方案是受支持的，但在本文中不予讨论。

此处不讨论部署为 [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) 群集的一部分的虚拟机规模集。 有关修补 Service Fabric 的详细信息，请参阅[在 Service Fabric 群集中修补 Windows OS](https://docs.microsoft.com/azure/service-fabric/service-fabric-patch-orchestration-application)。

更改平台映像的 OS 版本/SKU 或自定义映像的 URI 的基本顺序，如下所示：

1. 获取虚拟机规模集模型。
2. 更改模型中的版本、SKU、映像引用或 URI 值。
3. 更新模型。
4. 对规模集中的虚拟机执行 *manualUpgrade* 调用。 只有将规模集中的 *upgradePolicy* 设置为“手动”时，此步骤才适用。 如果设置为“自动”，则所有的虚拟机会同时升级，从而导致停机。

在记住了这些信息后，来看一下如何在 PowerShell 中以及使用 REST API 更新规模集的版本。 尽管这些示例讨论的是平台映像，但本文提供了足量的信息，使你能够针对自定义映像修改此过程。

## <a name="powershell"></a>PowerShell
此示例会更新 Windows 虚拟机规模集（创建到新版本 4.0.20160229。 更新模型后，它将一次更新一个虚拟机实例。

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

如果要更新自定义映像的 URI，而不是更改平台映像版本，请将 "set the new version" 一行替换为将要更新源映像 URI 的命令。 例如，如果创建规模集时没有使用 Azure 托管磁盘，更新将如下所示：

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```

如果基于自定义映像的规模集是使用 Azure 托管磁盘创建的，则映像引用将会更新。 例如：

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.id = $newImageReference
```

## <a name="the-rest-api"></a>REST API
这里有一些使用 Azure REST API 推出 OS 版本更新的 Python 示例。 两种都使用了 Azure REST API 包装函数的轻型 [azurerm](https://pypi.python.org/pypi/azurerm) 库，可先对规模集模型执行 GET，然后使用更新的模型执行 PUT。 它们也会查看虚拟机实例视图来根据更新域识别虚拟机。

### <a name="vmssupgrade"></a>Vmssupgrade
 [Vmssupgrade](https://github.com/gbowerman/vmsstools) 是用于为正在运行的虚拟机规模集推出 OS 升级的 Python 脚本，一次一个更新域。

![用于选择虚拟机或更新域的 Vmssupgrade 脚本](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

使用此脚本，可选择要更新的具体虚拟机或者指定更新域。 它支持更改平台映像版本或更改自定义映像的 URI。

### <a name="vmsseditor"></a>Vmsseditor
[Vmsseditor](https://github.com/gbowerman/vmssdashboard) 是一个适用于虚拟机规模集的通用编辑器，它以热度地图的形式显示虚拟机状态，其中一行表示一个更新域。 可以使用新版本、SKU 或自定义映像 URI 等来更新规模集的模型，并选择要升级的容错域。 执行此操作时，该更新域中的所有虚拟机都将升级到新模型。 或者，可以根据所选的批大小执行滚动升级。  

以下屏幕截图显示了 Ubuntu 14.04-2LTS 版本 14.04.201507060 的规模集的模型。 自此屏幕截图截取之后，此工具增加了很多新选项。

![适用于 Ubuntu 14.04-2LTS 的规模集的 Vmsseditor 模型](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

单击“升级”和“获取详细信息”之后，UD 0 中的虚拟机将开始进行更新。

![显示正在进行更新的 Vmsseditor](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

