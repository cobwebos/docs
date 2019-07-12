---
title: Azure VM 中运行的 SQL Server 的 azure 备份
description: 如何注册 Azure 备份 SQL Server Azure VM 中运行
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: swatisachdeva
ms.openlocfilehash: 8710242e04156c8af6e5882a3cb4d42cc31e3677
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607606"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure VM 中运行的 SQL Server 的 azure 备份

Azure 备份，在其他产品/服务之间提供了支持备份工作负荷，例如 SQL Server Azure Vm 中运行。 由于 SQL 应用程序正在运行的 Azure VM 中，备份服务必须有权访问该应用程序和提取必要的详细信息。
为此，Azure 备份安装**AzureBackupWindowsWorkload**上运行的 VM，在其中 SQL Server，在注册过程中由用户触发的扩展。

## <a name="prerequisites"></a>先决条件

有关支持的方案的列表，请参阅[可支持性矩阵](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support)Azure 备份支持。

## <a name="network-connectivity"></a>网络连接

Azure 备份支持 NSG 标记，部署代理服务器或列出; 的 IP 范围有关在每个方法的详细信息，请参阅这[一文](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity)。

## <a name="extension-schema"></a>扩展架构

扩展架构和属性值是服务传递到 CRP API 的配置值 （运行时设置）。 在注册和升级期间将使用这些配置值。 **AzureBackupWindowsWorkload**扩展还使用此架构。 架构将被预设置;可以在 objectStr 字段中添加一个新的参数

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
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "microsoft.compute/virtualmachines"  | string
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | string
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | 字符串
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | 字符串


## <a name="template-deployment"></a>模板部署

我们建议添加到虚拟机 AzureBackupWindowsWorkload 扩展通过启用虚拟机上的 SQL Server 备份。 这可以通过实现[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup)用于自动执行 SQL Server VM 上的备份。


## <a name="powershell-deployment"></a>PowerShell 部署

需要注册包含与恢复服务保管库 SQL 应用程序的 Azure VM。 在注册期间，获取在 VM 上安装 AzureBackupWindowsWorkload 扩展。 使用 [寄存器 AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet 来注册 VM。
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
该命令将返回**的备份容器**资源和状态将是此**注册**。


## <a name="next-steps"></a>后续步骤

- [了解详细信息](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot)有关 Azure SQL Server 虚拟机备份故障排除指南
- [常见的问题](https://docs.microsoft.com/azure/backup/faq-backup-sql-server)有关备份 SQL Server 数据库的 Azure 虚拟机 (Vm) 上运行并使用 Azure 备份服务。
