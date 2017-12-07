---
title: "将 Windows Server 备份到 Azure | Microsoft Docs"
description: "本教程详细介绍了如何将本地 Windows Server 备份到恢复服务保管库。"
services: back up
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: "windows server 备份; 备份 windows server; 备份和灾难恢复"
ms.assetid: 
ms.service: back up
ms.workload: storage-back up-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2017
ms.author: saurabhsensharma;markgal;
ms.custom: mvc
ms.openlocfilehash: 0dbf3850c7fcccb1a02e70a19b498522a4ce0e79
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="back-up-windows-server-to-azure"></a>将 Windows Server 备份到 Azure


可使用 Azure 备份保护 Windows Server 免受损坏、攻击和灾难影响。 Azure 备份提供一个名为 Microsoft Azure 恢复服务 (MARS) 代理的轻量工具。 MARS 代理安装在 Windows Server 上，用于通过 Windows Server 系统状态保护文件、文件夹和服务器配置信息。 本教程介绍如何使用 MARS 代理将 Windows Server 备份到 Azure。 本教程介绍如何执行下列操作： 


> [!div class="checklist"]
> * 下载和设置 MARS 代理
> * 为服务器备份配置备份时间和保留计划
> * 执行即席备份


## <a name="log-in-to-azure"></a>登录 Azure

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

备份 Windows Server 前，必须创建一个存储备份或还原点的位置。 [恢复服务保管库](backup-azure-recovery-services-vault-overview.md)是 Azure 中存储来自 Windows Server 的备份的容器。 按照以下步骤在 Azure 门户中创建恢复服务保管库。 

1. 在左侧菜单上，选择“所有服务”，然后在服务列表中键入“恢复服务”。 单击“**恢复服务保管库**”。

   ![打开恢复服务保管库](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2.  在“恢复服务保管库”菜单中，单击“添加”。

   ![提供保管库信息](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3.  在“恢复服务保管库”菜单中，

    - 在“名称”中键入“myRecoveryServicesVault”。
    - 当前订阅 ID 显示在“订阅”中。
    - 对于“资源组”，请选择“使用现有”，然后选择“myResourceGroup”。 如果没有“myResourceGroup”，请选择“新建”，然后键入“myResourceGroup”。 
    - 从“位置”下拉菜单中选择“西欧”。
    - 单击“创建”，创建恢复服务保管库。
 
创建保管库后，它会显示在“恢复服务保管库”的列表中。

## <a name="download-recovery-services-agent"></a>下载恢复服务代理

Microsoft Azure 恢复服务 (MARS) 代理在 Windows Server 和恢复服务保管库之间创建关联。 以下过程介绍如何将代理下载到服务器。

1.  在恢复服务保管库列表中，选择“myRecoveryServicesVault”以打开其仪表板。

   ![提供保管库信息](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2.  在保管库仪表板菜单中，单击“备份”。

3.  在“备份目标”菜单上：

    - 对于“工作负载的运行位置?”，请选择“本地” 
    - 对于“要备份的项?”，请选择“文件和文件夹”以及“系统状态” 

    ![提供保管库信息](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)
    
4.  单击“准备基础结构”以打开“准备基础结构”菜单。
5.  在“准备基础结构”菜单上，单击“下载 Windows Server 或 Windows 客户端的代理”，下载 MARSAgentInstaller.exe。 

    ![准备基础结构](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    安装程序单独打开浏览器并下载 MARSAgentInstaller.exe。
 
6.  运行此下载文件前，请单击“准备基础结构”边栏选项卡上的“下载”按钮，下载并保存“保管库凭据”文件。 连接 MARS 代理和恢复服务保管库时需使用此文件。

    ![准备基础结构](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)
 
## <a name="install-and-register-the-agent"></a>安装并注册代理

1. 找到并双击已下载的 MARSagentinstaller.exe。
2. “Microsoft Azure 恢复服务代理安装向导”随即出现。 执行向导的过程中，显示提示时请提供以下信息，然后单击“注册”。
    - 文件夹的安装和缓存位置。
    - 代理服务器信息（如果使用代理服务器连接到 Internet）。
    - 用户名和密码详细信息（如果使用经验证的代理）。

    ![准备基础结构](./media/tutorial-backup-windows-server-to-azure/mars-installer.png) 

3. 向导结束时，单击“继续注册”，并提供先前过程中下载的“保管库凭据”文件。
 
4. 出现提示时，请提供在 Windows Server 中加密备份的加密密码。 请将密码保存于安全位置，因为若密码丢失，Microsoft 无法恢复密码。

5. 单击“完成” 。 

## <a name="configure-backup-and-retention"></a>配置备份和保留期

使用 Microsoft Azure 恢复服务代理规划何时将 Windows Server 备份到 Azure。 在代理下载到的服务器上执行以下步骤。

1. 打开 Microsoft Azure 恢复服务代理。 可以通过在计算机中搜索 **Microsoft Azure 备份**找到该代理。

2.  在恢复服务代理控制台中，单击“操作”窗格中的“计划备份”。

    ![准备基础结构](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. 单击“下一步”，导航到“选择要备份的项”页面。

4. 单击“添加项”，然后在打开的对话框中选择“系统状态”，以及要备份的文件或文件夹。 然后单击“确定”。

5. 单击“下一步”。

6. 在“指定备份计划(系统状态)”页上，指定需为系统状态触发文件和文件夹备份的具体时间（哪天或哪周），然后单击“下一步”。 

7.  在“选择保留策略”页上，为系统状态的文件和文件夹备份副本选择“保留策略”，然后单击“下一步”。
8. 同样，为选择的文件和文件夹选择备份计划和保留策略。 
8.  在“选择初始备份类型”页面上，使“自动通过网络”选项保持选中状态，然后单击“下一步”。
9.  在“确认”页上检查信息，并单击“完成”。
10. 在向导完成创建备份计划后，请单击“**关闭**”。

## <a name="perform-an-ad-hoc-back-up"></a>执行即席备份

备份作业运行时表示计划已建立。 但服务器尚未备份。 运行按需备份是灾难恢复最佳做法，可确保服务器的数据弹性。

1.  在 Microsoft Azure 恢复服务代理控制台中，单击“立即备份”。

    ![准备基础结构](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2.  在“立即备份”向导上，从“文件和文件夹”或“系统状态”中选择要备份的内容，然后单击“下一步”。 
3. 在“确认”页上，检查“立即备份”向导用于备份服务器的设置。 然后单击“**备份**”。
4.  单击“**关闭**”以关闭向导。 如果在备份过程完成之前关闭向导，向导将继续在后台运行。
4.  完成初始备份后，MARS 代理控制台的“作业”窗格中会显示“作业已完成”状态。


## <a name="next-steps"></a>后续步骤

本教程使用 Azure 门户执行了以下操作： 
 
> [!div class="checklist"] 
> * 创建恢复服务保管库 
> * 下载 Microsoft Azure 恢复服务代理 
> * 安装代理 
> * 为 Windows Server 配置备份 
> * 执行按需备份 

继续阅读下一教程，了解如何将文件从 Azure 恢复到 Windows Server

> [!div class="nextstepaction"] 
> [将文件从 Azure 还原到 Windows Server](./tutorial-backup-restore-files-windows-server.md) 

