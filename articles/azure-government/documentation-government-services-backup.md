---
title: "Azure 政府版备份 | Microsoft Docs"
description: "本文概述了 Azure 政府版中可用的 Azure 备份功能。"
services: azure-government
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 1/5/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: fa00142a9e89c5ad2630f688ea9771a1a542c052
ms.openlocfilehash: e5f89f845302ecb890caa50dd8f86503b29f1154


---
# <a name="azure-government-backup"></a>Azure 政府版备份

本文概述了 Azure 备份服务，并列出 Azure 政府版中可用的备份功能。 Azure 备份是基于 Azure 的服务，可用于将数据备份到 Microsoft 云（或保护数据）。 保护 Azure 中的数据不只意味着将数据备份到云，还包括将数据还原到云，或还原到本地安装。 Azure 备份具备以下关键优势：

- 自动存储管理
- 无限缩放
- 多种存储选项
- 无限制的数据传输
- 数据加密
- 应用程序一致性备份
- 长期保留

如果不熟悉 Azure 备份，并希望获取可用功能的概述，请阅读[什么是 Azure 备份](../backup/backup-introduction-to-azure-backup.md)一文。

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Azure 政府版备份中可用的 Azure 备份组件

可以使用 Azure 备份保护：文件、文件夹、卷、虚拟机、应用程序和工作负荷。 根据要保护的内容及数据的存放位置，使用不同的 Azure 备份组件。 以下各节提供 Azure 备份公共文档中有关每个组件的文章链接。 这些章节按照经典门户或 Azure 门户进行划分。 如果打算使用 Resource Manager 部署，请使用 Azure 门户版本。

### <a name="using-windows-server-and-windows-computers-in-azure-portal"></a>在 Azure 门户中使用 Windows Server 和 Windows 计算机

- [备份 Windows Server 和 Windows 客户端计算机](../backup/backup-configure-vault.md)
- [还原 Windows Server 和 Windows 客户端计算机](../backup/backup-azure-restore-windows-server.md)
- [管理 Windows Server 和 Windows 客户端计算机](../backup/backup-azure-manage-windows-server.md)
- [使用 PowerShell 备份 Windows Server](../backup/backup-client-automation.md)

### <a name="using-windows-server-and-windows-computers-in-classic-portal"></a>在经典门户中使用 Windows Server 和 Windows 计算机

- [备份 Windows Server 和 Windows 客户端计算机](../backup/backup-configure-vault-classic.md)
- [还原 Windows Server 和 Windows 客户端计算机](../backup/backup-azure-restore-windows-server-classic.md)
- [管理 Windows Server 和 Windows 客户端计算机](../backup/backup-azure-manage-windows-server-classic.md)
- [使用 PowerShell 备份 Windows Server](../backup/backup-client-automation-classic.md)

### <a name="using-virtual-machines-in-azure-portal"></a>在 Azure 门户中使用虚拟机

- [准备虚拟机环境](../backup/backup-azure-arm-vms-prepare.md)
- [备份虚拟机](../backup/backup-azure-vms-first-look-arm.md)
- [恢复虚拟机](../backup/backup-azure-arm-restore-vms.md)
- [管理虚拟机](../backup/backup-azure-manage-vms.md)
- [使用 PowerShell 备份虚拟机](../backup/backup-azure-vms-automation.md)

### <a name="using-virtual-machines-in-classic-portal"></a>在经典门户中使用虚拟机

- [准备虚拟机环境](../backup/backup-azure-vms-prepare.md)
- [备份虚拟机](../backup/backup-azure-vms-first-look.md)
- [恢复虚拟机](../backup/backup-azure-restore-vms.md)
- [管理虚拟机](../backup/backup-azure-manage-vms-classic.md)
- [使用 PowerShell 备份虚拟机](../backup/backup-azure-vms-classic-automation.md)

### <a name="using-system-center-data-protection-manager-in-azure-portal"></a>在 Azure 门户中使用 System Center Data Protection Manager

- [备份 System Center Data Protection Manager](../backup/backup-azure-dpm-introduction.md)

### <a name="using-system-center-data-protection-manager-in-classic-portal"></a>在经典门户中使用 System Center Data Protection Manager

- [备份 System Center Data Protection Manager](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="using-azure-backup-server-in-azure-portal"></a>在 Azure 门户中使用 Azure 备份服务器

Azure 备份服务器是一个 Azure 备份组件，其功能类似于 System Center Data Protection Manager (DPM)，不同之处在于 Azure 备份服务器不能将数据保存到磁带。 Azure 备份服务器可以从单个控制台将应用程序工作负荷保护到云，例如 Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange 和 Windows 客户端。 Azure 备份服务器不需要 System Center 许可证。

- [Azure 备份服务器](../backup/backup-azure-microsoft-azure-backup.md)

### <a name="using-azure-backup-server-in-classic-portal"></a>在经典门户中使用 Azure 备份服务器

- [Azure 备份服务器](../backup/backup-azure-microsoft-azure-backup-classic.md)


## <a name="next-steps"></a>后续步骤

如果不确定从何处着手，可先阅读[使用经典部署模型将 Windows Server 或客户端备份到 Azure](../backup/backup-configure-vault-classic.md) 一文。 本教程介绍在 Windows Server 或计算机上设置备份项目的步骤。

如果已经知道可以使用 Azure 备份，但想要了解成本，请参阅[备份定价](http://azure.microsoft.com/pricing/details/backup/)页。 该页面提供的常见问题解答列表可提供有用信息。 另请注意，“区域”下拉菜单中有两个 Azure 政府版区域。



<!--HONumber=Jan17_HO1-->


