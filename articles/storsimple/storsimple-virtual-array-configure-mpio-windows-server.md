---
title: "在 Microsoft Azure StorSimple 虚拟阵列主机上配置 MPIO | Microsoft 文档"
description: "介绍了如何为连接到运行 Windows Server 2012 R2 的主机的 StorSimple 虚拟阵列配置多路径 I/O (MPIO)。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5b7a7f99-ee5b-4b7d-ab32-483a5a1fa504
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: ae076d52b532f5181868b2ab24ee8f2ffa887af7
ms.openlocfilehash: 53308da656f46a43332a204d9d49e972d8c1799c

---
# <a name="configure-multipath-io-on-windows-server-host-for-the-storsimple-virtual-array"></a>在 Windows Server 主机上为 StorSimple Virtual Array 配置多路径 I/O
## <a name="overview"></a>概述
本文介绍了如何在 Windows Server 主机上安装多路径 I/O 功能 (MPIO)，为纯 StorSimple 卷应用特定的配置设置，然后针对 StorSimple 卷验证 MPIO。 此过程假定具有两个网络接口的 StorSimple 1200 Virtual Array 连接到具有两个网络接口的 Windows Server 主机。 本文中包含的信息仅适用于虚拟阵列。 有关 StorSimple 8000 系列设备的信息，请转到[为 StorSimple 主机配置 MPIO](storsimple-configure-mpio-windows-server.md)。 

Windows Server 中的 MPIO 功能可以帮助构建具有高可用性的可容错存储配置。 MPIO 使用冗余的物理路径组件（适配器、电缆和交换机）在服务器与存储设备之间创建逻辑路径。 如果发生组件故障，导致某条逻辑路径失败，则多路径逻辑将使用备用路径执行 I/O，以便应用程序仍然可以访问其数据。 另外，根据你的配置，MPIO 还可以通过在这些路径之间重新进行负载平衡来提高性能。 有关详细信息，请参阅 [MPIO 概述](https://technet.microsoft.com/library/cc725907.aspx "MPIO overview and features")。

要实现 StorSimple 解决方案的高可用性，请在连接到 StorSimple 虚拟阵列（型号 1200）的 Windows Server 主机上配置 MPIO。 然后，主机服务器可以包容链路、网络或接口故障。 

需要执行以下步骤来配置 MPIO： 

* 配置先决条件
* 步骤 1：在 Windows Server 主机上安装 MPIO
* 步骤 2：为 StorSimple 卷配置 MPIO
* 步骤 3：在主机上装载 StorSimple 卷

以下各部分讨论了上述每个步骤。

## <a name="prerequisites"></a>先决条件
本部分详细介绍了 Windows Server 主机和虚拟阵列的配置先决条件。

### <a name="on-windows-server-host"></a>在 Windows Server 主机上
* 确保 Windows Server 主机已启用了 2 个网络接口。

### <a name="on-storsimple-virtual-array"></a>在 StorSimple 虚拟阵列上
* 应当将虚拟阵列配置为 iSCSI 服务器。 若要了解详细信息，请参阅[将虚拟阵列设置为 iSCSI 服务器](storsimple-virtual-array-deploy3-iscsi-setup.md)。 应当在阵列上启用一个或多个网络接口。   
* 应当可以从 Windows Server 主机访问虚拟阵列上的网络接口。
* 应当在 StorSimple Virtual Array 上创建一个或多个卷。 若要了解详细信息，请参阅“在 StorSimple 虚拟阵列上[添加卷](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume)”。 在此过程中，我们在虚拟阵列上创建了 3 个卷（1 个本地固定卷和 2 个分层卷，如下所示）。
  
    ![MPIO0](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio0.png)

### <a name="hardware-configuration-for-storsimple-virtual-array"></a>StorSimple 虚拟阵列的硬件配置
下图显示了此过程中为实现 Windows Server 主机和 StorSimple 虚拟阵列的高可用性和负载平衡多路径而使用的硬件配置。

![MPIO 硬件配置](./media/storsimple-virtual-array-configure-mpio-windows-server/1200hardwareconfig.png)

如上图所示：

* 预配在 Hyper-V 上的 StorSimple 虚拟阵列是一个配置为 iSCSI 服务器的单节点活动设备。
* 阵列上启用了两个虚拟网络接口。 在虚拟阵列的本地 Web UI 中，通过导航到“网络设置”来验证是否启用了两个网络接口，如下所示：
  
    ![1200 上启用的网络接口](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio9.png)
  
    记下已启用的网络接口（默认情况下为 Ethernet、Ethernet 2）的 IPv4 地址，并保存供稍后在主机上使用。
* Windows Server 主机上启用了两个网络接口。 如果主机和阵列的已连接接口在同一子网中，则将有 4 条路径可用。 此过程中就是这种情况。 但是，如果阵列上的每个网络接口和主机接口位于不同的 IP 子网中（且不可路由），则将只有 2 条路径可用。

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>步骤 1：在 Windows Server 主机上安装 MPIO
MPIO 是 Windows Server 上的一项可选功能，默认情况下不会安装。 应该通过服务器管理器将它作为一项功能安装。 若要在 Windows Server 主机上安装此功能，请完成以下过程。

[!INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>步骤 2：为 StorSimple 卷配置 MPIO
需要将 MPIO 配置为识别 StorSimple 卷。 若要将 MPIO 配置为识别 StorSimple 卷，请执行以下步骤。

[!INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>步骤 3：在主机上装载 StorSimple 卷
在 Windows Server 上配置 MPIO 后，可以装载在 StorSimple 阵列上创建的卷，然后可以利用 MPIO 实现冗余。 要装载卷，请执行以下步骤。

#### <a name="to-mount-volumes-on-the-host"></a>在主机上装载卷
1. 在 Windows Server 主机上打开“iSCSI 发起程序属性”窗口。 转到“服务器管理器”>“仪表板”>“工具”>“iSCSI 发起程序”。
2. 在“iSCSI 发起程序属性”对话框中，单击“发现”，然后单击“发现目标门户”。
3. 在“发现目标门户”对话框中，执行以下操作：
   
    1. 输入 StorSimple 虚拟阵列上启用的第一个网络接口的 IP 地址。 默认情况下，此接口为 **Ethernet**。 
    2. 单击“确定”以返回到“iSCSI 发起程序属性”对话框。
     
    > [!IMPORTANT]
    > 如果对 iSCSI 连接使用专用网络，请输入连接到专用网络的数据端口的 IP 地址。
     
4. 为阵列上的第二个网络接口（例如 Ethernet 2）重复步骤 2-3。 
5. 在“iSCSI 发起程序属性”对话框中选择“目标”选项卡。 对于你的虚拟阵列，应当会看到每个卷面都显示为“已发现的目标”下的一个目标。 在本例中，将会发现三个目标（对应于三个卷）。
   
    ![mpio1](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio1.png)
6. 单击“连接”来与 StorSimple 虚拟阵列建立 iSCSI 会话。 此时将显示“连接到目标”对话框。 选中“启用多路径”复选框。 单击“高级”。
   
    ![mpio2](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio2.png)
7. 在“高级设置”对话框中，执行以下操作：                                        
   
    1. 在“本地适配器”下拉列表中，选择“Microsoft iSCSI 发起程序”。
    2. 在“发起程序 IP”下拉列表中，选择主机的 IP 地址。
    3. 在“目标门户 IP”下拉列表中，选择阵列接口的 IP。
    4. 单击“确定”以返回到“iSCSI 发起程序属性”对话框。
     
        ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)

8. 单击“属性”。 
   
    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)

9. 在“属性”对话框中，单击“添加会话”。
   
   ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)
