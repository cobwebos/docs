<properties
   pageTitle="了解如何通过 Azure 备份并使用 Resource Manager 部署模型将 Windows 中的文件和文件夹备份到 Azure | Microsoft Azure"
   description="了解如何通过创建保管库、安装恢复服务代理，并将文件和文件夹备份到 Azure 来备份 Windows Server 数据。"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="如何备份;怎样备份"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.date="06/27/2016"
   ms.author="jimpark;"/>

# 初步了解：通过 Azure 备份并使用 Resource Manager 部署模型备份文件和文件夹

本文介绍如何通过 Azure 备份并使用 Resource Manager 将 Windows Server（或 Windows 客户端）文件和文件夹备份到 Azure。本教程旨在引导你完成基本操作。如果想要开始使用 Azure 备份，本文的内容非常合适。

如果想要深入了解 Azure 备份，请阅读此[概述](backup-introduction-to-azure-backup.md)。

将文件和文件夹备份到 Azure 需要进行以下活动：

![步骤 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) 获取一个 Azure 订阅（如果还没有的话）。<br> ![步骤 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) 创建恢复服务保管库。<br> ![步骤 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) 下载所需的文件。<br> ![步骤 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) 安装并注册恢复服务代理。<br> ![步骤 5](./media/backup-try-azure-backup-in-10-mins/step-5.png) 备份文件和文件夹。

