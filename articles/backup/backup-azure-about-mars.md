---
title: 关于 MARS 代理
description: 了解 MARS 代理如何支持备份方案
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 8e4ace0c17dbe75e989981db56583ed9477b3716
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87562593"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>关于 Microsoft Azure 恢复服务 (MARS) 代理

本文介绍 Azure 备份服务如何使用 Microsoft Azure 恢复服务 (MARS) 代理将本地计算机上的文件、文件夹和卷或系统状态备份和还原到 Azure。

## <a name="backup-scenarios"></a>备份方案

MARS 代理支持以下备份方案：

![MARS 备份方案](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **文件和文件夹**：选择性地保护 Windows 文件和文件夹。
- **卷级别**：保护计算机的整个 Windows 卷。
- **系统级别**：保护整个 Windows 系统状态。

### <a name="additional-scenarios"></a>其他方案

- **备份 Azure 虚拟机中的特定文件和文件夹**：备份 Azure 虚拟机 (VM) 的主要方法是在 VM 上使用 Azure 备份扩展。 该扩展可备份整个 VM。 若要备份 VM 中的特定文件和文件夹，可在 Azure VM 中安装 MARS 代理。 有关详细信息，请参阅[体系结构：内置 Azure VM 备份](./backup-architecture.md#architecture-built-in-azure-vm-backup)。

- **脱机种子设定**：最初在 Azure 中创建完整数据备份时，通常会传输大量的数据，并会占用更多的网络带宽。 后续的备份只会传输差异数量（增量）的数据。 Azure 备份可压缩初始备份。 通过脱机种子设定过程，Azure 备份可以使用磁盘将压缩后的初始备份数据脱机上传到 Azure。** 有关详细信息，请参阅使用 [Azure Data Box 对 Azure 备份进行脱机备份](offline-backup-azure-data-box.md)。

## <a name="restore-scenarios"></a>还原方案

MARS 代理支持以下还原方案：

![MARS 恢复方案](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **同一服务器**：最初在其上创建备份的服务器。
  - **文件和文件夹**：选择要还原的各个文件和文件夹。
  - **卷级别**：选择要还原的卷和恢复点。 然后将其还原到同一台计算机上的同一位置或其他位置。  创建现有文件的副本、覆盖现有文件，或跳过现有文件的恢复。
  - **系统级别**：选择要还原到同一计算机上的指定位置的系统状态和恢复点。

- **备用服务器**：在其上创建备份的服务器以外的服务器。
  - **文件和文件夹**：选择要将其恢复点还原到目标计算机的各个文件和文件夹。
  - **卷级别**：选择要还原到另一位置的卷和恢复点。 创建现有文件的副本、覆盖现有文件，或跳过现有文件的恢复。
  - **系统级别**：选择要作为系统状态文件还原到备用计算机的系统状态和恢复点。

## <a name="backup-process"></a>备份过程

1. 在 Azure 门户中创建[恢复服务保管库](install-mars-agent.md#create-a-recovery-services-vault)，并从“备份目标”中选择文件、文件夹和系统状态****。
2. [将恢复服务保管库凭据和代理安装程序下载](./install-mars-agent.md#download-the-mars-agent)到本地计算机。

3. [安装代理](./install-mars-agent.md#install-and-register-the-agent)并使用下载的保管库凭据将计算机注册到恢复服务保管库。
4. 从客户端上的代理控制台中，[配置备份](./backup-windows-with-mars-agent.md#create-a-backup-policy)，指定要备份的内容、何时备份（计划）、备份应在 Azure 中保留多长时间（保留策略）并开始保护。

![Azure 备份代理示意图](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>其他信息

- “初始备份”（首个备份）根据备份设置运行****。  MARS 代理使用 VSS 来创建选择进行备份的卷的时间点快照。 代理仅使用 Windows 系统写入器操作来捕获快照。 不使用任何应用程序 VSS 写入器，并且不会捕获应用一致性快照。 使用 VSS 创建快照后，MARS 代理将在配置备份时指定的缓存文件夹中创建一个虚拟硬盘 (VHD)。 该代理还会存储每个数据块的校验和。

- “增量备份”（后续备份）根据指定的计划运行****。 在增量备份期间，将会标识已更改的文件，并创建新的 VHD。 该 VHD 经过压缩和加密，然后发送到保管库。 增量备份完成后，新 VHD 将与初始复制后创建的 VHD 合并。 此合并的 VHD 提供最新状态，用于对现行备份进行比较。

- MARS 代理可以使用 USN（更新序列号）变更日志在优化模式下运行备份作业，也可以通过扫描整个卷来检查目录或文件的更改，在未优化模式下运行备份作业**** ****。 未优化模式的速度较慢，因为代理必须扫描卷上的每个文件，并与元数据进行比较以确定更改的文件。  “初始备份”始终在未优化模式下运行****。 如果上一个备份失败，则下一个计划的备份作业将在未优化模式下运行。 若要详细了解这些模式及其验证方法，请参阅[这篇文章](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode)。

## <a name="next-steps"></a>后续步骤

[MARS 代理支持矩阵](./backup-support-matrix-mars-agent.md)

[MARS 代理常见问题解答](./backup-azure-file-folder-backup-faq.md)
