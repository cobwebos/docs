---
title: "管理 StorSimple 卷 | Microsoft Docs"
description: "介绍了如何添加、修改、监视和删除 StorSimple 卷，以及在必要时如何使其脱机。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ccabd859-590c-4568-a81d-6ff38f125be3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/11/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2b79492914bb52e970343a2e829622122f03642b


---
# <a name="use-the-storsimple-manager-service-to-manage-volumes"></a>使用 StorSimple Manager 服务管理卷
[!INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>概述
本教程介绍了如何使用 StorSimple Manager 服务在 StorSimple 设备和 StorSimple 虚拟设备上创建和管理卷。

StorSimple Manager 服务是 Azure 经典门户的一个扩展，可让用户通过单个 Web 界面管理 StorSimple 解决方案。 除了管理卷以外，还可以使用 StorSimple Manager 服务来创建和管理 StorSimple 服务、查看和管理设备、查看警报以及查看和管理备份策略和备份目录。

> [!NOTE]
> Azure StorSimple 只能创建精简预配的卷。 无法在 Azure StorSimple 系统上创建完全预配或部分预配的卷。
> 
> 精简预配是一项虚拟化技术，可使可用存储看起来超过物理资源。 Azure StorSimple 使用精简预配来分配恰好满足当前需求的足够空间，而不是提前保留充足的存储。 云存储的弹性性质促进了这一方法的实施，因为 Azure StorSimple 能够增加或减少云存储以满足不断变化的需求。
> 
> 

## <a name="the-volumes-page"></a>“卷”页
在“卷”页中，可以管理在 Microsoft Azure StorSimple 设备上为发起程序（服务器）预配的存储卷。 该页显示 StorSimple 设备上的卷列表。

 ![“卷”页](./media/storsimple-manage-volumes/HCS_VolumesPage.png)

卷包括一系列属性：

* **卷名称** – 必须具有唯一性且有助于识别卷。 筛选特定的卷时，还可以使用此名称来监视报告。
* **状态** – 可以是“联机”或“脱机”。 如果某个卷已脱机，允许访问该卷的发起程序（服务器）将看不到该卷。
* **容量** – 按照发起程序（服务器）所获知的卷大小指定卷大小。 容量指定发起程序（服务器）可以存储的数据总量。 卷进行了精简预配，并且从数据中删除了重复数据项。 这意味着设备不会根据配置的卷容量在内部或者在云中预先分配物理存储容量。 卷容量是按需要分配和使用的。
* **类型** – 卷类型可以是“分层”或“存档”（“分层”的一个子类型）
* **访问** – 指定允许访问此卷的发起程序（服务器）。 如果发起程序不是与卷关联的访问控制记录 (ACR) 的成员，则发起程序将看不到该卷。
* **监视** – 指定是否正在监视卷。 默认情况下，创建卷时会启用监视。 但是，克隆卷时会禁用监视。 若要为卷启用监视，请遵循“监视卷”中的说明。

与卷相关的最常见任务包括：

* 添加卷
* 修改卷
* 删除卷
* 使卷脱机
* 监视卷

## <a name="add-a-volume"></a>添加卷
你在部署 StorSimple 解决方案期间[创建了一个卷](storsimple-deployment-walkthrough-u1.md#step-6-create-a-volume)。 添加卷的过程与此类似。

### <a name="to-add-a-volume"></a>添加卷
1. 在“设备”页上，选择设备、双击它，然后单击“卷容器”选项卡。
2. 选择一个卷容器，然后单击对应行中的箭头以访问与该容器关联的卷。
3. 在页面底部单击“添加”。 此时将启动“添加卷向导”。
   
     ![“添加卷向导”中的“基本设置”](./media/storsimple-manage-volumes/AddVolume1.png)
4. 在“添加卷向导”中的“基本设置” 下，执行以下操作：
   
   1. 为卷提供“名称”。
   2. 指定卷的“预配的容量”（以 GB 或 TB 为单位）。 对于物理设备，容量必须介于 1 GB 和 64 TB 之间。 在 StorSimple 虚拟设备上，可以为卷预配的最大容量是 30 TB。
   3. 选择卷的“使用类型”。 如果针对存档数据使用分层卷，则选中“将此卷用于不常访问的存档数据”复选框可将卷的重复数据删除区块大小更改为 512 KB。 如果不选择此选项，相应的分层卷将使用 64 KB 区块大小。 重复数据删除区块越大，设备将大型存档数据传输到云端的速度就越快。（分层卷以前称为主卷。）
   4. 单击箭头图标![箭头图标](./media/storsimple-manage-volumes/HCS_ArrowIcon.png)以转到“其他设置”页。
      
        ![“添加卷向导”中的“其他设置”](./media/storsimple-manage-volumes/AddVolume2.png)
5. 在“其他设置”下，添加一条新的访问控制记录 (ACR)：
   
   1. 从下拉列表中选择一条访问控制记录 (ACR)。 另外，也可以添加一条新 ACR。 ACR 通过将主机 IQN 与记录中列出的 IQN 进行匹配来确定哪些主机可以访问卷。
   2. 建议你通过选中“为此卷启用默认备份”复选框来启用默认备份。
   3. 单击对号图标 ![对号图标](./media/storsimple-manage-volumes/HCS_CheckIcon.png) 以使用指定的设置创建卷。

新卷现在已可供使用。

## <a name="modify-a-volume"></a>修改卷
需要扩展卷或更改有权访问该卷的主机时，可以修改卷。

> [!IMPORTANT]
> * 如果在设备上修改了卷大小，则还必须在主机上修改卷大小。
> * 此处所述的主机端步骤适用于 Windows Server 2012 (2012R2)。 Linux 或其他主机操作系统的过程有所不同。 如果要在运行其他操作系统的主机上修改卷，请参考主机操作系统的说明。
> 
> 

### <a name="to-modify-a-volume"></a>修改卷
1. 在“设备”页上，选择设备、双击它，然后单击“卷容器”选项卡。 此页以表格格式列出与设备关联的所有卷容器。
2. 选择并单击一个卷容器可以显示该容器中所有卷的列表。
3. 在“卷”页上，选择一个卷并单击“修改”。
4. 在“修改卷向导”中的“基本设置”下，可以执行以下操作：
   
   * 编辑“名称”和“类型”。如果希望通过选中“将此卷用于不常访问的存档数据”复选框将卷的重复数据删除区块大小更改为 512 KB 来将分层卷修改为存档卷，可以执行此操作。
   * 增加**预配的容量**。 **预配的容量**只能增加。 创建卷后无法缩小卷。
     
     > [!NOTE]
     > 将卷容器分配给卷后无法更改卷容器。
     > 
     > 
5. 在“其他设置”下，可以执行以下操作：
   
   * 修改 ACR，前提是卷处于脱机状态。 如果卷处于联机状态，需要先将它脱机。 修改 ACR 之前，请参阅[使卷脱机](#take-a-volume-offline)中的步骤。
   * 在卷脱机后修改 ACR 的列表。
     
     > [!NOTE]
     > 无法更改卷的“为此卷启用默认备份”选项。
     > 
     > 
6. 单击对号图标保存更改 ![对号图标](./media/storsimple-manage-volumes/HCS_CheckIcon.png)。 Azure 经典门户将显示一条正在更新卷消息。 成功更新卷后，它将显示一条成功消息。
7. 如果要扩展卷，请在 Windows 主机计算机上完成以下步骤：
   
   1. 转到“计算机管理” ->“磁盘管理”。
   2. 右键单击“磁盘管理”，然后选择“重新扫描磁盘”。
   3. 在磁盘列表中选择已更新的卷，单击右键，然后选择“扩展卷”。 此时将启动“扩展卷向导”。 单击“下一步”。
   4. 使用默认值完成向导。 在向导完成后，卷应该显示增大后的大小。

![可用视频](./media/storsimple-manage-volumes/Video_icon.png) **可用视频**

要观看演示了如何扩展卷的视频，请单击[此处](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/)。

## <a name="take-a-volume-offline"></a>使卷脱机
想要修改或删除某个卷时，可能需要将它脱机。 脱机的卷不可用于读写访问。 需要同时在主机和设备上使卷脱机。 可以执行以下步骤来使卷脱机。

### <a name="to-take-a-volume-offline"></a>使卷脱机
1. 使卷脱机之前，请确保相关的卷未被使用。
2. 先在主机上使卷脱机。 这样可以消除任何造成卷上数据损坏的潜在风险。 有关具体的步骤，请参阅适用于你的主机操作系统的说明。
3. 在主机上脱机后，执行以下步骤在设备上使卷脱机：
   
   1. 在“设备”页上，选择设备、双击它，然后单击“卷容器”选项卡。 “卷容器”选项卡以表格格式列出与设备关联的所有卷容器。
   2. 选择并单击一个卷容器可以显示该容器中所有卷的列表。
   3. 选择一个卷并单击“脱机”。
   4. 出现确认提示时，单击“是”。 该卷随即将会脱机。
      
      卷脱机后，将显示“联机”选项。

> [!NOTE]
> “脱机”命令向设备发送请求来使卷脱机。 如果主机仍在使用该卷，则会导致连接断开，但是使卷脱机的命令不会失败。
> 
> 

## <a name="delete-a-volume"></a>删除卷
> [!IMPORTANT]
> 仅当卷已脱机时，才可以删除该卷。
> 
> 

若要删除卷，请完成以下步骤。

### <a name="to-delete-a-volume"></a>删除卷
1. 在“设备”页上，选择设备、双击它，然后单击“卷容器”选项卡。
2. 选择包含要删除的卷的卷容器。 单击该卷容器以访问“卷”页。
3. 此时将以表格格式显示与此容器关联的所有卷。 检查要删除的卷的状态。 如果要删除的卷未脱机，请根据[使卷脱机](#take-a-volume-offline)中的步骤使其脱机。
4. 卷脱机后，单击页面底部的“删除”。
5. 出现确认提示时，单击“是”。 随即会删除该卷，“卷”页将显示容器中卷的更新列表。

## <a name="monitor-a-volume"></a>监视卷
使用卷监视功能可以收集卷的 I/O 相关统计信息。 默认情况下，创建的前 32 个卷会启用监视， 其他卷会禁用监视。 对于克隆的卷，默认情况下也会禁用监视。

可以执行以下步骤来为卷启用或禁用监视。

### <a name="to-enable-or-disable-volume-monitoring"></a>启用或禁用卷监视
1. 在“设备”页上，选择设备、双击它，然后单击“卷容器”选项卡。
2. 选择卷所在的卷容器，然后单击该卷容器以访问“卷”页。
3. 此时将以表格视图列出与此容器关联的所有卷。 单击并选择所需的卷或卷副本。
4. 在页面底部，单击“修改”。
5. 在“修改卷向导”的“基本设置”下，从“监视”下拉列表中选择“启用”或“禁用”。
   
    ![修改卷基本设置](./media/storsimple-manage-volumes/HCS_MonitorVolumeM.png)

## <a name="next-steps"></a>后续步骤
* 了解如何[克隆 StorSimple 卷](storsimple-clone-volume.md)。
* 了解如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。




<!--HONumber=Nov16_HO3-->