![如何使用 Azure 备份来备份 Windows 计算机](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

## 步骤 1：获取 Azure 订阅

如果你还没有 Azure 订阅，可以先创建一个[免费帐户](https://azure.microsoft.com/free/)，这样就可以访问任何 Azure 服务。

## 步骤 2：创建恢复服务保管库

若要备份文件和文件夹，你需要在要存储数据的区域内创建一个恢复服务保管库。还需要确定复制存储的方式。

### 创建恢复服务保管库

1. 如果你尚未登录 [Azure 门户](https://portal.azure.com/)，请使用你的 Azure 订阅登录。

2. 在“中心”菜单中，单击“**浏览**”，然后在资源列表中，键入“**恢复服务**”并单击“**恢复服务保管库**”。

    ![创建恢复服务保管库步骤 1](./media/backup-try-azure-backup-in-10-mins/browse-to-rs-vaults.png) <br/>

3. 在“恢复服务保管库”菜单中，单击“添加”。

    ![创建恢复服务保管库步骤 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    此时将打开恢复服务保管库边栏选项卡，其中会提示你提供“名称”、“订阅”、“资源组”和“位置”。

    ![创建恢复服务保管库步骤 5](./media/backup-try-azure-backup-in-10-mins/rs-vault-attributes.png)

4. 对于“名称”，请输入一个友好名称以标识保管库。

5. 单击“订阅”查看可用订阅列表。

6. 单击“资源组”查看可用资源组列表，或单击“新建”以创建新的资源组。

7. 单击“位置”，为保管库选择地理区域。此选项决定了备份数据要发送到的地理区域。

8. 单击“创建”。

    如果在完成后看不到你的保管库列出，请单击“刷新”。在列表刷新时，单击该保管库的名称。

### 确定存储冗余
在首次创建恢复服务保管库时，需确定复制存储的方式。

1. 单击新的保管库以打开仪表板。

2. 在“设置”边栏选项卡（它将随保管库仪表板自动打开）中，单击“备份基础结构”。

3. 在“备份基础结构”边栏选项卡中，单击“备份配置”以查看“存储复制类型”。

    ![创建恢复服务保管库步骤 5](./media/backup-try-azure-backup-in-10-mins/backup-infrastructure.png)

4. 为你的保管库选择合适的存储复制选项。

    ![恢复服务保管库列表](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    默认情况下，保管库具有异地冗余存储。如果你使用 Azure 作为主要备份存储终结点，则继续使用异地冗余存储。如果使用 Azure 作为非主要备份存储终结点，则选择本地冗余存储，以减少在 Azure 中存储数据的成本。请在此[概述](../storage/storage-redundancy.md)中深入了解[异地冗余](../storage/storage-redundancy.md#geo-redundant-storage)和[本地冗余](../storage/storage-redundancy.md#locally-redundant-storage)存储选项。

现在你已创建保管库，接着就可以通过下载 Microsoft Azure 恢复服务代理和保管库凭据，开始准备基础结构以备份文件和文件夹了。

## 步骤 3 - 下载文件

1. 单击恢复服务保管库仪表板上的“设置”。

    ![打开备份目标边栏选项卡](./media/backup-try-azure-backup-in-10-mins/settings-button.png)

2. 单击“设置”边栏选项卡上的“入门”>“备份”。

    ![打开备份目标边栏选项卡](./media/backup-try-azure-backup-in-10-mins/getting-started-backup.png)

3. 单击“备份”边栏选项卡上的“备份目标”。

    ![打开备份目标边栏选项卡](./media/backup-try-azure-backup-in-10-mins/backup-goal.png)

4. 从“运行工作负荷的位置?”菜单选择“本地”。

5. 从“要备份的项?”菜单选择“文件和文件夹”，然后单击“确定”。

### 下载恢复服务代理

1. 在“准备基础结构”边栏选项卡中，单击“下载 Windows Server 或 Windows 客户端的代理”。

    ![准备基础结构](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-short.png)

2. 在下载弹出窗口中单击“保存”。默认情况下，**MARSagentinstaller.exe** 文件将保存到 Downloads 文件夹。

### 下载保管库凭据

1. 在“准备基础结构”边栏选项卡上单击“下载”>“保存”。

    ![准备基础结构](./media/backup-try-azure-backup-in-10-mins/prepare-infrastructure-download.png)

## 步骤 4 - 安装并注册代理

>[AZURE.NOTE] 通过 Azure 门户启用备份功能即将推出。目前，可以使用本地 Microsoft Azure 恢复服务代理备份文件和文件夹。

1. 在 Downloads 文件夹（或其他保存位置）中找到并双击 **MARSagentinstaller.exe**。

2. 完成 Microsoft Azure 恢复服务代理安装向导。若要完成该向导，你需要：

    - 选择安装和缓存文件夹的位置。
    - 如果使用代理服务器来连接 Internet，请提供代理服务器信息。
    - 如果使用经过身份验证的代理，请提供用户名和密码详细信息。
    - 提供已下载的保管库凭据
    - 将加密通行短语保存在安全的位置。

    >[AZURE.NOTE] 如果你丢失或忘记了通行短语，Microsoft 无法帮助你恢复备份数据。请将文件保存在安全的位置。还原备份时需要用到此文件。

现已安装代理，且已向保管库注册计算机。接下来可以配置和计划备份。

## 步骤 5：备份文件和文件夹

初始备份包括两个关键任务：

- 计划备份
- 首次备份文件和文件夹

若要完成初始备份，请使用 Microsoft Azure 恢复服务代理。

### 计划备份

1. 打开 Microsoft Azure 恢复服务代理。可以通过在计算机中搜索 **Microsoft Azure 备份**找到该代理。

    ![启动 Azure 恢复服务代理](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. 在恢复服务代理中，单击“**计划备份**”。

    ![计划 Windows Server 备份](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. 在计划备份向导的“开始使用”页上，单击“下一步”。

4. 在“选择要备份的项”页上，单击“添加项”。

5. 选择要备份的文件和文件夹，然后单击“确定”。

6. 单击“下一步”。

7. 在“指定备份计划”页上指定**备份计划**，然后单击“下一步”。

    可以计划每日（频率为一天最多三次）或每周备份。

    ![Windows Server 备份项](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule-close.png)  


    >[AZURE.NOTE] 有关如何指定备份计划的详细信息，请参阅[使用 Azure 备份来取代磁带基础结构](backup-azure-backup-cloud-as-tape.md)一文。

8. 在“选择保留策略”页上，为备份复制选择“保留策略”。

    保留策略指定备份可以存储的时间长短。你可以根据备份的创建时间指定不同的保留策略，而不只是为所有备份点指定一个“通用的策略”。你可以根据需要修改每日、每周、每月和每年保留策略。

9. 在“选择初始备份类型”页上，选择初始备份类型。将“自动通过网络”选项保持选中状态，然后单击“下一步”。

    你可以通过网络自动备份，或者脱机备份。本文的余下部分将介绍自动备份过程。如果你想要执行脱机备份，请查看 [Azure 备份中的脱机备份工作流](backup-azure-backup-import-export.md)以了解更多信息。

10. 在“确认”页上复查信息，然后单击“完成”。

11. 在向导完成创建备份计划后，请单击“关闭”。

### 首次备份文件和文件夹

1. 在恢复服务代理中单击“**立即备份**”，以通过网络完成初始种子设定。

    ![立即备份 Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

2. 在“确认”页上复查“立即备份向导”用于备份计算机的设置。然后单击“备份”。

3. 单击“关闭”以关闭向导。如果你在备份过程完成之前执行此操作，向导将继续在后台运行。

完成初始备份后，备份控制台中将显示“作业已完成”状态。

![IR 完成](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)  


## 有疑问？
如果你有疑问，或者希望包含某种功能，请[给我们反馈](http://aka.ms/azurebackup_feedback)。

## 后续步骤
- 详细了解如何[备份 Windows 计算机](backup-configure-vault.md)。
- 备份文件和文件夹后，可以[管理保管库和服务器](backup-azure-manage-windows-server.md)。
- 如果需要还原备份，请参阅[将文件还原到 Windows 计算机](backup-azure-restore-windows-server.md)一文。

<!---HONumber=AcomDC_0921_2016-->