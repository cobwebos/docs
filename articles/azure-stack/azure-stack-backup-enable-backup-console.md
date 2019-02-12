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
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 1585eb460cc5f8ae437ee59a596dc7a854a108e7
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "55995724"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>从管理门户为 Azure Stack 启用备份
启用基础结构备份服务通过管理门户，以便 Azure Stack 可以生成基础结构备份。 硬件合作伙伴可以使用这些备份来还原你的环境使用的云恢复[灾难性故障](./azure-stack-backup-recover-data.md)。 云恢复的目的是为了确保操作员和用户在恢复完成后可以重新登录回门户。 用户可以包括基于角色的访问权限和角色、 原始计划、 产品/服务，和以前定义的计算、 存储、 网络配额，以及密钥保管库机密还原其订阅。

但是，不备份 IaaS Vm，网络配置，基础结构备份服务和存储资源，例如存储帐户、 blob、 表中，依次类推，因此用户在登录云恢复完成后不会看到任何其先前已资源。 平台即服务 (PaaS) 资源和数据也不由该服务备份。 

管理员和用户负责独立于基础结构备份过程，备份和还原 IaaS 和 PaaS 资源。 有关备份 IaaS 和 PaaS 资源的信息，请参阅以下链接：

- [虚拟机](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [应用服务](https://docs.microsoft.com/azure/app-service/manage-backup)
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

9. 加密设置中提供的证书.cer 文件框中的证书。 在证书中使用此公钥加密备份文件。 应提供仅包含公钥部分配置备份设置时的证书。 一旦第一次设置此证书或将来轮换证书时，只能查看证书的指纹。 您不能下载或查看已上传的证书文件。 若要创建证书文件，请运行以下 PowerShell 命令使用的公共和私有密钥创建自签名的证书并导出证书及仅公钥部分。

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

    > [!Note]  
    > **1901 及更高版本**:Azure Stack 接受证书基础结构备份数据进行加密。 请确保使用安全的位置中的公共和专用密钥存储的证书。 出于安全原因，不建议使用证书的公钥和私钥的密钥来配置备份设置。 有关如何管理此证书的生命周期的详细信息，请参阅[基础结构备份服务最佳实践](azure-stack-backup-best-practices.md)。

10. 选择“确定”以保存备份控制器设置。

![Azure Stack - 备份控制器设置](media/azure-stack-backup/backup-controller-settings-certificate.png)

## <a name="start-backup"></a>启动备份
若要启动备份，请单击“立即备份”以启动按需备份。 按需备份不会修改已计划的下次备份的时间。 任务完成后，可以在“概要”中确认设置：

![Azure Stack - 按需备份](media/azure-stack-backup/scheduled-backup.png)

还可以在 Azure Stack 管理计算机上运行 PowerShell cmdlet **Start-AzsBackup**。 有关详细信息，请参阅[备份 Azure Stack](azure-stack-backup-back-up-azure-stack.md)。

## <a name="enable-or-disable-automatic-backups"></a>启用或禁用自动备份
在启用备份时会自动计划备份。 可以在“概要”中检查下一次计划备份时间。 

![Azure Stack - 按需备份](media/azure-stack-backup/on-demand-backup.png)

如果需要禁用将来的已计划备份，请单击“禁用自动备份”。 禁用自动备份将保留所配置的备份设置，并将保留备份计划。 此操作只是告知计划程序跳过将来的备份。 

![Azure Stack - 禁用计划的备份](media/azure-stack-backup/disable-auto-backup.png)

在“概要”中确认已禁用了将来的已计划备份：

![Azure Stack - 确认备份已禁用](media/azure-stack-backup/confirm-disable.png)

单击“启用自动备份”以告知计划程序在计划的时间启动将来的备份。 

![Azure Stack - 启用计划的备份](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> 如果在更新到 1807 之前配置了基础结构备份，则将禁用自动备份。 这样，由 Azure Stack 启动的备份不会与由外部任务计划引擎启动的备份冲突。 在禁用任何外部任务计划程序后，请单击“启用自动备份”。

## <a name="update-backup-settings"></a>更新备份设置
适用于 1901 年的支持已弃用加密密钥。 如果要在 1901年中首次配置备份，必须使用证书。 Azure Stack 支持仅当更新到 1901年之前配置该密钥的加密密钥。 三个版本将继续向后兼容性模式。 之后，将不再支持加密密钥。 

### <a name="default-mode"></a>默认模式
在加密设置中，如果要安装或更新到适用于 1901 年后第一次配置基础结构备份必须配置备份的证书。 不再支持使用的加密密钥。 

若要更新用于加密备份数据的证书，你可以上载新。CER 文件使用的公钥部分，然后选择确定以保存设置。 

新的备份将开始在新的证书中使用的公钥。 对与以前的证书创建的所有现有备份没有影响。 请确保将围绕旧的证书保存在安全位置，如果需要为云恢复。

![Azure Stack-查看证书指纹](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>向后兼容性模式
如果更新到 1901年之前配置备份，设置会累加行为中进行任何更改。 在这种情况下，加密密钥支持向后兼容性。 您可以选择更新加密密钥或切换为使用的证书。 将具有三个版本以继续更新加密密钥。 使用这一次转换到证书。 

![Azure Stack-在向后兼容性模式下使用加密密钥](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> 从加密密钥更新到证书是一个单向操作。 此更改后，您不能切换到加密密钥。 所有现有备份将保持加密状态与以前的加密密钥。 

![Azure Stack-在向后兼容性模式下使用加密证书](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>后续步骤

了解如何运行备份。 请参阅[备份 Azure Stack](azure-stack-backup-back-up-azure-stack.md)

了解如何验证备份是否已运行。 请参阅[确认在管理门户中完成的备份](azure-stack-backup-back-up-azure-stack.md)