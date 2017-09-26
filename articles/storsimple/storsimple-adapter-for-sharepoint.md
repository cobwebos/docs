---
title: "安装 StorSimple Adapter for SharePoint | Microsoft 文档"
description: "介绍如何在 SharePoint 服务器场中安装、配置或删除 StorSimple Adapter for SharePoint。"
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: v-sharos
ms.translationtype: HT
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 8910471e09b9ecc797005818538ccfc6a91c68a9
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>安装和配置 StorSimple Adapter for SharePoint
## <a name="overview"></a>概述
StorSimple Adapter for SharePoint 是可为 SharePoint 服务器场提供 Microsoft Azure StorSimple 弹性存储和数据保护的组件。 可以使用该适配器将二进制大型对象 (BLOB) 内容从 SQL Server 内容数据库转移到 Microsoft Azure StorSimple 混合云存储设备。

StorSimple Adapter for SharePoint 充当远程 BLOB 存储 (RBS) 提供程序，使用 SQL Server 远程 BLOB 存储功能将非结构化 SharePoint 内容（采用 BLOB 形式）存储在 StorSimple 设备支持的文件服务器上

> [!NOTE]
> StorSimple Adapter for SharePoint 支持 SharePoint Server 2010 远程 BLOB 存储 (RBS)。 它不支持 SharePoint Server 2010 外部 BLOB 存储 (EBS)。


* 若要下载 StorSimple Adapter for SharePoint，请转到 Microsoft 下载中心中的 [StorSimple Adapter for SharePoint][1]。
* 有关规划 RBS 和 RBS 限制的信息，请转到[决定在 SharePoint 2013 中使用 RBS][2]或[规划 RBS (SharePoint Server 2010)][3]。

