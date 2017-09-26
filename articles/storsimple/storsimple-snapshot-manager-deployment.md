---
title: "部署 StorSimple Snapshot Manager | Microsoft Docs"
description: "了解如何下载和安装 StorSimple Snapshot Manager，这是用于管理 StorSimple 数据保护和备份功能的 MMC 管理单元。"
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: cde355381b0d726a1ab340bc4230b2dc8f6e2c56
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>部署 StorSimple Snapshot Manager MMC 管理单元

## <a name="overview"></a>概述
StorSimple Snapshot Manager 是一种 Microsoft 管理控制台 (MMC) 管理单元，可以简化 Microsoft Azure StorSimple 环境中的数据保护和备份管理。 借助 StorSimple Snapshot Manager，可以将 Microsoft Azure StorSimple 本地和云存储作为完全集成的存储系统进行管理，这样可以简化备份和还原流程并降低成本。 

该教程介绍了配置要求及安装、删除和升级 StorSimple Snapshot Manager 的流程。

> [!NOTE]
> * 不能使用 StorSimple Snapshot Manager 管理 Microsoft Azure StorSimple 虚拟数组（也称为 StorSimple 本地虚拟设备）。
> * 如果计划在 StorSimple 设备上安装 StorSimple Update 2，请在安装 StorSimple Update 2 前，务必先下载并安装最新版本的 StorSimple Snapshot Manager。 最新版本的 StorSimple Snapshot Manager 向后兼容，并可与所有已发布的 Microsoft Azure StorSimple 版本一起使用。 如果你正在使用旧版本的 StorSimple Snapshot Manager，则需要将其更新（安装新版本前，无需卸载旧版本）。


## <a name="storsimple-snapshot-manager-installation"></a>StorSimple Snapshot Manager 安装
StorSimple Snapshot Manager 可以安装在运行 Windows Server 2008 R2 SP1、Windows Server 2012 或 Windows Server 2012 R2 操作系统的计算机上。 在运行 Windows 2008 R2 的服务器上，还必须安装 Windows Server 2008 SP1 和 Windows Management Framework 3.0。

安装或升级 Microsoft 管理控制台 (MMC) 的 StorSimple Snapshot Manager 管理单元前，请确保已正确配置 Microsoft Azure StorSimple 设备和主机服务器。

## <a name="configure-prerequisites"></a>配置先决条件
以下步骤提供了安装 StorSimple Snapshot Manager 前必须完成的配置任务的高级概述。 有关 Microsoft Azure StorSimple 配置和安装的完整信息（包括系统要求和分步说明），请参阅[部署本地 StorSimple 设备](storsimple-8000-deployment-walkthrough-u2.md)。

> [!IMPORTANT]
> 请先查看[部署本地 StorSimple 设备](storsimple-8000-deployment-walkthrough-u2.md)中的[部署配置清单](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist)和[部署先决条件](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites)，然后再开始。
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>安装 StorSimple Snapshot Manager 前
1. 按照[安装 StorSimple 8100 设备](storsimple-8100-hardware-installation.md)或[安装 StorSimple 8600 设备](storsimple-8600-hardware-installation.md)中的说明解压、装载和连接 Microsoft Azure StorSimple 设备。
2. 请确保你的主计算机正在运行以下操作系统之一：
   
   * Windows Server 2008 R2（在运行 Windows 2008 R2 的服务器上，还必须安装 Windows Server 2008 SP1 和 Windows Management Framework 3.0）
   * Windows Server 2012
   * Windows Server 2012 R2
     
     对于 StorSimple 虚拟设备，主机必须是 Microsoft Azure 虚拟机。
3. 请确保你符合 Microsoft Azure StorSimple 的所有配置要求。 有关详细信息，请转到[部署先决条件](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites)。
4. 将设备连接到主机并执行初始配置。 有关详细信息，请转到[部署步骤](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps)。
5. 在设备上创建卷，将创建的卷分配到主机，并验证主机是否可以装载和使用这些卷。 StorSimple Snapshot Manager 支持以下类型的卷：
   
   * 基本卷
   * 简单卷
   * 动态卷
   * 镜像动态卷 (RAID 1)
   * 群集共享卷
     
     有关在 StorSimple 设备或 StorSimple 虚拟设备上创建卷的信息，请转到[部署本地 StorSimple 设备](storsimple-8000-deployment-walkthrough-u2.md)中的[第 6 步：创建卷](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)。

