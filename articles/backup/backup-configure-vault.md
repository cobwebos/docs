---
title: 备份 Windows 计算机与 Azure 备份 MARS 代理
description: 使用 Azure 备份 Microsoft 恢复服务 (MARS) 代理备份 Windows 计算机。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: 7a1bd6da68b49481429709c7e4fd37dd5c07ae2c
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200780"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>备份 Windows 计算机与 Azure 备份 MARS 代理

本文介绍如何备份使用的 Windows 计算机[Azure 备份](backup-overview.md)服务和 Microsoft Azure 恢复服务 (MARS) 代理，也称为 Azure 备份代理。

本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 验证先决条件，并创建恢复服务保管库。
> * 下载和设置 MARS 代理
> * 创建备份策略和计划。
> * 执行即席备份。

## <a name="about-the-mars-agent"></a>关于 MARS 代理

MARS 代理由 Azure 备份用于将从本地计算机和 Azure Vm 文件、 文件夹和系统状态备份到 Azure 的备份恢复服务保管库。 您可以按如下所示运行代理：

- 直接在本地 Windows 计算机上运行代理，以便它们可以直接备份到 Azure 的备份恢复服务保管库。
- 运行代理的 Azure Vm 运行 Windows （--并行与 Azure VM 备份扩展） 的特定文件和文件夹备份在 VM 上。
- 在 Microsoft Azure 备份服务器 (MABS) 或 System Center Data Protection Manager (DPM) 服务器上运行该代理。 在此方案中，计算机和工作负荷备份到 MABS/DPM，然后 MABS/DPM 备份到 Azure 中使用 MARS 代理的保管库。
可备份的内容取决于该代理的安装位置。

