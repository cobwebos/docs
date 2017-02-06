---
title: "StorSimple Snapshot Manager 和卷 | Microsoft Docs"
description: "介绍如何使用 StorSimple Snapshot Manager MMC 管理单元查看和管理卷以及配置备份。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5086fa86149e4d8133df339b1261d3792fa66b4c


---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>使用 StorSimple Snapshot Manager 查看和管理卷
## <a name="overview"></a>概述
可以使用 StorSimple Snapshot Manager“**卷**”节点（位于“**作用域**”窗格上）选择卷并查看其相关信息。 卷显示与主机装载的卷相对应的驱动器。 “**卷**”节点显示 StorSimple 所支持的本地卷和卷类型，其中包括通过使用 iSCSI 和设备发现的卷。 

有关受支持卷的详细信息，请转到[支持多个卷类型](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types)。

![“结果”窗格中的卷列表](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

“**卷**”节点还允许你在 StorSimple Snapshot Manager 发现卷后，重新扫描或删除这些卷。 

本教程介绍如何装载、初始化和格式化卷，然后使用 StorSimple Snapshot Manager 执行以下操作：

* 查看有关卷的信息 
* 删除卷
* 重新扫描卷 
* 配置基本卷并将其备份
* 配置动态镜像卷并将其备份

> [!NOTE]
> “**操作**”窗格中也提供了所有“**卷**”节点操作。
> 
> 

## <a name="mount-volumes"></a>装载卷
使用以下过程装载、初始化和格式化 StorSimple 卷。 此过程使用磁盘管理（一个系统实用工具）来管理硬盘和相应的卷或分区。 有关磁盘管理的详细信息，请转到 Microsoft TechNet 网站上的[磁盘管理](https://technet.microsoft.com/library/cc770943.aspx)。

#### <a name="to-mount-volumes"></a>装载卷
1. 在主计算机上，启动 Microsoft iSCSI 发起程序。
2. 提供其中一个接口 IP 地址作为目标门户或发现 IP 地址，然后连接到设备。 连接设备之后，卷即可供 Windows 系统访问。 有关使用 Microsoft iSCSI 发起程序的详细信息，请转到[安装和配置 Microsoft iSCSI 发起程序][1] 中的“连接到 iSCSI 目标设备”部分。
3. 使用以下任意选项启动磁盘管理：
   
   * 在“**运行**”框中键入 Diskmgmt.msc。
   * 启动服务器管理器，展开“**存储**”节点，然后选择“**磁盘管理**”。
   * 启动“**管理工具**”，展开“**计算机管理**”节点，然后选择“**磁盘管理**”。 
     
     > [!NOTE]
     > 必须使用管理员特权才能运行磁盘管理。
     > 
     > 
4. 使卷联机：
   
   1. 在“磁盘管理”中，右键单击标记为“**脱机**”的任何卷。
   2. 单击“**重新激活磁盘**”。 重新激活磁盘后，磁盘应标记为“**联机**”。
5. 初始化卷：
   
   1. 右键单击发现的卷。
   2. 在菜单上，选择“**初始化磁盘**”。
   3. 在“**初始化磁盘**”对话框中，选择要初始化的磁盘，然后单击“**确定**”。
6. 格式化简单卷：
   
   1. 右键单击要格式化的卷。
   2. 在菜单上，选择“**新建简单卷**”。
   3. 使用“新建简单卷”向导对卷进行格式化：
      
      * 指定卷的大小。
      * 提供驱动器号。
      * 选择 NTFS 文件系统。
      * 指定 64 KB 分配单元大小。
      * 执行快速格式化。
7. 格式化多分区卷。 有关说明，请转到[实施磁盘管理](https://msdn.microsoft.com/library/dd163556.aspx)中的“分区和卷”部分。

## <a name="view-information-about-your-volumes"></a>查看卷的相关信息
使用以下过程查看本地卷和 Azure StorSimple 卷的相关信息。

#### <a name="to-view-volume-information"></a>查看卷信息
1. 单击桌面图标启动 StorSimple Snapshot Manager。 
2. 在“**作用域**”窗格中，单击“**卷**”节点。 本地卷和已装入卷的列表（其中包括所有 Azure StorSimple 卷）将显示在“**结果**”窗格中。 “**结果**”窗格中的列是可以配置的。 （右键单击“**卷**”节点，选择“**查看**”，然后选择“**添加/删除列**”。）
   
    ![配置列](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | 结果列 | 说明 |
   |:--- |:--- |
   |  Name |“**名称**”列包含分配给每个发现的卷的驱动器号。 |
   |  设备 |“**设备**”列包含连接到主计算机的设备 IP 地址。 |
   |  设备卷名称 |“**设备卷名称**”列包含选定的卷所属的设备卷的名称。 这是在 Azure 经典门户中针对特定卷定义的卷名称。 |
   |  访问路径 |“**访问路径**”列显示该卷的访问路径。 这是在主计算机上可以访问该卷的驱动器号或装入点。 |

## <a name="delete-a-volume"></a>删除卷
使用以下过程从 StorSimple Snapshot Manager 中删除卷。

> [!NOTE]
> 如果该卷是任何卷组的一部分，则无法将其删除。 （该删除选项不适用于作为某个卷组成员的卷。）必须删除整个卷组才能删除该卷。
> 
> 

#### <a name="to-delete-a-volume"></a>删除卷
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，单击“**卷**”节点。 
3. 在“**结果**”窗格中，右键单击要删除的卷。
4. 在菜单上，单击“**删除**”。 
   
    ![删除卷](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. 此时将显示“**删除卷**”对话框。 在文本框中键入“**确认**”，然后单击“**确定**”。
6. 默认情况下，StorSimple Snapshot Manager 在删除卷之前会进行备份。 这种预防措施可以防止意外执行删除操作而导致的数据丢失。 在备份卷时，StorSimple Snapshot Manager 会显示“**自动快照**”进度消息。 
   
    ![自动快照消息](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>重新扫描卷
使用以下过程重新扫描连接到 StorSimple Snapshot Manager 的卷。

#### <a name="to-rescan-the-volumes"></a>重新扫描卷
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，右键单击“**卷**”，然后单击“**重新扫描卷**”。
   
    ![重新扫描卷](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    此过程会与 StorSimple Snapshot Manager 同步卷列表。 任何更改（如新建的卷或删除的卷）都将反映在结果中。

## <a name="configure-and-back-up-a-basic-volume"></a>配置和备份基本卷
使用以下过程配置基本卷的备份，然后立即开始备份或创建已计划备份的策略。

### <a name="prerequisites"></a>先决条件
开始之前：

* 请确保正确配置 StorSimple 设备和主计算机。 有关详细信息，请转到[部署本地 StorSimple 设备](storsimple-deployment-walkthrough-u2.md)。
* 安装和配置 StorSimple Snapshot Manager。 有关详细信息，请转到[部署 StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md)。

#### <a name="to-configure-backup-of-a-basic-volume"></a>配置基本卷的备份
1. 在 StorSimple 设备上创建基本卷。
2. 如[装载卷](#mount-volumes)中所述，装载、初始化和格式化卷。 
3. 单击桌面上的 StorSimple Snapshot Manager 图标。 StorSimple Snapshot Manager 窗口即会出现。 
4. 在“**作用域**”窗格中，右键单击“**卷**”节点，然后选择“**重新扫描卷**”。 扫描完成后，卷的列表应出现在“**结果**”窗格中。 
5. 在“**结果**”窗格中，右键单击该卷，然后选择“**创建卷组**”。 
   
    ![创建卷组](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. 在“**创建卷组**”对话框中，键入卷组的名称，将卷分配到该卷组，然后单击“**确定**”。
7. 在“**作用域**”窗格中，展开“**卷组**”节点。 新卷组应出现在“**卷组**”节点下。 
8. 右键单击卷组名称。
   
   * 若要启动交互式（按需）备份作业，请单击“**执行备份**”。 
   * 若要计划自动备份，请单击“**创建备份策略**”。 在“**常规**”页上，从列表中选择一个卷组。 在“**计划**”页上，输入计划详细信息。 完成后，单击“**确定**”。 
9. 若要确认备份作业已启动，请在“**作用域**”窗格中展开“**作业**”节点，然后单击“**正在运行**”节点。 当前正在运行的作业的列表将会显示在“**结果**”窗格中。 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>配置和备份动态镜像卷
完成以下步骤以配置动态镜像卷的备份：

* 步骤 1：使用磁盘管理创建动态镜像的卷。 
* 步骤 2：使用 StorSimple Snapshot Manager 配置备份。

### <a name="prerequisites"></a>先决条件
开始之前：

* 请确保正确配置 StorSimple 设备和主计算机。 有关详细信息，请转到[部署本地 StorSimple 设备](storsimple-deployment-walkthrough-u2.md)。
* 安装和配置 StorSimple Snapshot Manager。 有关详细信息，请转到[部署 StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md)。
* 在 StorSimple 设备上配置两个卷。 （在示例中，可用卷为**磁盘 1** 和**磁盘 2**。） 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>步骤 1：使用磁盘管理创建动态镜像的卷
磁盘管理是一个系统实用工具，用于管理硬盘以及其中包含的卷或分区。 有关磁盘管理的详细信息，请转到 Microsoft TechNet 网站上的[磁盘管理](https://technet.microsoft.com/library/cc770943.aspx)。

#### <a name="to-create-a-dynamic-mirrored-volume"></a>创建动态镜像卷
1. 使用以下任意选项启动磁盘管理： 
   
   * 打开“**运行**”框，键入 **Diskmgmt.msc**，然后按 Enter 键。
   * 启动服务器管理器，展开“**存储**”节点，然后选择“**磁盘管理**”。 
   * 启动“**管理工具**”，展开“**计算机管理**”节点，然后选择“**磁盘管理**”。 
2. 请确保在 StorSimple 设备上有两个卷可用。 （在该示例中，可用卷为**磁盘 1** 和**磁盘 2**。） 
3. 在“磁盘管理”窗口的下窗格的右栏中，右键单击“**磁盘 1**”，然后选择“**新建镜像卷**”。 
   
    ![新建镜像卷](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. 在“**新建镜像卷**”向导页上，单击“**下一步**”。
5. 在“**选择磁盘**”页上，在“**选定**”窗格中选择“**磁盘 2**”，单击“**添加**”，然后单击“**下一步**”。 
6. 在“**分配驱动器号或路径**”页上，接受默认值，然后单击“**下一步**”。 
7. 在“**格式化卷**”页上的“**分配单元大小**”框中，选择“**64K**”。 选择“**执行快速格式化**”复选框，然后单击“**下一步**”。 
8. 在“**正在完成新建镜像卷**”页上，查看你的设置，然后单击“**完成**”。 
9. 此时会出现一条消息，指示基本磁盘将转换为动态磁盘。 单击 **“是”**。
   
    ![动态磁盘转换消息](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. 在“磁盘管理”中，验证磁盘 1 和磁盘 2 是否显示为动态镜像卷。 （“**动态**”应出现在状态列中，而且容量条颜色应变为红色，即表示镜像卷。） 
    
    ![磁盘管理镜像动态磁盘](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>步骤 2：使用 StorSimple Snapshot Manager 配置备份
使用以下过程配置动态镜像卷，然后立即开始备份或创建已计划备份的策略。

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>配置动态镜像卷的备份
1. 单击桌面上的 StorSimple Snapshot Manager 图标。 StorSimple Snapshot Manager 窗口即会出现。 
2. 在“**作用域**”窗格中，右键单击“**卷**”节点并选择“**重新扫描卷**”。 扫描完成后，卷的列表应出现在“**结果**”窗格中。 动态镜像卷被列为单个卷。 
3. 在“**结果**”窗格中，右键单击动态镜像卷，然后单击“**创建卷组**”。 
4. 在“**创建卷组**”对话框中，键入卷组名称，将动态镜像卷分配到该卷组，然后单击“**确定**”。 
5. 在“**作用域**”窗格中，展开“**卷组**”节点。 新卷组应出现在“**卷组**”节点下。 
6. 右键单击卷组名称。 
   
   * 若要启动交互式（按需）备份作业，请单击“**执行备份**”。 
   * 若要计划自动备份，请单击“**创建备份策略**”。 在“**常规**”页上，从列表中选择一个卷组。 在“**计划**”页上，输入计划详细信息。 完成后，单击“**确定**”。 
7. 你可以在备份作业运行过程中进行监视。 在“**作用域**”窗格中，展开“**作业**”节点，然后单击“**正在运行**”，作业详细信息即会显示在“**结果**”窗格中。 备份作业完成后，详细信息会传输到“**过去 24 小时**”作业列表。 

## <a name="next-steps"></a>后续步骤
* 了解如何[使用 StorSimple Snapshot Manager 管理你的 StorSimple 解决方案](storsimple-snapshot-manager-admin.md)。
* 了解如何[使用 StorSimple Snapshot Manager 创建和管理卷组](storsimple-snapshot-manager-manage-volume-groups.md)。

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx



<!--HONumber=Dec16_HO2-->


