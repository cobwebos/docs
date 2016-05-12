<properties
   pageTitle="了解如何将文件和文件夹从 Windows 备份到 Azure | Microsoft Azure"
   description="了解如何通过创建保管库、安装备份代理，并将文件和文件夹备份到 Azure，来备份 Windows Server 数据。"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="如何备份;怎样备份"/>

<tags
        ms.service="backup"
   ms.date="04/14/2016"
	 wacn.date=""/>
# 先睹为快：将文件和文件夹从 Windows Server 或客户端备份到 Azure

本文介绍如何使用 Azure 备份将 Windows Server（或 Windows 客户端）文件和文件夹备份到 Azure。本教程旨在引导你完成基本操作。如果想要开始使用 Azure 备份，本文的内容非常合适。

如果想要深入了解 Azure 备份，请阅读此[概述](backup-introduction-to-azure-backup.md)。

将文件和文件夹备份到 Azure 需要进行以下活动：

![步骤 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) 获取 Azure 订阅（如果没有）。<br>
![步骤 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) 创建备份保管库并下载所需的项。<br>
![步骤 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) 下载并注册备份代理。<br>
![步骤 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) 备份文件和文件夹。


![如何使用 Azure 备份来备份 Windows 计算机](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## 步骤 1：获取 Azure 订阅

如果你没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/)，这样便可以访问任何 Azure 服务。

## 步骤 2：创建备份保管库并下载所需的项

若要备份文件和文件夹，你需要在要存储数据的区域内创建一个备份保管库。你还要确定存储的复制方式，并下载凭据和备份代理。

### 创建备份保管库的步骤

1. 如果你尚未登录 [Azure 门户](https://portal.azure.com/)，请使用你的 Azure 订阅登录。

2. 单击“新建”>“混合集成”>“备份”。

    ![开始准备备份文件和文件夹](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

3. 在“名称”中，输入一个友好名称以标识此备份保管库。

4. 在“区域”中，选择距离你最近的区域以加快文件传输速度。

5. 单击“创建保管库”。

    ![创建保管库](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    创建备份保管库后，该保管库将在恢复服务的资源中列为“活动”。

    ![保管库状态为活动](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

在创建保管库后，选择存储的复制方式。

>[AZURE.NOTE] 必须选择在保管库创建好之后、有任何计算机向其注册之前，存储的复制方式。将某个项注册到保管库后，存储复制选项将会锁定且不能修改。

### 选择存储的复制方式

1. 单击创建的保管库。
2. 在“快速启动”页面上，选择“配置”。

    ![配置保管库](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

3. 选择适当的存储选项。

    如果你使用 Azure 作为第一级备份，请选择[异地冗余存储](../storage/storage-redundancy.md#geo-redundant-storage)。如果你使用 Azure 作为第三级备份，请选择[本地冗余存储](../storage/storage-redundancy.md#locally-redundant-storage)。

    ![选择存储复制选项](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

4. 如果已选择“本地冗余”，请单击“保存”，因为“异地冗余”是默认选项。

使用保管库凭据向备份保管库验证计算机。下面是这些凭据的下载方法。

### 下载保管库凭据
保管库凭据文件仅在注册过程中使用，48 小时后过期。

1. 若要返回到保管库的“快速启动”页，请单击
    ![选择新保管库](./media/backup-try-azure-backup-in-10-mins/quick-start-icon.png)

2. 单击“下载保管库凭据”>“保存”。

接下来，请下载备份代理。

### 下载备份代理

单击“用于 Windows Server、System Center Data Protection Manager 或 Windows 客户端的代理”>“保存”。

![保存备份代理](./media/backup-try-azure-backup-in-10-mins/agent.png)

现已创建保管库并下载好所有项目，接下来需要安装并注册备份代理。

## 步骤 3：下载并注册备份代理

1. 从保存位置双击“MARSagentinstaller.exe”。
2. 完成 Microsoft Azure 恢复服务代理安装向导。若要完成该向导，你需要：
    - 选择安装和缓存文件夹的位置。
    - 如果使用代理服务器来连接 Internet，请提供代理服务器信息。
    - 如果使用经过身份验证的代理，请提供用户名和密码详细信息。
    - 将加密通行短语保存在安全的位置。

    >[AZURE.NOTE] 如果你丢失或忘记了通行短语，Microsoft 无法帮助你恢复备份数据。请将文件保存在安全的位置。还原备份时需要用到此文件。

现已安装代理，且已向保管库注册计算机。接下来可以配置和计划备份。

## 步骤 4：备份文件和文件夹
如果备份代理尚未打开，可以通过在计算机中搜索“Microsoft Azure 备份”找到该代理。

1. 在“备份代理”中，单击“计划备份”。

    ![计划 Windows Server 备份](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. 完成计划备份向导。在完成向导时，你可以：

    - 选择要备份的文件和文件夹。
    - 指定备份计划（每日或每周）。
    - 确定保留策略。
    - 选择想要如何完成初始备份（通过网络或脱机）。

    详细了解[脱机完成初始备份](backup-azure-backup-import-export.md)。
<br><br>

3. 完成向导后，请返回到“备份代理”，然后单击“立即备份”以通过网络完成初始备份。

    ![立即备份 Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

4. 在“确认”屏幕上单击“备份”。如果你在备份过程完成之前关闭向导，向导将在后台继续运行。

    初始备份完成后，控制台中的“作业”视图将显示作业已完成。

    ![初始备份完成](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

祝贺你，你已将文件和文件夹成功备份到 Azure 备份。

## 后续步骤
- 详细了解如何[备份 Windows 计算机](backup-configure-vault.md)。
- 备份文件和文件夹后，可以[管理保管库和服务器](backup-azure-manage-windows-server.md)。
- 如果需要还原备份，请参考 [restore files to a Windows machine（将文件还原到 Windows 计算机）](backup-azure-restore-windows-server.md) 一文。

<!---HONumber=Mooncake_0503_2016-->