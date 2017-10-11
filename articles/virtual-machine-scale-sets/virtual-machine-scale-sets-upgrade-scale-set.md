---
title: "升级 Azure 虚拟机规模集 |Microsoft 文档"
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
ms.openlocfilehash: c7093e221ff8fe69ded1cfbce4f3ddeb1a195666
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="upgrade-a-virtual-machine-scale-set"></a>升级虚拟机规模集
本指南介绍了如何可以将执行操作系统更新到 Azure 虚拟机缩放集而无需任何停机。 在此上下文中，操作系统更新涉及更改版本或的操作系统的 SKU 或更改自定义图像的 URI。 正在更新而无需停机时间意味着而不在一次更新一次或组 （如一次一个容错域） 中虚拟机。 通过此操作，不升级任何虚拟机可以保持正常运行。

若要避免多义性，让我们区分操作系统更新，你可能想要执行的四种的类型：

* 更改版本或平台映像的 SKU。 例如，更改 Ubuntu 14.04.2-LTS 版本从 14.04.201506100 到 14.04.201507060，或者更改为 16.04.0-LTS/latest 的 Ubuntu 15.10/最新 SKU。 这篇文章中介绍了此方案。
* 更改你指向自定义映像的新版本的 URI 生成 (**属性** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **映像** > **uri**)。 这篇文章中介绍了此方案。
* 更改使用 Azure 管理磁盘创建一个缩放集的图像引用。
* 修补虚拟机中的从 OS （此示例包括安装安全修补程序和运行 Windows 更新）。 支持这种情况下，但不是在本文中所述。

作为的一部分部署的虚拟机规模集[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/)此处不涉及群集。 请参阅[Service Fabric 群集中的修补程序 Windows OS](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-patch-orchestration-application)有关修补 Service Fabric 的详细信息。

有关更改平台映像的 OS 版本/SKU 或自定义图像的 URI 的基本顺序如下所示：

1. 获取虚拟机缩放集模型。
2. 更改版本、 SKU、 图像引用或在模型中的 URI 值。
3. 更新模型。
4. 执行*manualUpgrade*调用规模集中的虚拟机上。 此步骤才是相关如果*upgradePolicy*设置为**手动**在你的缩放设置。 如果设置为**自动**，所有虚拟机都升级，从而造成停机时间。

使用此信息记住，我们来看如何无法更新缩放集在 PowerShell 中，以及通过使用 REST API 的版本。 这些示例涵盖的平台映像，这种情况，但这篇文章提供足够的信息供你调整此过程的自定义图像。

## <a name="powershell"></a>PowerShell
此示例将更新 Windows 虚拟机缩放集 （创建到新版本 4.0.20160229。 在更新后的模型，它执行一次更新一个虚拟机实例。

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

如果你要更新而不是更改的平台映像版本自定义图像的 URI，将"设置新的版本"行替换将更新的源映像 URI 的命令。 例如，如果创建规模集时未使用 Azure 管理磁盘，更新将如下所示：

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```

如果自定义映像基于缩放集是使用创建 Azure 托管的磁盘，然后将更新的映像引用。 例如：

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.id = $newImageReference
```

## <a name="the-rest-api"></a>REST API
以下是几个使用 Azure REST API 推出的 OS 版本更新的 Python 示例。 同时使用轻量[azurerm](https://pypi.python.org/pypi/azurerm)库的 Azure REST API 包装器函数，可执行刻度上的获取设置模型，跟 PUT 更新模型。 它们还查看虚拟机实例视图以确定更新域的虚拟机。

### <a name="vmssupgrade"></a>Vmssupgrade
 [Vmssupgrade](https://github.com/gbowerman/vmsstools)是用于将操作系统升级到正在运行的虚拟机规模的 Python 脚本一次设置一个更新域。

![选择虚拟机或更新域 Vmssupgrade 脚本](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

此脚本，可以选择特定的虚拟机，若要更新或指定一个更新域。 它支持更改平台映像版本或更改自定义图像的 URI。

### <a name="vmsseditor"></a>Vmsseditor
[Vmsseditor](https://github.com/gbowerman/vmssdashboard)是其中一行表示一个更新域的通用编辑器为虚拟机规模集显示虚拟机状态作为热度地图。 除了别的之外你可以使用新版本、 SKU 或自定义图像 URI，更新规模集的模型，然后挑选的故障域以升级。 执行此操作时，该更新域中的所有虚拟机将都升级到新的模型。 或者，你可以执行滚动升级基于所选的批次大小。  

下面的屏幕截图显示了缩放集的 Ubuntu 14.04 2LTS 版本 14.04.201507060 一个模型。 更多的选项已添加到此工具提取此屏幕截图之后。

![缩放集的 Ubuntu 14.04 2LTS Vmsseditor 模型](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

单击后**升级**然后**获取详细信息**，UD 0 中的虚拟机开始更新。

![Vmsseditor 显示更新进行中](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

