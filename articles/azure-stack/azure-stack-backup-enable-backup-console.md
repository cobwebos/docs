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
ms.date: 05/11/2018
ms.author: jeffgilb
ms.openlocfilehash: 0ef8247eba4605d3c8e5ef0992ce97bce989002e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>从管理门户为 Azure Stack 启用备份
通过管理门户启用基础结构备份服务，以便 Azure Stack 可以生成备份。 你可以使用这些备份来还原环境使用的云恢复[灾难性故障](.\azure-stack-backup-recover-data.md)。 云恢复的目的是确保你的运算符和用户可以登录到门户在恢复完成后。 用户将必须包括基于角色的访问权限和角色、 原始计划、 优惠，和之前定义的计算、 存储和网络配额还原其订阅。

但是，基础结构备份服务不会备份 IaaS Vm、 网络配置和存储资源，例如存储帐户、 blob、 表和等等，因此，用户登录云恢复后完成将看不到任何以前现有资源。 平台即服务 (PaaS) 资源和数据也不会备份服务。 

管理员和用户负责备份和还原 IaaS 和 PaaS 资源单独从基础结构备份过程。 有关备份 IaaS 和 PaaS 资源的信息，请参阅以下链接：

- [虚拟机](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [应用服务](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> 通过控制台启用备份之前，需要配置备份服务。 可以使用 PowerShell 配置备份服务。 有关详细信息，请参阅[使用 PowerShell 为 Azure Stack 启用备份](azure-stack-backup-enable-backup-powershell.md)。

## <a name="enable-backup"></a>启用备份

1. 打开 Azure 堆栈管理门户在[ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external)。
2. 选择“更多服务” > “基础结构备份”。 在“基础结构备份”边栏选项卡中选择“配置”。

    ![Azure Stack - 备份控制器设置](media\azure-stack-backup\azure-stack-backup-settings.png)。

3. 键入**备份存储位置**的路径。 使用通用命名约定 (UNC) 字符串表示单独的设备上托管的文件共享的路径。 UNC 字符串指定资源（如共享文件或设备）的位置。 对于服务，可以使用 IP 地址。 若要确保备份数据在发生灾难后的可用性，设备应放置在单独的位置。
    > [!Note]  
    > 如果环境支持从 Azure Stack 基础结构网络到企业环境的名称解析，则可以使用 FQDN（而不是 IP）。
4. 使用域和用户名键入**用户名**。 例如，`Contoso\administrator`。
5. 键入用户的**密码**。
5. 再次键入密码以**确认密码**。
6. 在“加密密钥”框中提供预共享密钥。 使用此密钥加密备份文件。 请确保将此密钥存储在安全位置。 首次设置此密钥或将来轮换密钥后，都无法从此界面查看此密钥。 有关生成预共享密钥的详细说明，请按照[使用 PowerShell 为 Azure Stack 启用备份](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key)中的脚本进行操作。 
7. 选择“确定”以保存备份控制器设置。

若要执行备份，需要下载 Azure Stack Tools，然后在 Azure Stack 管理节点上运行 PowerShell cmdlet **Start-AzSBackup**。 有关详细信息，请参阅[备份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )。

## <a name="next-steps"></a>后续步骤

- 了解如何运行备份。 请参阅[备份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )。
- 了解如何验证备份是否已运行。 请参阅[在管理门户中确认已完成的备份](azure-stack-backup-back-up-azure-stack.md)。
