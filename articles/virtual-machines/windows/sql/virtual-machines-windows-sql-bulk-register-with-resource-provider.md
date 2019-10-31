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
ms.openlocfilehash: 80a1ac3ebe5f49d6a63f47e08e0b16114d75e91f
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199216"
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
- 最新版本的 [Az SqlVirtualMachine] （ https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0 。

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
| 成功注册的 Vm 数 | The count of virtual machines that were successfully registered after running the cmdlet. Lists the registered virtual machines in the format `SubscriptionID, Resource Group, Virtual Machine`. | 
| Number of VMs failed to register due to error | Count of virtual machines that failed to register due to some error. The details of the error can be found in the log file. | 
| Number of VMs skipped as the VM or the gust agent on VM is not running | Count and list of virtual machines that could not be registered as either the virtual machine or the guest agent on the virtual machine were not running. These can be retried once the virtual machine or guest agent has been started. Details can be found in the log file. |
| Number of VMs skipped as they are not running SQL Server on Windows | Count of virtual machines that were skipped as they are not running SQL Server or are not a Windows virtual machine. The virtual machines are listed in the format `SubscriptionID, Resource Group, Virtual Machine`. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>日志 

Errors are logged in the log file named `VMsNotRegisteredDueToError<Timestamp>.log` where timestamp is the time when the script started. If the error is at the subscription level, the log contains the comma-separated SubscriptionID and the error message. If the error is with the virtual machine registration, the log contains the Subscription ID, Resource group name, virtual machine name, error code and message separated by commas. 

## <a name="remarks"></a>备注

When you register SQL Server VMs with the resource provider using the provided script, consider the following:

- Registration with the resource provider requires a guest agent running on the SQL Server VM. Windows Server 2008 images do not have a guest agent, so these virtual machines will fail and must be registered manually using the [NoAgent management mode](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms).
- There is retry logic built-in to overcome transparent errors. If the virtual machine is successfully registered, then it is a rapid operation. However, if the registration fails, then each virtual machine will be retried.  As such, you should allow significant time to complete the registration process -  though actual time requirement is dependent on the type and number of errors. 

## <a name="full-script"></a>完整脚本

For the full script on GitHub, see [Bulk register SQL VMs with Az PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Copy the full script and save it as `RegisterSqLVMs.psm1`.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [FAQ for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pricing guidance for SQL Server on a Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Release notes for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)