本概述的余下部分说明 StorSimple Adapter for SharePoint 的角色以及 SharePoint 的容量和性能限制，这些都是在安装和配置该适配器之前应该了解的。 查看这些信息后，请转到 [StorSimple Adapter for SharePoint 安装](#storsimple-adapter-for-sharepoint-installation)开始设置该适配器。

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple Adapter for SharePoint 的优势
在 SharePoint 站点中，内容作为非结构化 BLOB 数据存储在一个或多个内容数据库中。 默认情况下，这些数据库托管在运行 SQL Server 且位于 SharePoint 服务器场中的计算机上。 BLOB 可快速扩增其大小，消耗大量的本地存储空间。 因此，可能需要寻找成本更低的另一种存储解决方案。 SQL Server 提供名为“远程 Blob 存储 (RBS)”的技术，用于将 BLOB 内容存储在 SQL Server 数据库以外的文件系统中。 有了 RBS，BLOB 就可以驻留在运行 SQL Server 的计算机上的文件系统中，或者可以存储在其他服务器计算机上的文件系统中。

RBS 要求使用 RBS 提供程序（例如 StorSimple Adapter for SharePoint）在 SharePoint 中启用 RBS。 StorSimple Adapter for SharePoint 与 RBS 兼容，允许将 BLOB 转移到 Microsoft Azure StorSimple 系统支持的服务器。 然后，Microsoft Azure StorSimple 会根据使用情况将 BLOB 数据存储在本地或云中。 非常活跃的 BLOB（通常称为第一层数据或热数据）驻留在本地。 不太活跃的数据和存档数据驻留在云中。 在内容数据库上启用 RBS 后，在 SharePoint 中创建的任何新 BLOB 内容都将存储在 StorSimple 设备中，而会不是存储在内容数据库中。

RBS 的 Microsoft Azure StorSimple 实现具有以下优势：

* 通过将 BLOB 内容转移到单独的服务器，可降低 SQL Server 上的查询负载，从而提高 SQL Server 的响应能力。 
* Azure StorSimple 使用重复数据删除功能和压缩功能来减小数据大小。
* Azure StorSimple 以本地快照和云快照的方式提供数据保护。 此外，如果将数据库本身置于 StorSimple 设备上，则可以通过崩溃一致性方式将内容数据库和 BLOB 一起进行备份。 （只有 StorSimple 8000 系列设备支持将内容数据库移到设备。 5000 或 7000 系列不支持此功能。）
* Azure StorSimple 提供灾难恢复功能，包括故障转移、文件和卷恢复（包括测试恢复），以及数据快速还原。
* 可将 Kroll Ontrack PowerControls 等数据恢复软件用于 BLOB 数据的 StorSimple 快照，执行 SharePoint 内容的项级恢复。 （需单独购买此数据恢复软件。）
* StorSimple Adapter for SharePoint 插入到 SharePoint 管理中心门户中，可让用户从一个中心位置管理整个 SharePoint 解决方案。

将 BLOB 内容转移到文件系统可以进一步节省成本并提供更多益处。 例如，使用 RBS 可以减少对成本昂贵的第 1 层存储的需求，而且由于 RBS 收缩内容数据库，因而减少了 SharePoint 服务器场中需要的数据库数量。 但是，其他因素（例如数据库大小限制和非 RBS 内容数量）也会影响存储要求。 有关使用 RBS 的成本和优势的详细信息，请参阅[规划 RBS (SharePoint Foundation 2010)][4] 和[决定在 SharePoint 2013 中使用 RBS][5]。

### <a name="capacity-and-performance-limits"></a>容量和性能限制
考虑在 SharePoint 解决方案中使用 RBS 之前，应了解 SharePoint Server 2010 和 SharePoint Server 2013 的已测试性能和容量限制，以及这些限制与可接受性能之间存在何种关系。 有关详细信息，请参阅 [Software Boundaries and Limits for SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx)（SharePoint 2013 的软件边界和限制）。

在配置 RBS 之前，请检查以下事项：

* 确保内容的总大小（内容数据库的大小加上所有关联的外部化 BLOB 的大小）未超过 SharePoint 支持的 RBS 大小限制。 此限制为 200 GB。 
  
    **测量内容数据库和 BLOB 大小**
  
  1. 请在管理中心 WFE 上运行此查询。 启动 SharePoint Management Shell，并输入以下 Windows PowerShell 命令获取内容数据库的大小：
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      此步骤获取磁盘中内容数据库的大小。
  2. 在 SQL Server 上的 SQL Management Studio 中，针对每个内容数据库运行以下 SQL 查询之一，然后将结果与在步骤 1 中获取的数字相加。
     
     在 SharePoint 2013 内容数据库中输入：
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     在 SharePoint 2010 内容数据库中输入：
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     此步骤获取外部化 BlOB 的大小。
* 建议将所有 BLOB 和数据库内容存储在 StorSimple 设备本地。 StorSimple 设备是高可用性的双节点群集。 将内容数据库和 BLOB 放置在 StorSimple 设备上可实现高可用性。
  
    使用传统 SQL Server 迁移最佳实践，将内容数据库转移到 StorSimple 设备。 只有在数据库的所有 BLOB 内容已通过 RBS 转移到文件共享之后，才能转移数据库。 如果选择将内容数据库转移到 StorSimple 设备，我们建议在设备上将内容数据库存储配置为主卷。
* 在 Microsoft Azure StorSimple 中，无法保证本地存储在 StorSimple 设备上的内容不会通过分层方式转移到 Microsoft Azure 云存储。 因此，建议将 StorSimple 本地固定卷与 SharePoint RBS 结合使用。 这可以确保所有 BLOB 内容保留在 StorSimple 设备上，而不会转移到 Microsoft Azure。
* 如果未将内容数据库存储在 StorSimple 设备上，请使用支持 RBS 的传统 SQL Server 高可用性最佳实践。 SQL Server 群集支持 RBS，但 SQL Server 镜像不支持 RBS。 

> [!WARNING]
> 如果尚未启用 RBS，则不建议将内容数据库转移到 StorSimple 设备。 这种配置未经测试。

## <a name="storsimple-adapter-for-sharepoint-installation"></a>StorSimple Adapter for SharePoint 安装
在安装 StorSimple Adapter for SharePoint 之前，必须配置 StorSimple 设备并确保 SharePoint 服务器场和 SQL Server 实例化满足所有先决条件。 该教程介绍配置要求，以及安装和升级 StorSimple Adapter for SharePoint 的过程。

## <a name="configure-prerequisites"></a>配置先决条件
在安装 StorSimple Adapter for SharePoint 之前，请确保 StorSimple 设备、SharePoint 服务器场和 SQL Server 实例化满足以下先决条件。

### <a name="system-requirements"></a>系统要求
StorSimple Adapter for SharePoint 支持以下硬件和软件：

* 支持的操作系统 – Windows Server 2008 R2 SP1、Windows Server 2012 或 Windows Server 2012 R2
* 支持的 SharePoint 版本 – SharePoint Server 2010 或 SharePoint Server 2013
* 支持的 SQL Server 版本 – SQL Server 2008 Enterprise Edition、SQL Server 2008 R2 Enterprise Edition 或 SQL Server 2012 Enterprise Edition
* 支持的 StorSimple 设备 – StorSimple 8000 系列、StorSimple 7000 系列或 StorSimple 5000 系列。

### <a name="storsimple-device-configuration-prerequisites"></a>StorSimple 设备配置先决条件
StorSimple 设备是一个块设备，因此需要一个可以托管数据的文件服务器。 我们建议使用单独的服务器，而不要使用 SharePoint 场中的现有服务器。 此文件服务器必须与托管内容数据库的 SQL Server 计算机在同一局域网 (LAN) 中。

> [!TIP]
> * 如果为了实现高可用性而配置了 SharePoint 场，则还应该部署文件服务器以实现高可用性。
> * 如果未将内容数据库存储在 StorSimple 设备上，请使用支持 RBS 的传统的高可用性最佳实践。 SQL Server 群集支持 RBS，但 SQL Server 镜像不支持 RBS。 


请确保 StorSimple 设备已正确配置，并配置了相应的卷来支持 SharePoint 部署，并且可以从 SQL Server 计算机访问这些卷。 如果尚未部署和配置 StorSimple 设备，请转到[部署本地 StorSimple 设备](storsimple-8000-deployment-walkthrough-u2.md)。 请记下 StorSimple 设备的 IP 地址，因为在 StorSimple Adapter for SharePoint 安装过程中需要用到。

此外，请确保要用于 BLOB 外部化的卷满足以下要求：

* 该卷必须使用 64 KB 分配单元大小进行格式化。
* Web 前端 (WFE) 服务器和应用程序服务器必须能够通过通用命名约定 (UNC) 路径访问该卷。
* 必须将 SharePoint 服务器场配置为写入该卷。

> [!NOTE]
> 安装并配置适配器后，所有 BLOB 外部化必须通过 StorSimple 设备（该设备将为 SQL Server 提供卷并管理存储层）执行。 不能将其他任何目标用于 BLOB 外部化。


如果打算使用 StorSimple Snapshot Manager 来创建 BLOB 和数据库数据的快照，请务必在数据库服务器上安装 StorSimple Snapshot Manager，以便它可以使用 SQL 编写器服务来实现 Windows 卷影复制服务 (VSS)。

> [!IMPORTANT]
> StorSimple Snapshot Manager 不支持 SharePoint VSS 编写器，并且不能创建应用程序一致性 SharePoint 数据快照。 在使用 SharePoint 的情况下，StorSimple Snapshot Manager 只提供崩溃一致性备份。


## <a name="sharepoint-farm-configuration-prerequisites"></a>SharePoint 场配置先决条件
确保已正确配置 SharePoint 服务器场，如下所述：

* 验证 SharePoint 服务器场是否处于正常状态，并检查以下事项：
* 场中注册的所有 SharePoint WFE 服务器和应用程序服务器均正在运行，并且可以从要安装 StorSimple Adapter for SharePoint 的服务器 Ping 通。
* 每个 WFE 服务器和应用程序服务器上均正在运行 SharePoint 定时服务（SPTimerV3 或 SPTimerV4）。
* SharePoint 定时服务及在其下运行 SharePoint 管理中心站点的 IIS 应用程序池具有管理特权。
* 确保 Internet Explorer 增强的安全上下文 (IE ESC) 已禁用。 遵循以下步骤禁用 IE ESC：
  
  1. 关闭 Internet Explorer 的所有实例。
  2. 启动服务器管理器。
  3. 在左窗格中单击“本地服务器”。
  4. 在右窗格中的“IE 增强的安全配置”旁边单击“打开”。
  5. 在“管理员”下面，单击“关闭”。
  6. 单击 **“确定”**。

## <a name="remote-blob-storage-rbs-prerequisites"></a>远程 BLOB 存储 (RBS) 先决条件
确保使用受支持的 SQL Server 版本。 只有以下版本受支持且能够使用 RBS：

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

只能在 StorSimple 设备提供给 SQL Server 的卷上外部化 BLOB。 不支持将其他任何目标用于 BLOB 外部化。

完成所有必要的配置步骤后，请转到[安装 StorSimple Adapter for SharePoint](#install-the-storsimple-adapter-for-sharepoint)。

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>安装 StorSimple Adapter for SharePoint
使用以下步骤安装 StorSimple Adapter for SharePoint。 若要重新安装该软件，请参阅[升级或重新安装 StorSimple Adapter for SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint)。 安装所需的时间取决于 SharePoint 服务器场中的 SharePoint 数据库总数。

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>配置 RBS
安装 StorSimple Adapter for SharePoint 后，请按照以下过程配置 RBS。

> [!TIP]
> StorSimple Adapter for SharePoint 已插入 SharePoint 管理中心页，因此可以在 SharePoint 场中的每个内容数据库上启用或禁用 RBS。 不过，在内容服务器上启用或禁用 RBS 会导致 IIS 重置，这可能会使 SharePoint Web (WFE) 前端暂时不可用，具体取决于场配置。 （前端负载均衡器的使用、当前服务器工作负荷等多种因素可能会限制或消除这种中断现象的发生。）为了避免用户受到服务中断的影响，我们建议仅在计划的维护时间窗口启用或禁用 RBS。


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>配置垃圾回收
从 SharePoint 站点中删除对象时，这些对象不会从 RBS 存储卷中自动删除， 而是有一个异步后台维护程序从文件存储中删除孤立的 BLOB。 系统管理员可以将此进程安排为定期运行，也可以在必要时启动它。

启用 RBS 时，此维护程序 (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) 会自动安装在所有 SharePoint WFE 服务器和应用程序服务器上。 该程序安装在以下位置：*引导盘*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\

有关配置和使用此维护程序的信息，请参阅[在 SharePoint Server 2013 中维护 RBS][8]。

> [!IMPORTANT]
> RBS 维护程序会占用大量资源。 应将它安排为仅在 SharePoint 场中的活动较少的时段内运行。


### <a name="delete-orphaned-blobs-immediately"></a>立即删除孤立的 BLOB
如果需要立即删除孤立的 BlOB，请参考以下说明。 请注意，这些说明举例说明如何在包含以下组件的 SharePoint 2013 环境中执行此操作：

* 内容数据库名称为 WSS_Content。
* SQL Server 名称为 SHRPT13-SQL12\SHRPT13。
* Web 应用程序名称为 SharePoint–80。

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>升级或重新安装 StorSimple Adapter for SharePoint
使用以下过程升级 SharePoint 服务器，并重新安装 StorSimple Adapter for SharePoint，或者在现有 SharePoint 服务器场中升级或重新安装该适配器。

> [!IMPORTANT]
> 在尝试升级 SharePoint 软件和/或升级或重新安装 StorSimple Adapter for SharePoint 之前，请查看以下信息：
> 
> * 以前通过 RBS 转到外部存储的任何文件，必须等到重新安装完成并重新启用 RBS 功能之后才能使用。 为了限制用户受影响的程度，请在规划的维护期间执行任何升级或重新安装。
> * 升级/重新安装所需的时间取决于 SharePoint 服务器场中的 SharePoint 数据库总数。
> * 升级/重新安装完成后，需要为内容数据库启用 RBS。 有关详细信息，请参阅[配置 RBS](#configure-rbs)。
> * 如果要配置 RBS 的 SharePoint 场包含大量的数据库（超过 200 个），“SharePoint 管理中心”页可能会超时。如果发生这种情况，请刷新页面。 这不会影响配置过程。


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>删除 StorSimple Adapter for SharePoint
以下过程介绍如何将 BLOB 移回 SQL Server 内容数据库，然后卸载 StorSimple Adapter for SharePoint。 

> [!IMPORTANT]
> 必须先将 BLOB 移回内容数据库，然后才能卸载适配器软件。


### <a name="before-you-begin"></a>开始之前
在将数据移回 SQL Server 内容数据库并开始适配器删除过程之前，请先收集以下信息：

* 所有已启用 RBS 的数据库的名称
* 配置的 BLOB 存储的 UNC 路径

### <a name="move-the-blobs-back-to-the-content-databases"></a>将 BLOB 移回内容数据库
在卸载 StorSimple Adapter for SharePoint 软件之前，必须将所有已外部化的 BLOB 迁移回 SQL Server 内容数据库。 如果在将所有 BLOB 移回内容数据库之前尝试卸载 StorSimple Adapter for SharePoint，则会看到以下警告消息。

![警告消息](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>将 BLOB 移回内容数据库
1. 下载每个外部化对象。
2. 打开“SharePoint 管理中心”页，浏览到“系统设置”。
3. 在“Azure StorSimple”下面，单击“配置 StorSimple Adapter”。
4. 在“配置 StorSimple Adapter”页上，单击每个要从外部 BLOB 存储中删除的内容数据库下面的“禁用”按钮。 
5. 从 SharePoint 中删除对象，然后将其重新上传。

也可以使用 SharePoint 随附的 Microsoft` RBS Migrate()` PowerShell cmdlet。 有关详细信息，请参阅[将内容移入或移出 RBS](https://technet.microsoft.com/library/ff628255.aspx)。

将 BLOB 移回内容数据库后，请转到下一步：[卸载适配器](#uninstall-the-adapter)。

### <a name="uninstall-the-adapter"></a>卸载适配器
将 BLOB 移回 SQL Server 内容数据库后，请使用以下选项之一卸载 StorSimple Adapter for SharePoint。

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>使用安装程序卸载适配器
1. 使用具有管理员权限的帐户登录到 Web 前端 (WFE) 服务器。
2. 双击 StorSimple Adapter for SharePoint 安装程序。 此时会启动安装向导。
   
    ![安装向导](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. 单击“下一步”。 将显示以下页。
   
    ![安装向导删除页](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. 单击“删除”开始执行删除过程。 将显示以下页。
   
    ![安装向导确认页](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. 单击“删除”确认删除。 此时会显示以下进度页。
   
    ![安装向导进度页](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. 删除完成后，会显示完成页。 单击“完成”关闭安装向导。

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>使用控制面板卸载适配器
1. 打开控制面板，并单击“程序和功能”。
2. 选择“StorSimple Adapter for SharePoint”，并单击“卸载”。

## <a name="next-steps"></a>后续步骤
[详细了解 StorSimple](storsimple-overview.md)。

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/en-us/library/ff943565.aspx

