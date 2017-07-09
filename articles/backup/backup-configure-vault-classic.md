---
title: "将 Windows 服务器或工作站备份到 Azure（经典模型）| Microsoft 文档"
description: "将 Windows 服务器或客户端备份到 Azure 中的备份保管库。 了解有关使用 Azure 备份代理将文件和文件夹保护到备份保管库的基础知识。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "备份保管库; 备份 Windows 服务器; 备份 windows;"
ms.assetid: 3b543bfd-8978-4f11-816a-0498fe14a8ba
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: markgal;trinadhk;
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 3cd32a85c225d1670887d086c324db1eadd61262
ms.contentlocale: zh-cn
ms.lasthandoff: 06/16/2017


---
# <a name="back-up-a-windows-server-or-workstation-to-azure-using-the-classic-portal"></a>使用经典门户将 Windows 服务器或工作站备份到 Azure
> [!div class="op_single_selector"]
> * [经典门户](backup-configure-vault-classic.md)
> * [Azure 门户](backup-configure-vault.md)
>
>

本文介绍了在准备你的环境以及将 Windows 服务器（或工作站）备份到 Azure 时要遵循的步骤。 此外，还说明了部署备份解决方案的注意事项。 如果你对 Azure 备份感兴趣，本文可引导你快速完成相关过程。


> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型：Resource Manager 模型和经典模型。 本文介绍使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。
>
>

## <a name="before-you-start"></a>开始之前
若要将服务器或客户端备份到 Azure，你需要一个 Azure 帐户。 如果你没有帐户，只需几分钟的时间就能创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-backup-vault"></a>创建备份保管库
若要从服务器或客户端备份文件和文件夹，你需要在要存储数据的地理区域内创建一个备份保管库。

> [!IMPORTANT]
> 从 2017 年 3 月份开始，无法再使用经典门户来创建备份保管库。
>
> 现在可将备份保管库升级到恢复服务保管库。 有关详细信息，请参阅文章[将备份保管库升级到恢复服务保管库](backup-azure-upgrade-backup-to-recovery-services.md)。 Microsoft 鼓励将备份保管库升级到恢复服务保管库。<br/> 从 2017 年 11 月 1 日起：
>- 其余的所有备份保管库都将自动升级到恢复服务保管库。
>- 将无法在经典门户中访问备份数据。 而是使用 Azure 门户在恢复服务保管库中访问备份数据。
>


## <a name="download-the-vault-credential-file"></a>下载保管库凭据文件
本地计算机需要先在备份保存库中通过身份验证才能将数据备份到 Azure。 身份验证是通过*保管库凭据*实现的。 从经典门户通过安全通道下载保管库凭据文件。 证书私钥不会在门户或服务中持久保存。

### <a name="to-download-the-vault-credential-file-to-a-local-machine"></a>将保管库凭据文件下载到本地计算机
1. 在左侧导航窗格中单击“**恢复服务**”，然后选择你创建的备份保管库。

    ![IR 完成](./media/backup-configure-vault-classic/rs-left-nav.png)
2. 在“快速启动”页上，单击“**下载保管库凭据**”。

   经典门户将使用保管库名称和当前日期的组合生成保管库凭据。 保管库凭据文件仅在注册工作流中使用，48 小时后过期。

   保管库凭据文件可从门户下载。
3. 单击“**保存**”将保管库凭据文件下载到本地帐户的“下载”文件夹中。 也可以从“**保存**”菜单中选择“**另存为**”，以指定保管库凭据文件的保存位置。

   > [!NOTE]
   > 确保将保管库凭据文件保存在可从计算机访问的位置。 如果将它存储在文件共享或服务器消息块中，请确认你有权访问该文件。
   >
   >

## <a name="download-install-and-register-the-backup-agent"></a>下载、安装和注册备份代理
创建备份保管库并下载保管库凭据文件之后，必须在每台 Windows 计算机上安装一个代理。

### <a name="to-download-install-and-register-the-agent"></a>下载、安装和注册代理
1. 单击“**恢复服务**”，然后选择你要向其注册服务器的备份保管库。
2. 在“快速启动”页上，单击“**Windows Server、System Center Data Protection Manager 或 Windows 客户端的代理**”。 。

    ![保存代理](./media/backup-configure-vault-classic/agent.png)
3. 下载 MARSagentinstaller.exe 后，单击“**运行**”（或双击保存位置中的 **MARSAgentInstaller.exe**）。
4. 选择代理所需的安装文件夹和缓存文件夹，然后单击“**下一步**”。 指定的缓存位置必须至少有备份数据的 5% 的可用空间。
5. 你可以继续通过默认代理设置连接到 Internet。             如果使用代理服务器连接到 Internet ，请在“代理配置”页上选中“**使用自定义代理设置**”复选框，然后输入代理服务器详细信息。 如果使用已经过身份验证的代理，请输入用户名和密码详细信息，然后单击“**下一步**”。
6. 单击“**安装**”开始安装代理。 备份代理将安装 .NET Framework 4.5 和 Windows PowerShell（如果尚未安装）以完成安装。
7. 安装代理后，单击“**继续注册**”以继续运行工作流。
8. 在“保管库标识”页上，浏览到前面下载的保管库凭据文件并将其选中。

    从门户下载后，保管库凭据文件只能生效 48 小时。 如果此页上显示任何错误（例如“提供的保管库凭据文件已过期”），请登录到门户，并再次下载保管库凭据文件。

    确保将保管库凭据文件放置在安装应用程序可访问的位置。 如果你遇到访问相关的错误，请将保管库凭据文件复制到同一台计算机中的临时位置，然后重试操作。

    如果遇到保管库凭据错误（例如“提供的保管库凭据无效”），则表示该文件已损坏，或者没有与恢复服务关联的最新凭据。 请在从门户下载新的保管库凭据文件后重试该操作。 如果用户快速连续单击“**下载保管库凭据**”选项，则可能会出现此错误。 在这种情况下，只有最后一个保管库凭据文件有效。
