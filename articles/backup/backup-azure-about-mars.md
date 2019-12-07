---
title: 关于 MARS 代理
description: 了解 MARS 代理如何支持备份方案
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c036d93c09195c0c330cfe86f307d28866131d9f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897320"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>关于 Microsoft Azure 恢复服务（MARS）代理

本文介绍 Azure 备份服务如何使用 Microsoft Azure 恢复服务（MARS）代理从本地计算机备份和还原文件/文件夹、卷或系统状态。

MARS 代理支持以下备份方案：

![恢复服务保管库仪表板](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **文件和文件夹**：有选择地保护 Windows 文件和文件夹。
- **卷级别**：保护计算机的整个 Windows 卷。
- **系统级别**：保护整个 Windows 系统状态。

MARS 代理支持以下还原方案：

![恢复服务保管库仪表板](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **同一服务器**：最初在其上创建备份的同一服务器。
    -    **文件和文件夹**：可以浏览并选择要还原的各个文件和文件夹。
    -    **卷级**：你可以选择要还原的卷和恢复点，并将其还原到同一计算机上的同一位置或备用位置。  你可以创建现有文件的副本，覆盖现有文件，或者跳过恢复现有文件。
    -    **系统级别**：可以选择 "系统状态" 和 "恢复点" 以还原到指定位置的同一台计算机。


-   **备用服务器**：另一个服务器，即不是在其中创建备份的同一服务器。
    -    **文件和文件夹**：可以浏览并选择要将恢复点还原到目标计算机的各个文件和文件夹。
    -    **卷级别**：通过创建现有文件的副本、覆盖现有文件或跳过恢复现有文件，你可以选择要还原到备用位置的卷和恢复点。
    -    **系统级别**：可以选择 "系统状态" 和 "恢复点" 以将系统状态文件还原到备用计算机。

## <a name="backup-process"></a>备份过程

1.  在 Azure 门户中，创建[恢复服务保管库](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault)，并选择 "文件和文件夹" 和/或 "来自备份目标的系统状态"。
2.  将恢复服务保管库凭据和代理安装程序[下载](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent)到本地计算机。 若要通过选择备份选项来保护本地计算机，请选择 "文件和文件夹和系统状态"，然后下载 MARS 代理。
3.  准备基础结构：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。    运行安装程序以[安装](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent)代理。

    b.  使用下载的保管库凭据将计算机注册到恢复服务保管库。
4.  在客户端上的代理控制台中，使用 "[计划备份](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy)" 来配置备份。 指定备份数据的保留策略并开始保护。

![恢复服务保管库仪表板](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>其他方案
-   **备份 azure 虚拟机中的特定文件和文件夹**-备份 azure 虚拟机（vm）的主要方法是在 VM 上使用 azure 备份扩展。 这将备份整个 VM。 如果要备份 VM 内的特定文件和文件夹，则可在 Azure Vm 中安装 MARS 代理。 [了解详细信息](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)。

-   **脱机种子设定**-将数据备份到 Azure 的初始完整备份通常会传输大量数据，并且与仅传输增量/增量的后续备份相比，需要更多的网络带宽。 Azure 备份可压缩初始备份。 通过脱机种子设定，Azure 备份可以使用磁盘将压缩后的初始备份数据脱机上传到 Azure。 [了解详细信息](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)。


## <a name="next-steps"></a>后续步骤
[MARS 代理支持矩阵](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[常见问题-MARS 代理](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
