---
title: 通过 SQL VM 资源提供程序在 Azure 中大容量注册 SQL 虚拟机 |Microsoft Docs
description: 向 SQL VM 资源提供程序大容量注册 SQL Server Vm 以提高可管理性。
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75353889"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>利用 SQL VM 资源提供程序在 Azure 中大容量注册 SQL 虚拟机

本文介绍如何使用 `Register-SqlVMs` PowerShell cmdlet 在 Azure 中批量注册 SQL Server 的虚拟机（VM）和 SQL VM 资源提供程序。

`Register-SqlVMs` cmdlet 可用于在给定的订阅、资源组或特定虚拟机列表中注册所有虚拟机。 该 cmdlet 将以_轻型_管理模式注册虚拟机，然后生成[报告和日志文件](#output-description)。 

注册过程不会带来任何风险，无需停机，也不会 SQL Server 或虚拟机重新启动。 

有关资源提供程序的详细信息，请参阅[SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)。 

## <a name="prerequisites"></a>必备组件

若要向资源提供程序注册你的 SQL Server VM，你将需要以下各项： 

- 已[向资源提供程序注册](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp)并包含未注册 SQL Server 虚拟机的[Azure 订阅](https://azure.microsoft.com/free/)。 
- 用于注册虚拟机的客户端凭据位于以下任何 RBAC 角色中： "**虚拟机参与者**"、"**参与者**" 或 "**所有者**"。 
- [Az PowerShell](/powershell/azure/new-azureps-module-az)的最新版本。 
- [SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0)的最新版本。

## <a name="getting-started"></a>入门

在继续操作之前，必须先创建脚本的本地副本，将其作为 PowerShell 模块导入并连接到 Azure。 

### <a name="create-script"></a>创建脚本

若要创建脚本，请复制本文末尾的[完整脚本](#full-script)，并将其作为 `RegisterSqlVMs.psm1`保存到本地。 

### <a name="import-script"></a>导入脚本

创建脚本后，可以将其作为模块导入到 Powershell 终端。 

打开管理 PowerShell 终端并导航到保存 `RegisterSqlVMs.psm1` 文件的位置。 然后，运行以下 PowerShell cmdlet 以将脚本导入模块： 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>连接到 Azure

使用以下 PowerShell cmdlet 连接到 Azure：

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>订阅列表中的所有 Vm 

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

## <a name="all-vms-in-a-single-subscription"></a>单个订阅中的所有 Vm

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

## <a name="all-vms-in-multiple-resource-groups"></a>多个资源组中的所有 Vm

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

## <a name="all-vms-in-a-resource-group"></a>资源组中的所有 Vm

使用以下 cmdlet 注册单个资源组中的所有 SQL Server 虚拟机： 

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

## <a name="specific-vms-in-single-resource-group"></a>单个资源组中的特定 Vm

使用以下 cmdlet 在单个资源组中注册特定 SQL Server 虚拟机：

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

使用以下 cmdlet 注册特定 SQL Server 虚拟机： 

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


## <a name="output-description"></a>输出描述

每次使用 `Register-SqlVMs` cmdlet 时都会生成报告文件和日志文件。 

### <a name="report"></a>报告

报表生成为名为 `RegisterSqlVMScriptReport<Timestamp>.txt` 的 `.txt` 文件，其中时间戳是启动 cmdlet 的时间。 该报表列出了以下详细信息：

| **输出值** | **说明** |
| :--------------  | :-------------- | 
| 由于你没有访问权限或凭据不正确而无法注册的订阅数 | 这将提供与提供的身份验证问题相关的订阅的数量和列表。 通过搜索订阅 ID，可以在日志中找到详细错误。 | 
| 由于未向 RP 注册而无法尝试的订阅数 | 此部分包含尚未注册到 SQL VM 资源提供程序的订阅的计数和列表。 |
| 找到的 Vm 总数 | 在传递到 cmdlet 的参数作用域中找到的虚拟机的计数。 | 
| 已注册 Vm | 已在资源提供程序中注册的虚拟机的计数。 |
| 成功注册的 Vm 数 | 运行 cmdlet 后成功注册的虚拟机的计数。 以 `SubscriptionID, Resource Group, Virtual Machine`的格式列出已注册的虚拟机。 | 
| 由于出错，无法注册的 Vm 数 | 由于一些错误无法注册的虚拟机的计数。 此错误的详细信息可在日志文件中找到。 | 
| 虚拟机上跳过的 vm 数或 VM 上的阵风代理未运行 | 无法注册为虚拟机或虚拟机上的来宾代理的虚拟机计数和列表未在运行。 启动虚拟机或来宾代理后，可以重试这些工作。 详细信息可在日志文件中找到。 |
| 由于未在 Windows 上运行 SQL Server 而跳过的 Vm 数 | 由于虚拟机未运行 SQL Server 或不是 Windows 虚拟机而跳过的虚拟机的计数。 虚拟机以 `SubscriptionID, Resource Group, Virtual Machine`格式列出。 | 
| &nbsp; | &nbsp; |

### <a name="log"></a>日志 

错误记录在名为 `VMsNotRegisteredDueToError<Timestamp>.log` 的日志文件中，其中 timestamp 是脚本开始的时间。 如果错误是在订阅级别，则日志包含以逗号分隔的 SubscriptionID 和错误消息。 如果错误是与虚拟机注册有关的，则日志包含订阅 ID、资源组名称、虚拟机名称、错误代码和以逗号分隔的消息。 

## <a name="remarks"></a>备注

使用提供的脚本向资源提供程序注册 SQL Server Vm 时，请考虑以下事项：

- 向资源提供程序注册需要在 SQL Server VM 上运行一个来宾代理。 Windows Server 2008 映像没有来宾代理，因此这些虚拟机将失败，必须使用[NoAgent 管理模式](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)手动注册。
- 已内置重试逻辑来克服透明错误。 如果成功注册了虚拟机，则这是一个快速操作。 但是，如果注册失败，则会重试每个虚拟机。  因此，您应该留出足够的时间来完成注册过程，但实际时间要求取决于错误的类型和数量。 

## <a name="full-script"></a>完整脚本

有关 GitHub 上的完整脚本，请参阅[用 Az PowerShell 批量注册 SQL vm](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1)。 

复制完整的脚本并将其保存为 `RegisterSqLVMs.psm1`。

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)