9. 在“加密设置”页上，你可以生成一个通行短语，或者提供一个通行短语（最少包含 16 个字符）。 请记住将通行短语保存在安全位置。
10. 单击“完成” 。 注册服务器向导将在备份中注册服务器。

    > [!WARNING]
    > 如果你丢失或忘记了通行短语，Microsoft 无法帮助你恢复备份数据。 加密通行短语由你拥有，Microsoft 看不到你使用的通行短语。 请将该文件保存在安全位置，因为在恢复操作期间需要用到它。
    >
    >

11. 设置加密密钥后，请将“**启动 Microsoft Azure 恢复服务代理**”复选框保持选中状态，然后单击“**关闭**”。

## <a name="complete-the-initial-backup"></a>完成初始备份
初始备份包括两个关键任务：

* 创建备份计划
* 首次备份文件和文件夹

在备份策略完成初始备份之后，将会创建在需要恢复数据时可用的备份点。 备份策略根据定义的计划执行此操作。

### <a name="to-schedule-the-backup"></a>计划备份
1. 打开 Microsoft Azure 备份代理。 （如果你在关闭注册服务器向导时已将“**启动 Microsoft Azure 恢复服务代理**”复选框保持选中状态，备份代理将自动打开。）可以通过在计算机中搜索 **Microsoft Azure 备份**找到该代理。

    ![启动 Azure 备份代理](./media/backup-configure-vault-classic/snap-in-search.png)
2. 在“备份代理”中，单击“**计划备份**”。

    ![计划 Windows Server 备份](./media/backup-configure-vault-classic/schedule-backup-close.png)
3. 在计划备份向导的“开始使用”页上，单击“**下一步**”。
4. 在“选择要备份的项”页上，单击“**添加项**”。
5. 选择要备份的文件和文件夹，然后单击“**确定**”。
6. 单击“下一步”。
7. 在“**指定备份计划**”页上指定**备份计划**，然后单击“**下一步**”。

    可以计划每日（频率为一天最多三次）或每周备份。

    ![Windows Server 备份项](./media/backup-configure-vault-classic/specify-backup-schedule-close.png)

   > [!NOTE]
   > 有关如何指定备份计划的详细信息，请参阅 [使用 Azure 备份来取代磁带基础结构](backup-azure-backup-cloud-as-tape.md)一文。
   >
   >

8. 在“**选择保留策略**”页上，为备份复制选择“**保留策略**”。

    保留策略指定备份可以存储的时间长短。 你可以根据备份的创建时间指定不同的保留策略，而不只是为所有备份点指定一个“通用的策略”。 你可以根据需要修改每日、每周、每月和每年保留策略。
9. 在“选择初始备份类型”页上，选择初始备份类型。 将“**自动通过网络**”选项保持选中状态，然后单击“**下一步**”。

    你可以通过网络自动备份，或者脱机备份。 本文的余下部分将介绍自动备份过程。 如果你想要执行脱机备份，请查看 [Azure 备份中的脱机备份工作流](backup-azure-backup-import-export.md) 以了解更多信息。
10. 在“确认”页上复查信息，然后单击“**完成**”。
11. 在向导完成创建备份计划后，请单击“**关闭**”。

### <a name="enable-network-throttling-optional"></a>启用网络限制（可选）
备份代理提供网络限制。 限制功能将控制数据传输期间的网络带宽使用方式。 如果需要在上班时间内备份数据，但不希望备份程序干扰其他 Internet 流量，此控制机制很有帮助。 限制适用于备份和还原活动。

**启用网络限制**

1. 在“备份代理”中，单击“**更改属性**”。

    ![更改属性](./media/backup-configure-vault-classic/change-properties.png)
2. 在“**限制**”选项卡上，选中“**为备份操作启用 Internet 带宽使用限制**”复选框。

    ![网络限制](./media/backup-configure-vault-classic/throttling-dialog.png)
3. 启用限制后，指定在“**工作时间**”和“**非工作时间**”允许使用多少带宽进行备份数据传输。

    带宽值从每秒 512 千字节 (Kbps) 开始，最高可为每秒 1,023 兆字节 (MBps)。 你还可以指定“**工作时间**”的开始和结束时间，以及一周中有哪几天被视为工作日。 指定的工作时间以外的时间视为非工作时间。
4. 单击 **“确定”**。

### <a name="to-back-up-now"></a>立即备份
1. 在“备份代理”中单击“**立即备份**”，以通过网络完成初始种子设定。

    ![立即备份 Windows Server](./media/backup-configure-vault-classic/backup-now.png)
2. 在“确认”页上复查“立即备份向导”用于备份计算机的设置。 然后单击“**备份**”。
3. 单击“**关闭**”以关闭向导。 如果你在备份过程完成之前执行此操作，向导将继续在后台运行。

完成初始备份后，备份控制台中将显示“**作业已完成**”状态。

![IR 完成](./media/backup-configure-vault-classic/ircomplete.png)

## <a name="next-steps"></a>后续步骤
* 注册[免费 Azure 帐户](https://azure.microsoft.com/free/)。

有关备份 VM 或其他工作负荷的详细信息，请参阅：

* [备份 IaaS VM](backup-azure-vms-prepare.md)
* [使用 Microsoft Azure 备份服务器将工作负荷备份到 Azure](backup-azure-microsoft-azure-backup.md)
* [使用 DPM 将工作负荷备份到 Azure](backup-azure-dpm-introduction.md)

