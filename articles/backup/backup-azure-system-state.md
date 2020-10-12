---
title: 将 Windows 系统状态备份到 Azure
description: 了解如何将 Windows Server 计算机的系统状态备份到 Azure。
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 1b3573d757d2f7b1ffec9ae718aa791488960f3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332877"
---
# <a name="back-up-windows-system-state-to-azure"></a>将 Windows 系统状态备份到 Azure

本文介绍了如何将 Windows Server 系统状态备份到 Azure。 它旨在引导完成基本操作。

如果想要深入了解 Azure 备份，请阅读此 [概述](backup-overview.md)。

如果还没有 Azure 订阅，可以先创建一个 [免费帐户](https://azure.microsoft.com/free/)，这样就可以访问任何 Azure 服务。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>为保管库设置存储冗余

在创建恢复服务保管库时，请确保存储冗余配置为所希望的方式。

1. 在“恢复服务保管库”窗格中，选择新保管库。

    ![在恢复服务保管库列表中选择新保管库](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    当你选择保管库时，" **恢复服务保管库** " 窗格会缩小，"设置" 窗格 (*其顶部有保管库的名称*) 并且保管库详细信息窗格将打开。

    ![查看新保管库的存储配置](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. 在新的保管库的 "设置" 窗格中，使用垂直滑动向下滚动到 "管理" 部分，并选择 " **备份基础结构**"。
    此时将打开 "备份基础结构" 窗格。
3. 在 "备份基础结构" 窗格中，选择 " **备份配置** "，打开 " **备份配置** " 窗格。

    ![设置新保管库的存储配置](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. 为保管库选择合适的存储复制选项。

    ![存储配置选择](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration-for-vault.png)

    默认情况下，保管库具有异地冗余存储。 如果使用 Azure 作为主要备份存储终结点，请继续使用“异地冗余”****。 如果不使用 Azure 作为主要备份存储终结点，则选择“本地冗余” ，以减少 Azure 存储成本。 在此[存储冗余概述](../storage/common/storage-redundancy.md)中了解有关[异地冗余](../storage/common/storage-redundancy.md#geo-redundant-storage)的[本地冗余](../storage/common/storage-redundancy.md#locally-redundant-storage)和[区域冗余](../storage/common/storage-redundancy.md#zone-redundant-storage)存储选项的详细信息。

创建保管库之后，即可对其进行配置，以便备份 Windows 系统状态。

## <a name="configure-the-vault"></a>配置保管库

1. 在 "恢复服务保管库" 窗格中 (刚刚创建的保管库) ，请在 "入门" 部分中选择 " **备份**"，然后在 " **入门备份** " 窗格中选择 " **备份目标**"。

    ![打开备份设置](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    此时将打开 " **备份目标** " 窗格。

    ![打开备份目标窗格](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. 从“工作负荷的运行位置?”下拉菜单中选择“本地” 。

    选择 **"本地"** ，因为 windows Server 或 windows 计算机是不在 Azure 中的物理计算机。

3. 从 " **要备份的内容"** 菜单中，选择 " **系统状态**"，然后选择 **"确定"**。

    ![配置文件和文件夹](./media/backup-azure-system-state/backup-goal-system-state.png)

    选择 **"确定"** 后，会在 " **备份目标**" 旁边显示一个复选标记，并会打开 " **准备基础结构** " 窗格。

    ![已配置备份目标，接下来准备基础结构](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. 在 " **准备基础结构** " 窗格中，选择 " **下载 Windows Server 或 windows 客户端的代理**"。

    ![准备基础结构](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    如果使用的是 Windows Server 必需的，请选择下载适用于 Windows Server 的代理。 弹出菜单会提示用户运行或保存 MARSAgentInstaller.exe。

    ![MARSAgentInstaller 对话框](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. 在 "下载" 弹出菜单中，选择 " **保存**"。

    默认情况下，**MARSagentinstaller.exe** 文件将保存到 Downloads 文件夹。 安装程序完成后，你将看到一个弹出窗口，询问你是要运行安装程序，还是打开该文件夹。

    ![MARS 安装程序已完成](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    此时还不需要安装代理。 下载保管库凭据后，可以安装代理。

6. 在 " **准备基础结构** " 窗格中，选择 " **下载**"。

    ![下载保管库凭据](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    保管库凭据下载到 **下载** 文件夹。 保管库凭据下载完成后，会显示一个弹出窗口，询问你是要打开还是要保存凭据。 选择“保存”。 如果意外选择了 " **打开**"，则让尝试打开保管库凭据的对话框失败。 你将无法打开保管库凭据。 继续执行下一步。 保管库凭据位于 " **下载** " 文件夹中。

    ![已下载保管库凭据](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > 保管库凭据必须仅保存到要在其上使用代理的 Windows Server 的本地位置。
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>安装并注册代理

> [!NOTE]
> 无法通过 Azure 门户启用备份。 使用 Microsoft Azure 恢复服务代理备份 Windows Server 的系统状态。
>

1. 在 Downloads 文件夹（或其他保存位置）中找到并双击 **MARSagentinstaller.exe**。

    安装程序会在提取、安装和注册恢复服务代理时提供一系列消息。

    ![运行恢复服务代理安装程序凭据](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. 完成 Microsoft Azure 恢复服务代理安装向导。 若要完成该向导，需完成以下操作：

   * 选择安装和缓存文件夹的位置。
   * 如果使用代理服务器来连接 Internet，请提供代理服务器信息。
   * 如果使用经过身份验证的代理，请提供用户名和密码详细信息。
   * 提供已下载的保管库凭据
   * 将加密通行短语保存在安全的位置。

     > [!NOTE]
     > 如果你丢失或忘记了该通行短语，Microsoft 无法帮助恢复备份数据。 请将文件保存在安全的位置。 需要还原备份。
     >
     >

现已安装代理，且已向保管库注册计算机。 接下来可以配置和计划备份。

## <a name="back-up-windows-server-system-state"></a>备份 Windows Server 系统状态

初始备份包括两个任务：

* 计划备份
* 首次备份系统状态

若要完成初始备份，请使用 Microsoft Azure 恢复服务代理。

> [!NOTE]
> 可以在 Windows Server 2008 R2 到 Windows Server 2016 上备份系统状态。 客户端 Sku 不支持系统状态备份。 系统状态不会显示为适用于 Windows 客户端或 Windows Server 2008 SP2 计算机的选项。
>
>

### <a name="to-schedule-the-backup-job"></a>计划备份作业

1. 打开 Microsoft Azure 恢复服务代理。 可以通过在计算机中搜索 **Microsoft Azure 备份**找到该代理。

    ![启动 Azure 恢复服务代理](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. 在恢复服务代理中，选择 " **计划备份**"。

    ![计划 Windows Server 备份](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. 在计划备份向导的 " **入门** " 页上，选择 " **下一步**"。

4. 在 " **选择要备份的项** " 页上，选择 " **添加项**"。

5. 选择 " **系统状态** "，然后选择 **"确定"**。

6. 选择“下一步”。

7. 在后续页中选择系统状态备份所需的备份频率和保留策略。

8. 在 "确认" 页上，查看信息，然后选择 " **完成**"。

9. 在向导完成创建备份计划后，选择“关闭”。

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>首次备份 Windows Server 系统状态

1. 请确保需要重启的 Windows Server 没有挂起的更新。

2. 在恢复服务代理中，选择 " **立即备份** " 以通过网络完成初始种子设定。

    ![立即备份 Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. 在出现的 "**选择备份项目**" 屏幕上选择 "**系统状态**"，然后选择 "**下一步**"

4. 在“确认”页上复查“立即备份向导”用于备份计算机的设置。 然后选择“备份”。

5. 选择“关闭”以关闭向导。 如果在备份过程完成之前关闭向导，向导将继续在后台运行。
    > [!NOTE]
    > MARS 代理在 `SFC /verifyonly` 每次系统状态备份之前都触发为预检查的一部分。 这是为了确保作为系统状态一部分备份的文件具有与 Windows 版本相对应的正确版本。 在[本文](/windows-server/administration/windows-commands/sfc)中详细了解系统文件检查器 (SFC)。
    >

完成初始备份后，备份控制台中显示“**作业已完成**”状态。

  ![IR 完成](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>是否有任何问题?

如果有疑问，请 [向我们发送反馈](https://feedback.azure.com/forums/258995-azure-backup)。

## <a name="next-steps"></a>后续步骤

* 详细了解如何 [备份 Windows 计算机](backup-windows-with-mars-agent.md)。
* 至此，你已备份 Windows Server 系统状态，接下来可以[管理保管库和服务器](backup-azure-manage-windows-server.md)了。
* 如果需要还原备份，请参阅[将文件还原到 Windows 计算机](backup-azure-restore-windows-server.md)一文。
