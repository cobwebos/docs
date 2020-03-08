---
title: 关于 MARS 代理
description: 了解 MARS 代理如何支持备份方案
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673290"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>关于 Microsoft Azure 恢复服务（MARS）代理

本文介绍 Azure 备份服务如何使用 Microsoft Azure 恢复服务（MARS）代理将本地计算机上的文件、文件夹和卷或系统状态备份和还原到 Azure。

MARS 代理支持以下备份方案：

![MARS 备份方案](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **文件和文件夹**：有选择地保护 Windows 文件和文件夹。
- **卷级别**：保护计算机的整个 Windows 卷。
- **系统级别**：保护整个 Windows 系统状态。

MARS 代理支持以下还原方案：

![MARS 恢复方案](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **同一服务器**：最初在其上创建备份的服务器。
  - **文件和文件夹**：选择要还原的单个文件和文件夹。
  - **卷级别**：选择要还原的卷和恢复点，然后将其还原到同一计算机上的同一位置或备用位置。  创建现有文件的副本，覆盖现有文件，或者跳过恢复现有文件。
  - **系统级别**：选择要还原到指定位置的同一计算机的系统状态和恢复点。

- **备用服务器**：一台服务器，而不是在其中创建备份的服务器。
  - **文件和文件夹**：选择要还原到目标计算机的恢复点所在的单个文件和文件夹。
  - **卷级别**：选择要还原到另一个位置的卷和恢复点。 创建现有文件的副本，覆盖现有文件，或者跳过恢复现有文件。
  - **系统级别**：选择要作为系统状态文件还原到备用计算机的系统状态和恢复点。

## <a name="backup-process"></a>备份过程

1. 在 Azure 门户中，创建[恢复服务保管库](install-mars-agent.md#create-a-recovery-services-vault)，并从备份目标中选择文件、文件夹和系统状态。
2. 将[恢复服务保管库凭据和代理安装程序下载](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent)到本地计算机。

    若要通过选择备份选项来保护本地计算机，请选择 "文件"、"文件夹" 和 "系统状态"，然后下载 MARS 代理。

3. 准备基础结构：

    a. 运行安装程序以[安装代理](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent)。

    b. 使用下载的保管库凭据将计算机注册到恢复服务保管库。
4. 在客户端上的代理控制台中[配置备份](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy)。 指定备份数据的保留策略，以开始保护该数据。

![Azure 备份代理关系图](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>其他方案

- **备份 azure 虚拟机中的特定文件和文件夹**：备份 azure 虚拟机（vm）的主要方法是在 VM 上使用 azure 备份扩展。 此扩展将备份整个 VM。 如果要备份 VM 内的特定文件和文件夹，可以在 Azure Vm 中安装 MARS 代理。 有关详细信息，请参阅[体系结构：内置 AZURE VM 备份](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)。

- **脱机播种**：对 Azure 进行的初始完整备份通常会传输大量数据，并且需要更多的网络带宽。 后续备份仅传输增量数据量或增量数据量。 Azure 备份可压缩初始备份。 通过*脱机种子设定*，Azure 备份可以使用磁盘将压缩后的初始备份数据脱机上传到 Azure。 有关详细信息，请参阅[Azure 备份使用 Azure Data Box 进行脱机备份](offline-backup-azure-data-box.md)。

## <a name="next-steps"></a>后续步骤

[MARS 代理支持矩阵](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[MARS 代理常见问题](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
