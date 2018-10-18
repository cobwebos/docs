---
title: 从 Azure 开发测试实验室的另一个实验室中导入 VM | Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中将虚拟机从另一个实验室导入到当前实验室
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 4585d151e286917c67586a02539a10ade32bdd4c
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094508"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>从 Azure 开发测试实验室的另一个实验室中导入 VM
Azure 开发测试实验室服务可显著改善虚拟机 (VM) 的管理，以便进行开发和测试活动。 当团队或基础结构需求发生更改，它允许将 VM 从一个实验室转移到另一个实验室。 下面是一些常见的方案，你可能需要执行此操作： 

- 团队成员转移到企业中的另一个组，并希望将开发虚拟机带到新团队实验室。
- 组已达到订阅级别配额，并且想要将团队拆分到多个订阅。
- 公司计划移到 Express Route（或其他一些新的网络拓扑）且团队想要移动 VM 以使用此新基础结构。

## <a name="solution-and-constraints"></a>解决方案和约束
Azure 开发测试实验室允许实验室所有者将源实验室中的 VM 导入到目标实验室。 实验室所有者可以选择在过程中为目标 VM 提供新名称。 导入过程包括如磁盘、计划、网络设置等所有依赖项。此过程需要一些时间，并受到附加到源计算机的磁盘数量/大小以及到目标（例如，美国东部区域到东南亚区域）距离的影响。 导入过程完成后，源 VM 保持关闭，新 VM 在目标实验室中运行。

计划将 VM 导入到另一个实验室时要注意两个键约束：

- 支持在订阅之间和跨区域导入虚拟机，但订阅必须与同一 Azure Active Directory 租户相关联。
- VM 不能在源实验室中处于可认领状态。

此外，若要将 VM 从一个实验室导入到另一个实验室，你需要是源实验室中的 VM 以及目标实验室中实验室的所有者。

## <a name="steps-to-import-a-vm-from-another-lab"></a>从另一个实验室导入 VM 的步骤
目前，可以通过使用 Azure PowerShell 和 REST API 将 VM 从一个实验室导入到另一个实验室。

### <a name="use-powershell"></a>使用 PowerShell
从 [Azure 开发测试实验室 Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImportVirtualMachines)将 PowerShell 脚本文件 ImportVirtualMachines.ps1 下载到本地驱动器。 

#### <a name="import-a-single-vm"></a>导入单个 VM
运行 ImportVirtualMachines.ps1 脚本从源实验室将单个 VM 导入到目标实验室。 可以使用 DestinationVirtualMachineName 参数指定正在复制的虚拟机的新名称。 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>导入所有 VM
运行 ImportVirtualMachines.ps1 脚本时，如果未指定源实验室中的 VM，该脚本会将源实验室中的所有 VM 都导入目标实验室。 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>使用 REST API
调用针对目标实验室的 REST API 并传入源实验室、订阅和 VM 信息作为参数，如以下示例所示： 

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>后续步骤

- 有关调整 VM 大小的信息，请参阅[调整 VM 的大小](devtest-lab-resize-vm.md)。
- 有关部署 VM 的信息，请参阅[重新部署 VM](devtest-lab-redeploy-vm.md)。


