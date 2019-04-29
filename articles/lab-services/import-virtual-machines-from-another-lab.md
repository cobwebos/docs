---
title: 从 Azure 开发测试实验室中的另一个实验室导入虚拟机 |Microsoft Docs
description: 了解如何从另一个实验室的虚拟机导入到当前实验室。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: ca6ed58cfabb5027830828812c4820c1b586875c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322827"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>从 Azure 开发测试实验室中的另一个实验室导入虚拟机
本文提供了有关如何从另一个实验室的虚拟机导入到你的实验室信息。

## <a name="scenarios"></a>方案
下面是一些情况下您需要从一个实验室的虚拟机导入到另一个实验室：

- 团队的个人移动到企业内的另一个组，并且想要将开发人员桌面到新团队的开发测试实验室。
- 组已达到[订阅级别配额](../azure-subscription-service-limits.md)和想要拆分到一些订阅团队
- 该公司将迁移到 Express Route （或其他一些新的网络拓扑），团队想要移动的虚拟机，若要使用此新的基础结构

## <a name="solution-and-constraints"></a>解决方案和约束
此功能，可在一个实验室 （源） 中的虚拟机导入到另一个实验室 （目标）。 （可选） 可以在过程中目标虚拟机的新名称。 导入过程包括磁盘、 计划、 网络设置等的所有依赖项。

该过程需要一些时间，并受以下因素：

- （因为它是复制操作而不是移动操作） 附加到源计算机的磁盘的数量/大小
- 到目标 （例如，美国东部区域到东南亚） 的距离。

该过程完成后，源虚拟机将保持关闭和新的其中一个在目标实验室中运行。

有两个键约束，需要注意的计划时要将 Vm 从一个实验室导入到另一个实验室：

- 支持跨订阅和区域的虚拟机导入，但订阅必须与同一 Azure Active Directory 租户相关联。
- 虚拟机不能在源实验室中的可认领状态中。
- 你是实验室的在源实验室和所有者中目标实验室中 VM 的所有者。
- 目前，只能通过 Powershell 和 REST API 支持此功能。

## <a name="use-powershell"></a>使用 PowerShell
下载文件 ImportVirtualMachines.ps1 [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)。 该脚本可用于单个 VM 或源实验室中的所有 Vm 导入到目标实验室。

### <a name="use-powershell-to-import-a-single-vm"></a>使用 PowerShell 导入单个 VM
执行此 powershell 脚本需要标识源 VM 和目标实验中，并根据需要提供要用于在目标计算机的新名称：

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>使用 PowerShell 导入源实验室中的所有 Vm
如果未指定源虚拟机，该脚本将自动导入开发测试实验室中的所有 Vm。  例如：

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>使用 HTTP REST 来导入的 VM
REST 调用非常简单。 提供足够信息来识别源和目标资源。 请记住，在目标实验室资源上进行的操作。

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [设置实验室的策略](devtest-lab-get-started-with-lab-policies.md)
- [常见问题](devtest-lab-faq.md)
