---
title: 通过 MARS 代理备份 Windows 计算机
description: 使用 Azure 备份 Microsoft 恢复服务（MARS）代理来备份 Windows 计算机。
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 990929cc95d1c25117873ca39415d33370456b91
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025531"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>使用 Azure 备份 MARS 代理备份 Windows 计算机

本文介绍如何使用[Azure 备份](backup-overview.md)服务和 MICROSOFT AZURE 恢复服务（MARS）代理（也称为 Azure 备份代理）来备份 Windows 计算机。

本文介绍如何执行以下操作：

> [!div class="checklist"]
>
> * 验证必备组件并创建恢复服务保管库。
> * 下载和设置 MARS 代理
> * 创建备份策略和计划。
> * 执行按需备份。

## <a name="about-the-mars-agent"></a>关于 MARS 代理

Azure 备份使用 MARS 代理将文件、文件夹和系统状态从本地计算机和 Azure Vm 备份到 Azure 中的备份恢复服务保管库。 可以按如下所示运行代理：

* 直接在本地 Windows 计算机上运行代理，以便它们可以直接备份到 Azure 中的备份恢复服务保管库。
* 在运行 Windows （与 Azure VM 备份扩展并行）的 Azure Vm 上运行代理，以备份 VM 上的特定文件和文件夹。
* 在 Microsoft Azure 备份服务器 (MABS) 或 System Center Data Protection Manager (DPM) 服务器上运行该代理。 在此方案中，计算机和工作负荷备份到 MABS/DPM，然后 MABS/DPM 使用 MARS 代理备份到 Azure 中的保管库。
可备份的内容取决于该代理的安装位置。