> [!NOTE]
> Azure Vm 备份的主要方法是在 VM 上使用 Azure 备份扩展。 这将备份整个 VM。 你可能想要安装并使用与扩展的 MARS 代理，如果你想要在 VM 上将特定文件和文件夹。 [了解详细信息](backup-architecture.md#architecture-direct-backup-of-azure-vms)。

![备份过程的步骤](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>开始之前

- [了解如何](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)Azure 备份可与 MARS 代理的 Windows 计算机进行备份。
- [了解有关](backup-architecture.md#architecture-back-up-to-dpmmabs)辅助 MABS 或 DPM 服务器上运行的 MARS 代理的备份体系结构。
- [查看](backup-support-matrix-mars-agent.md)什么可以支持使用 MARS 代理且支持的功能。
- 验证想要备份的计算机上的 internet 访问权限。
- 要将服务器或客户端备份到 Azure，需要一个 Azure 帐户。 如果没有帐户，只需几分钟的时间就能创建一个[免费帐户](https://azure.microsoft.com/free/)。

### <a name="verify-internet-access"></a>验证 Internet 访问

如果你的计算机具有有限的 internet 访问权限，请确保代理在计算机上的防火墙设置允许这些 Url 和 IP 地址：

**URLs**

- www\.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

IP 地址

- 20.190.128.0/18
- 40.126.0.0/18


## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库存储所有备份和恢复点创建随着时间推移，并包含应用于备份的计算机的备份策略。 按如下所述创建保管库：

1. 使用 Azure 订阅登录到 [Azure 门户](https://portal.azure.com/)。
2. 在搜索中，键入**恢复服务**然后单击**恢复服务保管库**。

    ![创建恢复服务保管库步骤 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png)

3. 上**恢复服务保管库**菜单上，单击 **+ 添加**。

    ![创建恢复服务保管库步骤 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. 对于“名称”，请输入一个友好名称以标识保管库 。

   - 名称对于 Azure 订阅需要是唯一的。
   - 该名称可以包含 2 到 50 个字符。
   - 名称必须以字母开头，只能包含字母、数字和连字符。

5. 选择 Azure 订阅、 资源组和将在其中创建保管库的地理区域。 备份数据发送到保管库。 然后单击“创建”。

    ![创建恢复服务保管库步骤 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   - 创建保管库可能需要一段时间。
   - 可以在门户的右上区域中监视状态通知。 如果在几分钟后看不到保管库，请单击**刷新**。

     ![单击“刷新”按钮](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>设置存储冗余

Azure 备份会自动处理存储在保管库。 需要指定如何复制该存储。

1. 在“恢复服务保管库”边栏选项卡中，单击新保管库。 下**设置**部分中，单击**属性**。
2. 在中**属性**下**备份配置**，单击**更新**。

3. 选择存储复制类型，然后单击**保存**。

      ![设置新保管库的存储配置](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

- 我们建议，如果你使用 Azure 作为主要备份存储终结点，将继续使用默认**异地冗余**设置。
- 如果不使用 Azure 作为主要的备份存储终结点，则请选择“本地冗余”，减少 Azure 存储费用。
- 详细了解如何[异地](../storage/common/storage-redundancy-grs.md)并[本地](../storage/common/storage-redundancy-lrs.md)冗余。

## <a name="download-the-mars-agent"></a>下载 MARS 代理

下载适用于在你想要备份的计算机上安装的 MARS 代理。

- 如果您在任何计算机上已安装代理，请确保你运行的最新版本。
- 最新版本是可在门户中，或使用[直接下载](https://aka.ms/azurebackup_agent)

1. 在保管库下**Getting Started**，单击**备份**。

    ![打开备份目标边栏选项卡](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. 在中**工作负荷的运行位置？**，选择**本地**。 即使你想要在 Azure VM 上安装 MARS 代理，应选择此选项。
3. 在中**做您要备份？**，选择**文件和文件夹**和/或**系统状态**。 有一些其他选项可用，但仅支持这些如果运行辅助备份服务器。 单击**准备基础结构**。

      ![配置文件和文件夹](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. 上**准备基础结构**下**安装恢复服务代理**，下载 MARS 代理。

    ![准备基础结构](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. 在下载弹出窗口中单击“保存”。 默认情况下，**MARSagentinstaller.exe** 文件将保存到 Downloads 文件夹。

6. 现在，检查**已下载或使用最新的恢复服务代理**，然后下载保管库凭据。

    ![下载保管库凭据](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. 单击“ **保存**”。 此文件下载到下载文件夹中。 无法打开保管库凭据文件。

## <a name="install-and-register-the-agent"></a>安装并注册代理

1. 运行**MARSagentinstaller.exe**你想要备份的计算机上的文件。
2. 在 MARS 代理安装向导 >**安装设置**，指定你想要安装代理和一个位置以供缓存使用。 然后单击“下一步”。
   - Azure 备份使用缓存之前将它们发送到 Azure 存储数据快照。
   - 缓存位置应具有数据将备份的大小等于至少 5%的可用的空间。

     ![MARS 向导安装设置](./media/backup-configure-vault/mars1.png)

2. 在中**代理配置**，指定如何在 Windows 计算机上运行的代理连接到 internet。 然后单击“下一步”。

   - 如果您使用的自定义代理指定的代理设置和必要的凭据。
   - 请记住，代理需要有权[这些 Url](#verify-internet-access)。

     ![MARS 向导 internet 访问权限](./media/backup-configure-vault/mars2.png)

3. 在中**安装**查看必备项检查，然后单击**安装**。
4. 安装代理后，单击**继续注册**。
5. 在中**注册服务器向导** > **保管库标识**，浏览并选择下载的凭据文件。 然后单击“下一步”。

    ![注册-保管库凭据](./media/backup-configure-vault/register1.png)

6. 在中**加密设置**，指定将用于加密和解密的计算机备份的通行短语。

    - 将加密通行短语保存在安全的位置。
    - 如果丢失或忘记了通行短语，Microsoft 无法帮助恢复备份数据。 请将文件保存在安全的位置。 您需要它来还原备份。

7. 单击**完成**。 现已安装代理，且已向保管库注册计算机。 接下来可以配置和计划备份。

## <a name="create-a-backup-policy"></a>创建备份策略

备份策略指定执行数据创建恢复点的快照的时间以及恢复点保留时长。

- 配置使用 MARS 代理的备份策略。
- Azure 备份不会自动考虑夏令时 (DST)。 这可能导致某些实际的时间和计划的备份时间之间的差异。

创建策略，如下所示：

1. 在每台计算机上打开 MARS 代理。 可以通过在计算机中搜索 **Microsoft Azure 备份**找到该代理。
2. 在中**操作**，单击**计划备份**。

    ![计划 Windows Server 备份](./media/backup-configure-vault/schedule-first-backup.png)

3. 在计划备份向导 >**入门**，单击**下一步**。
4. 在中**选择要备份的项**，单击**添加的项**。
5. 在中**选择的项**，选择你想要备份。 然后单击“确定”。
6. 在中**选择要备份的项**页上，单击**下一步**。
7. 在中**指定备份计划**页上，指定当您想要进行每日或每周备份。 然后单击“下一步”。

    - 执行备份时创建一个恢复点。
    - 在您的环境中创建的恢复点数量将取决于你的备份计划。

1. 您可以计划每日备份，最多一天三次。 例如，屏幕截图显示了两个每日备份，另一个在午夜在下午 6 点。

    ![每日计划](./media/backup-configure-vault/day-schedule.png)

9. 也可以运行每周备份。 例如，屏幕截图显示了在上午 9:30 和 1:00 AM 执行每个备用星期日和星期三的备份。

    ![每周日程安排](./media/backup-configure-vault/week-schedule.png)

8. 上**选择保留策略**页上，指定如何存储数据的历史记录副本。 然后单击“下一步”。

   - 保留设置指定应存储的恢复点，并且它们的存储的时间长度。
   - 例如，设置每日保留期设置，则指示，指定对于日保留期的时间，最新恢复点将保留指定的天数。 或者，作为另一个示例中，您可以指定每月的保留策略，以指示应 12 个月内存储上每个月 30 日创建的恢复点。
   - 每日和每周恢复点保留期通常与备份计划。 这意味着根据计划触发备份时，将创建的备份的恢复点存储持续时间内的每日或每周保留策略中所示。
   - 例如，以下屏幕截图中：
     - 午夜和下午 6 点的每日备份保留七天。
     - 在星期六午夜和下午 6 点创建的备份保留 4 周。
     - 创建在星期六午夜和下午 6 点月份的最后一周的备份保留 12 个月。 -在星期六年 3 月的最后一周中创建的备份保留 10 年之久。

   ![保留示例](./media/backup-configure-vault/retention-example.png)

11. 在中**选择初始备份类型**指定如何采用初始备份，通过网络或脱机。 然后单击“下一步”。

10. 在中**确认**，查看的信息，然后单击**完成**。
11. 在向导完成创建备份计划后，请单击“**关闭**”。

### <a name="perform-the-initial-backup-offline"></a>执行脱机初始备份

您可以运行初始备份自动通过网络或脱机。 初始备份的脱机种子设定是如果您有大量将需要大量网络带宽传输的数据。 按如下所示执行脱机传输操作：

1. 备份数据写入暂存位置。
2. AzureOfflineBackupDiskPrep 工具用于将数据从临时位置复制到一个或多个 SATA 磁盘。
3. 该工具创建 Azure 导入作业。 [了解详细信息](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)有关 Azure 导入和导出。
4. 将 SATA 磁盘发送到 Azure 数据中心。
5. 在数据中心，磁盘数据复制到 Azure 存储帐户。
6. Azure 备份将数据从存储帐户复制到保管库，并计划增量备份。

[了解详细信息](backup-azure-backup-import-export.md)有关脱机种子设定。

### <a name="enable-network-throttling"></a>启用网络限制

您可以控制网络带宽通过启用网络限制的使用 MARS 代理。 限制是有帮助，如果您需要在工作时间内备份数据，但想要控制带宽用于备份和还原活动。

- Azure 备份网络限制使用[服务质量 (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top)本地操作系统上。
- 及更高版本，Windows Server 2008 R2 和 Windows 7 及更高版本上提供了网络限制进行备份。 操作系统应运行最新 service pack。

启用网络限制，如下所示：

1. 在 MARS 代理中，单击**更改属性**。
2. 上**限制**选项卡上，选中**为备份操作启用 internet 带宽使用限制**。

    ![网络限制](./media/backup-configure-vault/throttling-dialog.png)
3. 指定允许的带宽在工作和工作时间之外。 带宽值从 512 Kbps 开始，请转到 1023 MBps。 然后单击“确定”。

## <a name="run-an-ad-hoc-backup"></a>运行即席备份

1. 在 MARS 代理中，单击**立即备份**。 这将启动初始复制在网络上。

    ![立即备份 Windows Server](./media/backup-configure-vault/backup-now.png)

2. 在中**确认**，查看设置，然后单击**备份**。
3. 单击“**关闭**”以关闭向导。 如果在备份完成之前执行此操作，向导将继续在后台运行。

完成初始备份后，备份控制台中会显示“**作业已完成**”状态。

## <a name="next-steps"></a>后续步骤

[了解如何](backup-azure-restore-windows-server.md)还原文件。