10. 在“连接到目标”对话框中，选中“启用多路径”复选框。 单击“高级”。
11. 在“高级设置”对话框中：                                        
    
    1. 在“本地适配器”下拉列表中，选择“Microsoft iSCSI 发起程序”。

    2. 在“发起程序 IP”下拉列表中，选择与主机对应的 IP 地址。 在本例中，你将阵列上的两个网络接口连接到主机上的单个网络接口。 因此，此接口与为第一个会话提供的接口相同。

    3. 在“目标门户 IP”下拉列表中，选择阵列上启用的第二个数据接口的 IP 地址。

    4. 单击“确定”以返回到“iSCSI 发起程序属性”对话框。 你已添加了与目标之间的第二个会话。
      
       ![mpio11](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio11.png)
    
    5. 在添加所需的会话（路径）后，在“iSCSI 发起程序属性”对话框中，选择目标并单击“属性”。 在“属性”对话框的“会话”选项卡上，记下与可能的路径排列对应的四个会话标识符。 若要取消会话，请选中会话标识符旁边的复选框，然后单击“断开连接”。

    6. 若要查看会话内存在的设备，请选择“设备”选项卡。 若要为所选设备配置 MPIO 策略，请单击“MPIO”。

    7. “详细信息”对话框随即将出现。 在“MPIO”选项卡上，可以选择适当的“负载均衡策略”设置。 还可以查看“活动”或“备用”路径类型。
12. 重复步骤 8-11 来向目标添加其他会话（路径）。 当主机上有两个接口且虚拟阵列上有两个接口时，可以为每个目标添加总共四个会话。 
    
    ![mpio14](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio14.png)
13. 需要为每个卷（作为目标的面）重复这些步骤。
    
    ![mpio15](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio15.png)
14. 通过导航到“服务器管理器”>“仪表板”>“计算机管理”打开“计算机管理”。 在左窗格中，单击“存储”>“磁盘管理”。 在 StorSimple 虚拟阵列上创建的、对此主机可见的卷将在“磁盘管理”下显示为新磁盘。
15. 初始化磁盘并创建新卷。 在格式化过程中，选择 64 KB 作为分配单元大小 (AUS)。 为所有可用卷重复此过程。
    
    ![磁盘管理](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio20.png)
16. 在“磁盘管理”下，右键单击“磁盘”并选择“属性”。
17. 在“多路径磁盘设备属性”对话框中，单击“MPIO”选项卡。
    
    ![磁盘属性](./media/storsimple-virtual-array-configure-mpio-windows-server/mpio21.png)
18. 在“DSM 名称”部分中，单击“详细信息”并验证各个参数是否设置为默认参数。 默认参数为：
    
    * 路径验证期限 = 30
    * 重试计数 = 3
    * PDO 删除期限 = 20
    * 重试间隔 = 1
    * 已启用路径验证 = 不选中。
    
    > [!NOTE]
    > **不要修改默认参数。**
   
## <a name="next-steps"></a>后续步骤
详细了解如何[使用 StorSimple Device Manager 服务管理 StorSimple 虚拟阵列](storsimple-virtual-array-manager-service-administration.md)。




<!--HONumber=Nov16_HO4-->


