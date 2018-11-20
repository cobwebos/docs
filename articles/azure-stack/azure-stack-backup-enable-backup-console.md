---
title: 从管理门户为 Azure Stack 启用备份 | Microsoft Docs
description: 通过管理门户启用基础结构备份服务，以便出现故障时可以还原 Azure Stack。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: d7d47b61c926c6704a06dacc55f00d77a1266988
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2018
ms.locfileid: "51038359"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>从管理门户为 Azure Stack 启用备份
通过管理门户启用基础结构备份服务，以便 Azure Stack 可以生成备份。 出现[灾难性故障](.\azure-stack-backup-recover-data.md)时，可以通过云恢复使用这些备份还原环境。 云恢复的目的是为了确保操作员和用户在恢复完成后可以重新登录回门户。 用户将恢复其订阅，包括基于角色的访问权限和角色、原始计划、套餐以及先前定义的计算、存储和网络配额。

但是，基础结构备份服务不会备份 IaaS VM、网络配置和存储资源（如存储帐户、Blob、表等），因此在云恢复完成后登录的用户将看不到他们以前存在的任何资源。 平台即服务 (PaaS) 资源和数据也不由该服务备份。 

管理员和用户负责独立于基础结构备份过程，备份和还原 IaaS 和 PaaS 资源。 有关备份 IaaS 和 PaaS 资源的信息，请参阅以下链接：

- [虚拟机](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [应用服务](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>启用或重新配置备份

1. 打开 [Azure Stack 管理门户](azure-stack-manage-portals.md)。
2. 选择“所有服务”，然后在“管理”类别下选择“基础结构备份”。 在“基础结构备份”边栏选项卡中选择“配置”。
3. 键入**备份存储位置**的路径。 使用通用命名约定 (UNC) 字符串表示单独的设备上托管的文件共享的路径。 UNC 字符串指定资源（如共享文件或设备）的位置。 对于服务，可以使用 IP 地址。 若要确保备份数据在发生灾难后的可用性，设备应放置在单独的位置。

    > [!Note]  
    > 如果环境支持从 Azure Stack 基础结构网络到企业环境的名称解析，则可以使用 FQDN（而不是 IP）。
    
4. 使用具有足够访问权限的域和用户名输入**用户名**，以便读取和写入文件。 例如，`Contoso\backupshareuser`。
5. 键入用户的**密码**。
6. 再次键入密码以**确认密码**。
7. “频率(小时)”决定了以何频率创建备份。 默认值为 12。 计划程序支持的最大值为 12，最小值为 4。 
8. “保留期(天)”决定了备份在外部位置保留多少天。 默认值为 7。 计划程序支持的最大值为 14，最小值为 2。 超过保留期的备份会自动从外部位置删除。

    > [!Note]  
    > 如果希望对超过保留期的备份进行存档，请确保在计划程序删除备份之前对这些文件进行备份。 如果减小备份保留期（例如从 7 天到 5 天），则计划程序将删除超过新的保留期的所有备份。 在更新此值之前，请确保删除备份没有问题。 

9. 在“加密密钥”框中提供预共享密钥。 使用此密钥加密备份文件。 请确保将此密钥存储在安全位置。 首次设置此密钥或将来轮换密钥后，都无法从此界面查看此密钥。 若要创建密钥，请运行以下 Azure Stack PowerShell 命令：
    ```powershell
    New-AzsEncryptionKeyBase64
    ```
10. 选择“确定”以保存备份控制器设置。

    ![Azure Stack - 备份控制器设置](media\azure-stack-backup\backup-controller-settings.png)

## <a name="start-backup"></a>启动备份
若要启动备份，请单击“立即备份”以启动按需备份。 按需备份不会修改已计划的下次备份的时间。 任务完成后，可以在“概要”中确认设置：

![Azure Stack - 按需备份](media\azure-stack-backup\scheduled-backup.png)

还可以在 Azure Stack 管理计算机上运行 PowerShell cmdlet **Start-AzsBackup**。 有关详细信息，请参阅[备份 Azure Stack](azure-stack-backup-back-up-azure-stack.md)。

## <a name="enable-or-disable-automatic-backups"></a>启用或禁用自动备份
在启用备份时会自动计划备份。 可以在“概要”中检查下一次计划备份时间。 

![Azure Stack - 按需备份](media\azure-stack-backup\on-demand-backup.png)

如果需要禁用将来的已计划备份，请单击“禁用自动备份”。 禁用自动备份将保留所配置的备份设置，并将保留备份计划。 此操作只是告知计划程序跳过将来的备份。 

![Azure Stack - 禁用计划的备份](media\azure-stack-backup\disable-auto-backup.png)

在“概要”中确认已禁用了将来的已计划备份：

![Azure Stack - 确认备份已禁用](media\azure-stack-backup\confirm-disable.png)

单击“启用自动备份”以告知计划程序在计划的时间启动将来的备份。 

![Azure Stack - 启用计划的备份](media\azure-stack-backup\enable-auto-backup.png)


> [!Note]  
> 如果在更新到 1807 之前配置了基础结构备份，则将禁用自动备份。 这样，由 Azure Stack 启动的备份不会与由外部任务计划引擎启动的备份冲突。 在禁用任何外部任务计划程序后，请单击“启用自动备份”。


## <a name="next-steps"></a>后续步骤

- 了解如何运行备份。 请参阅[备份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )。
- 了解如何验证备份是否已运行。 请参阅[在管理门户中确认已完成的备份](azure-stack-backup-back-up-azure-stack.md)。
