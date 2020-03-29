---
title: 关于 MARS 代理
description: 了解 MARS 代理如何支持备份方案
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673290"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>关于微软 Azure 恢复服务 （MARS） 代理

本文介绍 Azure 备份服务如何使用 Microsoft Azure 恢复服务 （MARS） 代理从本地计算机备份和还原文件、文件夹以及卷或系统状态到 Azure。

MARS 代理支持以下备份方案：

![MARS 备份方案](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **文件和文件夹**：有选择地保护 Windows 文件和文件夹。
- **卷级别**：保护计算机的整个 Windows 卷。
- **系统级别**：保护整个 Windows 系统状态。

MARS 代理支持以下还原方案：

![MARS 恢复方案](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **同一服务器**：最初创建备份的服务器。
  - **文件和文件夹**：选择要还原的单个文件和文件夹。
  - **音量级别**：选择要还原的卷和恢复点，然后将其还原到同一台计算机上的同一位置或备用位置。  创建现有文件的副本，覆盖现有文件，或跳过恢复现有文件。
  - **系统级别**：选择系统状态和恢复点以在指定位置还原到同一台计算机。

- **备用服务器**：执行备份的服务器以外的服务器。
  - **文件和文件夹**：选择要还原到目标计算机的恢复点的各个文件和文件夹。
  - **卷级别**：选择要还原到另一个位置的卷和恢复点。 创建现有文件的副本，覆盖现有文件，或跳过恢复现有文件。
  - **系统级别**：选择系统状态和恢复点以还原为系统状态文件到备用计算机。

## <a name="backup-process"></a>备份过程

1. 从 Azure 门户创建[恢复服务保管库](install-mars-agent.md#create-a-recovery-services-vault)，并从备份目标中选择文件、文件夹和系统状态。
2. [将恢复服务保管库凭据和代理安装程序下载](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent)到本地计算机。

    要通过选择"备份"选项保护本地计算机，请选择文件、文件夹和系统状态，然后下载 MARS 代理。

3. 准备基础结构：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 运行安装程序以[安装代理](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent)。

    b.保留“数据库类型”设置，即设置为“共享”。 使用下载的保管库凭据将计算机注册到恢复服务保管库。
4. 从客户端上的代理控制台配置[备份](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy)。 指定备份数据的保留策略以开始保护它。

![Azure 备份代理图](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>其他方案

- **备份 Azure 虚拟机中的特定文件和文件夹**：备份 Azure 虚拟机 （VM） 的主要方法是在 VM 上使用 Azure 备份扩展名。 扩展备份整个 VM。 如果要在 VM 中备份特定文件和文件夹，可以在 Azure VM 中安装 MARS 代理。 有关详细信息，请参阅[体系结构：内置 Azure VM 备份](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)。

- **脱机种子设定**：将数据的初始完整备份到 Azure 通常传输大量数据，并需要更多的网络带宽。 后续备份仅传输增量或增量数据量。 Azure 备份可压缩初始备份。 通过*脱机种子设定*过程，Azure 备份可以使用磁盘将压缩的初始备份数据脱机上载到 Azure。 有关详细信息，请参阅使用[Azure 数据框 进行 Azure 备份脱机备份](offline-backup-azure-data-box.md)。

## <a name="next-steps"></a>后续步骤

[MARS 代理支持矩阵](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[MARS 代理常见问题解答](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