## <a name="install-a-new-storsimple-snapshot-manager"></a>安装新的 StorSimple Snapshot Manager
安装 StorSimple Snapshot Manager 前，请确保已按照[配置先决条件](#configure-prerequisites)中的说明装载、初始化你在 StorSimple 设备或 StorSimple 虚拟设备上创建的卷并设置其格式。

> [!IMPORTANT]
> * 对于 StorSimple 虚拟设备，主机必须是 Microsoft Azure 虚拟机。 
> * 主机必须运行 Windows 2008 R2、Windows Server 2012 或 Windows Server 2012 R2。 如果你的服务器运行的是 Windows Server 2008 R2，则还必须安装 Windows Server 2008 SP1 和 Windows Management Framework 3.0。
> * 必须配置从主机到 StorSimple 设备的 iSCSI 连接，然后才能将设备连接到 StorSimple Snapshot Manager。

按照这些步骤完成 StorSimple Snapshot Manager 的全新安装。 如果你正在安装升级，请转到[升级或重新安装 StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager)。

* 第 1 步：安装 StorSimple Snapshot Manager 
* 第 2 步：将 StorSimple Snapshot Manager 连接到设备 
* 第 3 步：验证到设备的连接 

### <a name="step-1-install-storsimple-snapshot-manager"></a>第 1 步：安装 StorSimple Snapshot Manager
使用以下步骤安装 StorSimple Snapshot Manager。

#### <a name="to-install-storsimple-snapshot-manager"></a>安装 StorSimple Snapshot Manager
1. 下载 StorSimple Snapshot Manager 软件（转到 Microsoft 下载中心中的 [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220)）并将其保存在主机本地。
2. 在“文件资源管理器”中，右键单击压缩文件夹，然后单击“全部提取”。
3. 在“提取压缩文件夹”窗口的“选择一个目标并提取文件”框中，键入或浏览至要提取的文件所在的路径。
   
    > [!IMPORTANT]
    > 必须将 StorSimple Snapshot Manager 安装在 C: drive 上。
    
4. 选中“完成时显示提取的文件”复选框，然后单击“提取”。
   
    ![“提取文件”对话框](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. 提取过程完成后，目标文件夹打开。 双击目标文件夹中出现的应用程序安装图标。
6. 出现提示“安装成功”的消息后，单击“关闭”。 应该会在桌面上看到 StorSimple Snapshot Manager 图标。
   
    ![桌面图标](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>第 2 步：将 StorSimple Snapshot Manager 连接到设备
使用以下步骤将 StorSimple Snapshot Manager 连接到 StorSimple 设备。

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>将 StorSimple Snapshot Manager 连接到设备
1. 单击桌面上的 StorSimple Snapshot Manager 图标。 StorSimple Snapshot Manager 窗口即会出现。 窗口中包含“作用域”、“结果”和“操作”窗格。 
   
    ![StorSimple Snapshot Manager 用户界面](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * “作用域”窗格（左窗格）包含以树结构组织的节点列表。 可以展开一些节点来选择视图或与该节点关联的特定数据。 单击箭头图标展开或折叠节点。 右键单击“作用域”窗格中的一个项查看该项的可用操作列表。
   * “结果”窗格（中间窗格）包含有关在“作用域”窗格中所选的节点、视图或数据的详细状态信息。
   * “操作”窗格列出了可以对在“作用域”窗格中所选的节点、视图或数据执行的操作。
     
     有关 StorSimple Snapshot Manager 用户界面的完整说明，请参阅 [StorSimple Snapshot Manager 用户界面](storsimple-use-snapshot-manager.md)。
2. 在“作用域”窗格中，右键单击“设备”节点，然后单击“配置设备”。 “配置设备”对话框出现。
   
    ![配置设备](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. 在“设备”列表框中，选择 Microsoft Azure StorSimple 设备或虚拟设备的 IP 地址。 在“密码”文本框中，键入为 Azure 门户中的设备创建的 StorSimple Snapshot Manager 密码。 单击 **“确定”**。
4. StorSimple Snapshot Manager 搜索你所标识的设备。 如果设备可用，则 StorSimple Snapshot Manager 会添加一个连接。 可以 [验证到设备的连接](#to-verify-the-connection) 以确认已成功添加连接。
   
    如果设备由于任何原因而不可用，则 StorSimple Snapshot Manager 会返回一条错误消息。 单击“确定”关闭错误消息，然后单击“取消”关闭“配置设备”对话框。
5. 当它连接到某个设备后，如果卷组有关联的备份，则 StorSimple Snapshot Manager 会导入为该设备配置的各个卷组。 没有关联备份的卷组将不被导入。 此外，不会导入针对卷组创建的备份策略。 若要查看已导入的组，右键单击“**作用域**”窗格最上方的“**卷组**”节点，然后单击“**切换已导入的组**”。

### <a name="step-3-verify-the-connection-to-the-device"></a>第 3 步：验证到设备的连接
使用以下步骤验证 StorSimple Snapshot Manager 是否已连接到 StorSimple 设备。

#### <a name="to-verify-the-connection"></a>验证连接
1. 在“作用域”窗格中，单击“设备”节点。
   
    ![StorSimple Snapshot Manager 设备状态](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. 检查“结果”窗格： 
   
   * 如果设备图标上出现绿色标识符并且“状态”栏中出现“可用”，则表示设备已连接。 
   * 如果设备图标上出现红色标识符并且“状态”栏中出现“不可用”，则表示设备未连接。 
   * 如果“状态”列中出现“正在刷新”，则 StorSimple Snapshot Manager 正在检索已连接设备的卷组和已关联的备份。

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>升级或重新安装 StorSimple Snapshot Manager
升级或重新安装软件前，应完全卸载 StorSimple Snapshot Manager。 

重新安装 StorSimple Snapshot Manager 前，备份主计算机上现有的 StorSimple Snapshot Manager 数据库。 此操作会保存备份策略和配置信息，以便可以轻松地将数据从备份中还原。

如果正在升级或重新安装 StorSimple Snapshot Manager，请按照以下步骤操作：

* 第 1 步：卸载 StorSimple Snapshot Manager 
* 第 2 步：备份 StorSimple Snapshot Manager 数据库 
* 第 3 步：重新安装 StorSimple Snapshot Manager 并还原数据库 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>第 1 步：卸载 StorSimple Snapshot Manager
使用以下步骤卸载 StorSimple Snapshot Manager。

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>卸载 StorSimple Snapshot Manager
1. 在主计算机上，打开“控制面板”，单击“程序”，然后单击“程序和功能”。
2. 在左窗格中，单击“卸载或更改程序”。
3. 右键单击“StorSimple Snapshot Manager”，然后单击“卸载”。
4. 此操作会启动 StorSimple Snapshot Manager 安装程序。 单击“修改安装程序”，然后单击“卸载”。
   
   > [!NOTE]
   > 如果有任何 MMC 进程正在后台中运行，例如 StorSimple Snapshot Manager 或磁盘管理，则卸载会失败且会收到提示在尝试卸载程序前关闭 MMC 的所有实例的消息。 选择“自动关闭应用程序并在安装完成后尝试重新启动它们”，然后单击“确定”。
   > 
   > 
5. 卸载过程完成后，“安装成功”消息出现。 单击“关闭”。

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>第 2 步：备份 StorSimple Snapshot Manager 数据库
使用以下步骤创建并保存 StorSimple Snapshot Manager 数据库的副本。

#### <a name="to-back-up-the-database"></a>备份数据库
1. 停止 Microsoft StorSimple Management Service：
   
   1. 启动服务器管理器。
   2. 在服务器管理器仪表板的“工具”菜单上，选择“服务”。
   3. 在“服务”页面上，选择“Microsoft StorSimple Management Service”。
   4. 在右窗格的“Microsoft StorSimple Management Service”下，单击“停止服务”。
      
        ![停止 StorSimple Device Manager 服务](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. 浏览至 C:\ProgramData\Microsoft\StorSimple\BACatalog。 
   
   > [!NOTE]
   > ProgramData 是隐藏的文件夹。
  
3. 找到目录 XML 文件，复制文件，并将副本保存在安全的位置或者云中。
   
    ![StorSimple 备份目录文件](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. 重新启动 Microsoft StorSimple Management Service： 
   
   1. 在服务器管理器仪表板的“工具”菜单上，选择“服务”。
   2. 在“服务”页面上，选择“Microsoft StorSimple Management Service”。
   3. 在右窗格的“Microsoft StorSimple Management Service”下，单击“重新启动服务”。 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>第 3 步：重新安装 StorSimple Snapshot Manager 并还原数据库
若要重新安装 StorSimple Snapshot Manager，请按照[安装新的 StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager) 中的步骤操作。 然后，使用以下步骤还原 StorSimple Snapshot Manager 数据库。

#### <a name="to-restore-the-database"></a>还原数据库
1. 停止 Microsoft StorSimple Management Service：
   
   1. 启动服务器管理器。
   2. 在服务器管理器仪表板的“工具”菜单上，选择“服务”。
   3. 在“服务”页面上，选择“Microsoft StorSimple Management Service”。
   4. 在右窗格的“Microsoft StorSimple Management Service”下，单击“停止服务”。
2. 浏览至 C:\ProgramData\Microsoft\StorSimple\BACatalog。
   
   > [!NOTE]
   > ProgramData 是隐藏的文件夹。
   > 
   > 
3. 删除目录 XML 文件，并将其替换为你在上面步骤中保存的版本。
4. 重新启动 Microsoft StorSimple Management Service： 
   
   1. 在服务器管理器仪表板的“工具”菜单上，选择“服务”。
   2. 在“服务”页面上，选择“Microsoft StorSimple Management Service”。
   3. 在右窗格的“Microsoft StorSimple Management Service”下，单击“重新启动服务”。

## <a name="next-steps"></a>后续步骤
* 若要了解有关 StorSimple Snapshot Manager 的详细信息，请转到[什么是 StorSimple Snapshot Manager？](storsimple-what-is-snapshot-manager.md)。
* 若要了解有关 StorSimple Snapshot Manager 用户界面的详细信息，请转到 [StorSimple Snapshot Manager 用户界面](storsimple-use-snapshot-manager.md)。
* 若要了解有关使用 StorSimple Snapshot Manager 的详细信息，请转到[使用 StorSimple Snapshot Manager 管理你的 StorSimple 解决方案](storsimple-snapshot-manager-admin.md)。


