---
title: 批量注册 Azure 中的 SQL 虚拟机与 SQL VM 资源提供程序 |微软文档
description: 批量注册 SQL Server VM VM VM 提供程序以提高可管理性。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 015aa4c209a99921a930a51b15c3d0230722519a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353889"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>批量注册 Azure 中的 SQL 虚拟机与 SQL VM 资源提供程序

本文介绍如何使用`Register-SqlVMs`PowerShell cmdlet 向 SQL VM 资源提供程序批量注册 Azure 中的 SQL Server 虚拟机 （VM）。

`Register-SqlVMs` cmdlet 可用于注册给定订阅、资源组或特定虚拟机列表中的所有虚拟机。 cmdlet 将在_轻量级_管理模式下注册虚拟机，然后生成[报表和日志文件](#output-description)。 

注册过程没有风险，没有停机时间，也不会重新启动 SQL Server 或虚拟机。 

有关资源提供程序的详细信息，请参阅 SQL [VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)。 

## <a name="prerequisites"></a>先决条件

要向资源提供程序注册 SQL Server VM，您需要以下操作： 

- 已[注册到资源提供程序并](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp)包含未注册的 SQL Server 虚拟机的[Azure 订阅](https://azure.microsoft.com/free/)。 
- 用于注册虚拟机的客户端凭据存在于以下任何 RBAC 角色中：**虚拟机参与者**、**参与者**或**所有者**。 
- 最新版本的Az [PowerShell](/powershell/azure/new-azureps-module-az). 
- 最新版本的[Az.Sql虚拟计算机](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0)。

## <a name="getting-started"></a>入门

在继续操作之前，必须首先创建脚本的本地副本，将其导入为 PowerShell 模块，然后连接到 Azure。 

### <a name="create-script"></a>创建脚本

要创建脚本，请从本文末尾复制[完整脚本](#full-script)，并将其保存在本地为`RegisterSqlVMs.psm1`。 

### <a name="import-script"></a>导入脚本

创建脚本后，可以在 Powershell 终端中将其导入为模块。 

打开管理 PowerShell 终端并导航到保存`RegisterSqlVMs.psm1`文件的位置。 然后，运行以下 PowerShell cmdlet 以将脚本导入为模块： 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>连接到 Azure

使用以下 PowerShell cmdlet 连接到 Azure：

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>订阅列表中的所有 VM 

使用以下 cmdlet 在订阅列表中注册所有 SQL Server 虚拟机：

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

示例输出： 

```
Number of Subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>单个订阅中的所有 VM

使用以下 cmdlet 在单个订阅中注册所有 SQL Server 虚拟机： 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

示例输出：

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>多个资源组中的所有 VM

使用以下 cmdlet 在单个订阅中注册多个资源组中的所有 SQL Server 虚拟机：

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

示例输出：

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>资源组中的所有 VM

使用以下 cmdlet 将所有 SQL Server 虚拟机注册到单个资源组中： 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

示例输出：

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-single-resource-group"></a>单个资源组中的特定 VM

使用以下 cmdlet 在单个资源组中注册特定的 SQL Server 虚拟机：

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

示例输出：

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vm"></a>特定 VM

使用以下 cmdlet 注册特定的 SQL Server 虚拟机： 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

示例输出：

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>输出说明

每次使用`Register-SqlVMs`cmdlet 时都会生成报表和日志文件。 

### <a name="report"></a>报表

报表生成为名为`.txt`文件`RegisterSqlVMScriptReport<Timestamp>.txt`的文件，其中时间戳是启动 cmdlet 的时间。 报告列出了以下详细信息：

| **输出值** | **说明** |
| :--------------  | :-------------- | 
| 由于您没有访问权限或凭据不正确，订阅注册失败数 | 这提供了与提供的身份验证有问题的订阅的数量和列表。 通过搜索订阅 ID，可以在日志中找到详细的错误。 | 
| 由于未注册到 RP 而无法尝试的订阅数 | 本节包含尚未注册到 SQL VM 资源提供程序的订阅的计数和列表。 |
| 找到的 VM 总数 | 在传递给 cmdlet 的参数范围内找到的虚拟机的计数。 | 
| VM 已注册 | 已跳过的虚拟机的计数，因为它们已注册到资源提供程序。 |
| 成功注册的 VM 数量 | 运行 cmdlet 后成功注册的虚拟机的计数。 以 格式`SubscriptionID, Resource Group, Virtual Machine`列出已注册的虚拟机。 | 
| 由于错误而无法注册的 VM 数量 | 由于某些错误而无法注册的虚拟机计数。 错误的详细信息可以在日志文件中找到。 | 
| 当 VM 或 VM 上的阵风代理未运行时跳过的 VM 数 | 无法注册为虚拟机或虚拟机上的来宾代理的虚拟机的计数和列表未运行。 启动虚拟机或来宾代理后，可以重试这些程序。 详细信息可在日志文件中找到。 |
| 由于未在 Windows 上运行 SQL 服务器而跳过的 VM 数 | 因未运行 SQL Server 或不是 Windows 虚拟机而跳过的虚拟机数。 虚拟机以格式`SubscriptionID, Resource Group, Virtual Machine`列出。 | 
| &nbsp; | &nbsp; |

### <a name="log"></a>日志 

错误记录在名为"`VMsNotRegisteredDueToError<Timestamp>.log`时间戳"的日志文件中，其中时间戳是脚本启动的时间。 如果错误位于订阅级别，日志包含逗号分隔的订阅 ID 和错误消息。 如果错误与虚拟机注册，日志包含订阅 ID、资源组名称、虚拟机名称、错误代码和按逗号分隔的消息。 

## <a name="remarks"></a>备注

使用提供的脚本向资源提供程序注册 SQL Server VM 时，请考虑以下事项：

- 与资源提供程序注册需要在 SQL Server VM 上运行的来宾代理。 Windows Server 2008 映像没有来宾代理，因此这些虚拟机将失败，必须使用[NoAgent 管理模式](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)手动注册。
- 内置重试逻辑以克服透明错误。 如果虚拟机已成功注册，则它是一种快速操作。 但是，如果注册失败，则重试每个虚拟机。  因此，您应该留出大量时间来完成注册过程，尽管实际时间要求取决于错误的类型和数量。 

## <a name="full-script"></a>完整脚本

有关 GitHub 上的完整脚本，请参阅[批量注册具有 Az PowerShell 的 SQL VM。](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1) 

复制完整脚本并将其另存为`RegisterSqLVMs.psm1`。

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL 服务器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL 服务器常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL 服务器定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL 服务器的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)
