---
title: 从 Azure 开发人员测试实验室中的另一个实验室导入虚拟机
description: 本文介绍如何将虚拟机从其他实验室导入到 Azure DevTest 实验室中的当前实验室中。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759510"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>从 Azure 开发人员测试实验室中的另一个实验室导入虚拟机
本文提供有关如何将虚拟机从其他实验室导入实验室的信息。

## <a name="scenarios"></a>方案
下面是一些需要将 VM 从一个实验室导入另一个实验室的情况：

- 团队中的一个人正在迁移到企业内的另一个组，并希望将开发人员桌面带到新团队的 DevTest 实验室。
- 该组已达到[订阅级别配额](../azure-resource-manager/management/azure-subscription-service-limits.md)，并希望将团队拆分为几个订阅
- 公司正在转向快速路由（或其他一些新的网络拓扑），团队希望移动虚拟机以使用此新基础架构

## <a name="solution-and-constraints"></a>解决方案和约束
此功能使您能够将一个实验室（源）中的 VM 导入到另一个实验室（目标）。 您可以选择为进程中的目标 VM 指定新名称。 导入过程包括所有依赖项，如磁盘、计划、网络设置等。

该过程确实需要一些时间，并受以下因素的影响：

- 连接到源计算机的磁盘的数量/大小（因为它是复制操作而不是移动操作）
- 到目的地的距离（例如，美国东部区域到东南亚）。

该过程完成后，源虚拟机将保持关闭状态，并且新虚拟机在目标实验室中运行。

在计划将 VM 从一个实验室导入另一个实验室时，需要注意两个关键约束：

- 支持跨订阅和区域导入虚拟机，但订阅必须与同一 Azure 活动目录租户关联。
- 在源实验室中，虚拟机不得处于可声明状态。
- 您是源实验室中的 VM 所有者，也是目标实验室中实验室的所有者。
- 目前，此功能仅通过电源壳和 REST API 支持。

## <a name="use-powershell"></a>使用 PowerShell
从[GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)下载导入虚拟机.ps1 文件。 可以使用该脚本将源实验室中的单个 VM 或所有 VM 导入目标实验室。

### <a name="use-powershell-to-import-a-single-vm"></a>使用 PowerShell 导入单个 VM
执行此 powershell 脚本需要标识源 VM 和目标实验室，并可以选择提供用于目标计算机的新名称：

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>使用 PowerShell 导入源实验室中的所有 VM
如果未指定源虚拟机，脚本将自动导入 DevTest 实验室中的所有 VM。  例如：

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>使用 HTTP REST 导入 VM
REST 调用很简单。 您可以提供足够的信息来标识源和目标资源。 请记住，操作发生在目标实验室资源上。

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