> [!NOTE]
> 备份 Azure Vm 的主要方法是在 VM 上使用 Azure 备份扩展。 这将备份整个 VM。 如果要备份 VM 上的特定文件和文件夹，你可能需要安装并使用 MARS 代理和扩展。 [了解详细信息](backup-architecture.md#architecture-built-in-azure-vm-backup)。

![备份过程的步骤](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>开始之前

* [了解如何](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)Azure 备份通过 MARS 代理备份 Windows 计算机。
* [了解](backup-architecture.md#architecture-back-up-to-dpmmabs)在辅助 MABS 或 DPM 服务器上运行 MARS 代理的备份体系结构。
* [查看](backup-support-matrix-mars-agent.md)受支持的内容以及可通过 MARS 代理进行备份的内容。
* 验证要备份的计算机上的 internet 访问权限。
* 要将服务器或客户端备份到 Azure，需要一个 Azure 帐户。 如果没有帐户，只需几分钟的时间就能创建一个[免费帐户](https://azure.microsoft.com/free/)。

### <a name="verify-internet-access"></a>验证 Internet 访问

如果计算机具有有限的 internet 访问权限，请确保计算机或代理上的防火墙设置允许这些 Url 和 IP 地址：

#### <a name="urls"></a>URL

* www\.msftncsi.com
* *.Microsoft.com
* *.WindowsAzure.com
* *.microsoftonline.com
* *.windows.net

#### <a name="ip-addresses"></a>IP 地址

* 20.190.128.0/18
* 40.126.0.0/18

对上面列出的所有 Url 和 IP 地址的访问都使用端口443上的 HTTPS 协议。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库存储了一段时间内创建的所有备份和恢复点，并包含应用于备份的计算机的备份策略。 按如下所述创建保管库：

1. 使用 Azure 订阅登录到 [Azure 门户](https://portal.azure.com/)。

2. 搜索并选择“恢复服务保管库”。

    ![创建恢复服务保管库步骤 1](./media/backup-configure-vault/open-recovery-services-vaults.png)

3. 在 "**恢复服务保管库**" 菜单上，单击 " **+ 添加**"。

    ![创建恢复服务保管库步骤 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. 对于“名称”，请输入一个友好名称以标识保管库。

   * 名称对于 Azure 订阅需要是唯一的。
   * 该名称可以包含 2 到 50 个字符。
   * 名称必须以字母开头，只能包含字母、数字和连字符。

5. 选择应在其中创建保管库的 Azure 订阅、资源组和地理区域。 将备份数据发送到保管库。 然后单击“创建”。

    ![创建恢复服务保管库步骤 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   * 创建保管库可能需要一段时间。
   * 可以在门户的右上区域中监视状态通知。 如果在几分钟后看不到保管库，请单击 "**刷新**"。

     ![单击“刷新”按钮](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>设置存储冗余

Azure 备份会自动处理保管库的存储。 你需要指定如何复制该存储。

1. 在“恢复服务保管库”边栏选项卡中，单击新保管库。 在 "**设置**" 部分下，单击 "**属性**"。
2. 在 "**属性**" 中的 "**备份配置**" 下，单击 "**更新**"。

3. 选择存储复制类型，然后单击 "**保存**"。

      ![设置新保管库的存储配置](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

* 如果你使用 Azure 作为主要的备份存储终结点，则建议继续使用默认**异地冗余**设置。
* 如果不使用 Azure 作为主要的备份存储终结点，则请选择“本地冗余”，减少 Azure 存储费用。
* 了解有关[地域](../storage/common/storage-redundancy-grs.md)和[本地](../storage/common/storage-redundancy-lrs.md)冗余的详细信息。

## <a name="download-the-mars-agent"></a>下载 MARS 代理

在要备份的计算机上下载用于安装的 MARS 代理。

* 如果已在任何计算机上安装了代理，请确保运行的是最新版本。
* 最新版本可在门户中找到或使用[直接下载](https://aka.ms/azurebackup_agent)

1. 在保管库中的 "**入门**下，单击"**备份**"。

    ![打开备份目标边栏选项卡](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. 在**工作负荷的运行位置**，选择 **"本地**"。 即使要在 Azure VM 上安装 MARS 代理，也应选择此选项。
3. 在 **"要备份的内容"** 中，选择 "**文件和文件夹**" 和/或 "**系统状态**"。 还有很多其他选项可用，但只有在运行辅助备份服务器时才支持这些选项。 单击 "**准备基础结构**"。

      ![配置文件和文件夹](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. 在 "**准备基础结构**" 的 "**安装恢复服务代理**" 下，下载 MARS 代理。

    ![准备基础结构](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. 在下载弹出窗口中单击“保存”。 默认情况下，**MARSagentinstaller.exe** 文件将保存到 Downloads 文件夹。

6. 现在，请检查**已下载或正在使用最新的恢复服务代理**，然后下载保管库凭据。

    ![下载保管库凭据](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. 单击“ **保存**”。 文件将下载到下载文件夹。 不能打开保管库凭据文件。

## <a name="install-and-register-the-agent"></a>安装并注册代理

1. 在要备份的计算机上运行**marsagentinstaller.exe**文件。
2. 在 MARS 代理安装向导中 >**安装设置**，指定要安装代理的位置以及要用于缓存的位置。 然后单击“下一步”。
   * Azure 备份使用缓存来存储数据快照，然后将其发送到 Azure。
   * 缓存位置的可用空间应等于要备份的数据大小的至少5%。

    ![MARS 向导安装设置](./media/backup-configure-vault/mars1.png)

3. 在 "**代理配置**" 中，指定在 Windows 计算机上运行的代理将如何连接到 internet。 然后单击“下一步”。

   * 如果使用自定义代理，请指定代理设置，并根据需要指定凭据。
   * 请记住，代理需要访问[这些 url](#verify-internet-access)。

     ![MARS 向导 internet 访问](./media/backup-configure-vault/mars2.png)

4. 在 "**安装**" 中，查看必备项检查，然后单击 "**安装**"。
5. 安装代理后，单击 "**继续注册**"。
6. 在 "**注册服务器" 向导** > **保管库标识**"中，浏览并选择已下载的凭据文件。 然后单击“下一步”。

    ![注册-保管库凭据](./media/backup-configure-vault/register1.png)

7. 在 "**加密设置**" 中，指定将用于对计算机的备份进行加密和解密的通行短语。

    * 将加密通行短语保存在安全的位置。
    * 如果丢失或忘记了通行短语，Microsoft 无法帮助你恢复备份数据。 请将文件保存在安全的位置。 你需要它来还原备份。

8. 单击“完成”。 现已安装代理，且已向保管库注册计算机。 接下来可以配置和计划备份。

## <a name="create-a-backup-policy"></a>创建备份策略

备份策略指定何时拍摄数据快照以创建恢复点，以及恢复点保留的时间。

* 使用 MARS 代理配置备份策略。
* Azure 备份不会自动考虑夏令时（DST）。 这可能会导致实际时间和计划的备份时间发生某些差异。

创建策略，如下所示：
1. 下载并注册 MARS 代理后，启动代理控制台。 可以通过在计算机中搜索 **Microsoft Azure 备份**找到该代理。  
2. 在 "**操作**" 中，单击 "**计划备份**"。

    ![计划 Windows Server 备份](./media/backup-configure-vault/schedule-first-backup.png)
3. 在计划备份向导中 >**开始**，单击 "**下一步**"。
4. 在 "**选择要备份的项**" 中，单击 "**添加项**"。

    ![选择要备份的项](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. 在 "**选择项目**" 中，选择要备份的内容，然后单击 **"确定"** 。

    ![要备份的选定项](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. 在 "**选择要备份的项**" 页上，单击 "**下一步**"。
7. 在 "**指定备份计划**" 页上，指定要进行每日或每周备份的时间。 然后单击“下一步”。

    - 执行备份时，将创建恢复点。
    - 你的环境中创建的恢复点的数量取决于你的备份计划。


8. 你可以计划每日备份，一天最多三次。 例如，屏幕快照显示两个每日备份，一个为午夜，另一个在下午6:00。

    ![每日计划](./media/backup-configure-vault/day-schedule.png)


9. 还可以运行每周备份。 例如，屏幕截图显示每个备用星期日 & 星期三 9:30 AM 到 1:00 AM 创建的备份。

    ![每周日程安排](./media/backup-configure-vault/week-schedule.png)


10. 在 "**选择保留策略**" 页上，指定存储数据的历史副本的方式。 然后单击“下一步”。

    - 保留设置指定应存储的恢复点，以及存储这些恢复点的时间长度。
    - 例如，当你设置每日保留设置时，你指示在为每日保留指定的时间，将在指定的天数内保留最新的恢复点。 或者，您也可以指定每月保留策略，以指示每月30日创建的恢复点应存储12个月。
    - 每日和每周恢复点保留期通常与备份计划一致。 这意味着，根据计划触发备份时，将在每日或每周保留策略中指定的持续时间内存储备份创建的恢复点。
    - 例如，在以下屏幕截图中：

        -   每日午夜备份和 6:00 PM 保留7天。
        -   在星期六午夜和 6:00 PM 创建的备份将保留四周。
        -   在每月的第一周的午夜和 6:00 PM 进行的备份保留12个月。
        -   在三月份的最后一周在星期六进行的备份保留10年。

        ![保留示例](./media/backup-configure-vault/retention-example.png)


11. 在 "**选择初始备份类型**" 中，决定是要通过网络进行初始备份还是使用脱机备份（有关脱机备份的详细信息，请参阅此[文](offline-backup-azure-data-box.md)）。 若要通过网络进行初始备份，请选择 "**自动通过网络**"，然后单击 "**下一步**"。

    ![初始备份类型](./media/backup-azure-manage-mars/choose-initial-backup-type.png)


12. 在 "**确认**" 中，查看信息，然后单击 "**完成**"。

    ![确认备份类型](./media/backup-azure-manage-mars/confirm-backup-type.png)


13. 在向导完成创建备份计划后，请单击“**关闭**”。

    ![确认修改备份过程](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

你必须在安装了代理的每台计算机上创建一个策略。

### <a name="perform-the-initial-backup-offline"></a>脱机执行初始备份

可以通过网络自动运行初始备份，也可以脱机运行。 如果有大量数据需要传输大量的网络带宽，则初始备份的脱机种子设定会很有用。 执行脱机传输的方式如下：

1. 将备份数据写入暂存位置。
2. 使用 AzureOfflineBackupDiskPrep 工具将数据从暂存位置复制到一个或多个 SATA 磁盘。
3. 该工具会创建 Azure 导入作业。 [详细了解](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)Azure 导入和导出。
4. 将 SATA 磁盘发送到 Azure 数据中心。
5. 在数据中心，磁盘数据将复制到 Azure 存储帐户。
6. Azure 备份将数据从存储帐户复制到保管库，并计划增量备份。

[详细了解](offline-backup-azure-data-box.md)脱机种子设定。

### <a name="enable-network-throttling"></a>启用网络限制

可以通过启用网络限制来控制 MARS 代理使用网络带宽的方式。 如果需要在工作时间内备份数据，但想要控制在备份和还原活动中使用的带宽量，则限制会很有帮助。

* Azure 备份网络限制在本地操作系统上使用[服务质量（QoS）](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) 。
* Windows Server 2012 和更高版本上都提供了用于备份的网络限制。 操作系统应运行最新的 service pack。

启用网络限制，如下所示：

1. 在 MARS 代理中，单击 "**更改属性**"。
2. 在 "**限制**" 选项卡上，选中 "**为备份操作启用 internet 带宽使用限制**"。

    ![网络限制](./media/backup-configure-vault/throttling-dialog.png)
3. 指定工作时间和工作时间之外的允许带宽。 带宽值从 512 Kbps 开始，最高可达 1023 MBps。 然后单击“确定”。

## <a name="run-an-on-demand-backup"></a>运行按需备份

1. 在 MARS 代理中，单击 "**立即备份**"。

    ![立即备份 Windows Server](./media/backup-configure-vault/backup-now.png)

2. 如果 MARS 代理版本为2.0.9169.0 或更高版本，则可以设置自定义保留。 在 "**保留备份截止**时间" 部分中，从显示的日历中选择一个日期：

   ![保留备份日历](./media/backup-configure-vault/mars-ondemand.png)

3. 在 "**确认**" 中查看设置，然后单击 "**备份**"。
4. 单击“**关闭**”以关闭向导。 如果在备份完成之前执行此操作，向导将继续在后台运行。
5. 完成初始备份后，备份控制台中会显示“**作业已完成**”状态。

## <a name="on-demand-backup-policy-retention-behavior"></a>按需备份策略保留行为

>[!NOTE]
>仅适用于早于2.0.9169.0 的 MARS 代理版本
>

* 有关详细信息，请参阅[创建备份策略](backup-configure-vault.md#create-a-backup-policy)的步骤8

| 备份计划选项 | 已备份的数据将保留多长时间？
| -- | --
| 计划备份间隔： * 天 | **默认保留期**：等效于 "每天备份的保留天数" <br/><br/> **异常**：如果长期保留（周、月、年）的每日计划备份集失败，则在将此失败计划的备份视为长期保留后，将立即触发按需备份。 否则，下一次计划的备份将被视为长期保留。<br/><br/> **示例**：如果（假设）在星期四 8:00 am 执行的计划备份失败，且要考虑每周/每月/每年保留的同一备份，则在下一次计划的备份之前触发第一个按需备份（例如）星期五，8:00 am 将自动标记为每周/每月/每年保留，适用于星期四 8:00 am 备份。
| 计划备份间隔：每周 | **默认保留期**：1天 <br/> 对于每周备份策略的数据源所采用的按需备份将在下一天删除，即使它们是数据源的最新备份。 <br/><br/> **异常**：如果每周计划备份集的长期保留（周、月、年）失败，则在将此失败计划的备份视为长期保留后，将立即触发按需备份。 否则，下一次计划的备份将被视为长期保留。 <br/><br/> **示例**：如果（假设）在星期四 8:00 am 上执行的计划备份失败，且要考虑相同的备份以进行每月/每年保留，则在下一次计划的备份之前触发第一个按需备份（例如）周二，8:00 am 会自动标记为适用于星期四 8:00 am 备份的每月/每年保留

## <a name="next-steps"></a>后续步骤

[了解如何还原文件](backup-azure-restore-windows-server.md)。
