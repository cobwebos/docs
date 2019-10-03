---
title: Azure 备份, 适用于在 Azure VM 中运行的 SQL Server
description: 如何注册 Azure 备份 SQL Server 在 Azure VM 中运行
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: vijayts
ms.openlocfilehash: 25f23078af67b2f80f39faab975cbec54721c560
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871896"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure 备份, 适用于在 Azure VM 中运行的 SQL Server

Azure 备份在其他产品中提供对备份工作负荷 (例如 SQL Server 在 Azure Vm 中运行) 的支持。 由于 SQL 应用程序在 Azure VM 中运行, 因此备份服务需要访问应用程序和提取必要详细信息的权限。
为此, Azure 备份会在用户触发的注册过程中, 在运行 SQL Server 的 VM 上安装**AzureBackupWindowsWorkload**扩展。

## <a name="prerequisites"></a>先决条件

有关支持的方案的列表, 请参阅 Azure 备份支持的可支持性[矩阵](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support)。

## <a name="network-connectivity"></a>网络连接

Azure 备份支持 NSG 标记、部署代理服务器或列出的 IP 范围;有关每种方法的详细信息, 请参阅此[文](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity)。

## <a name="extension-schema"></a>扩展架构

扩展架构和属性值是服务传递到 CRP API 的配置值 (运行时设置)。 注册和升级过程中将使用这些配置值。 **AzureBackupWindowsWorkload**扩展还使用此架构。 架构已预设置;可以在 "objectStr" 字段中添加新参数

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

以下 JSON 显示 WorkloadBackup 扩展的架构。  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>属性值

名称 | 值/示例 | 数据类型
 --- | --- | ---
区域设置 | zh-cn  |  string
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objectStr <br/> PublicSettings  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "virtualmachines/"  | string
objectStr <br/> ProtectedSettings | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | string
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | 字符串
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | 字符串


## <a name="template-deployment"></a>模板部署

建议将 AzureBackupWindowsWorkload 扩展添加到虚拟机, 方法是启用虚拟机上的 SQL Server 备份。 这可以通过为在 SQL Server VM 上自动备份而设计的[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup)来实现。


## <a name="powershell-deployment"></a>PowerShell 部署

需要使用恢复服务保管库 "注册" 包含 SQL 应用程序的 Azure VM。 在注册期间, 会在 VM 上安装 AzureBackupWindowsWorkload 扩展。 使用 [AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) CMDLET 注册 VM。
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
此命令将返回此资源的**备份容器**, 状态将为 "**已注册**"。


## <a name="next-steps"></a>后续步骤

- [详细了解](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot)Azure SQL Server VM 备份故障排除指南
- [有关备份](https://docs.microsoft.com/azure/backup/faq-backup-sql-server)azure 虚拟机 (vm) 上运行并使用 azure 备份服务的 SQL Server 数据库的常见问题。
