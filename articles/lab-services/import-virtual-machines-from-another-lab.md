---
title: 从 Azure 开发测试实验室中的其他实验室导入虚拟机
description: 本文介绍如何将虚拟机从另一个实验室导入到 Azure 开发测试实验室中的当前实验室。
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759510"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>从 Azure 开发测试实验室中的其他实验室导入虚拟机
本文介绍如何将虚拟机从另一个实验室导入实验室。

## <a name="scenarios"></a>方案
以下是需要将 Vm 从一个实验室导入到另一个实验室的一些方案：

- 团队成员正在迁移到企业内的另一个组，希望将开发人员桌面转到新团队的开发测试实验室。
- 该组已达到[订阅级配额](../azure-resource-manager/management/azure-subscription-service-limits.md)，需要将团队拆分为几个订阅
- 该公司正在迁移到快速路由（或其他一些新的网络拓扑），并且团队想要移动虚拟机以使用此新基础结构

## <a name="solution-and-constraints"></a>解决方案和约束
此功能使你能够将一个实验室中的 Vm （源）导入到另一个实验室（目标）。 您可以选择为进程中的目标 VM 提供一个新名称。 导入过程包含所有依赖项，例如磁盘、计划、网络设置等。

此过程需要一段时间，并受以下因素影响：

- 附加到源计算机的磁盘的数量/大小（因为它是复制操作，而不是移动操作）
- 与目标的距离（例如，美国东部区域到东南亚）。

完成此过程后，源虚拟机将处于关闭状态，而新的虚拟机将在目标实验室中运行。

计划将 Vm 从一个实验室导入到另一个实验室时，有两个关键约束需要注意：

- 支持跨订阅和跨区域的虚拟机导入，但必须将订阅关联到相同的 Azure Active Directory 租户。
- 虚拟机在源实验室中不得处于可认领状态。
- 你是源实验室中的 VM 所有者，以及目标实验室中实验室的所有者。
- 目前只能通过 Powershell 和 REST API 支持此功能。

## <a name="use-powershell"></a>使用 PowerShell
从[GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)下载 ImportVirtualMachines 文件。 可以使用脚本将源实验室中的单个 VM 或所有 Vm 导入到目标实验室。

### <a name="use-powershell-to-import-a-single-vm"></a>使用 PowerShell 导入单个 VM
执行此 powershell 脚本需要标识源 VM 和目标实验室，并选择性地提供要用于目标计算机的新名称：

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>使用 PowerShell 导入源实验室中的所有 Vm
如果未指定源虚拟机，则脚本会自动导入开发测试实验室中的所有 Vm。  例如：

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>使用 HTTP REST 导入 VM
REST 调用非常简单。 您可以为标识源资源和目标资源而获得足够的信息。 请记住，操作发生在目标实验室资源上。

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
- [常见问题解答](devtest-lab-faq.md)
