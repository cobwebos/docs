---
title: "为 StorSimple 设备配置 MPIO | Microsoft 文档"
description: "介绍如何为连接到运行 Windows Server 2012 R2 的主机的 StorSimple 设备配置多路径 I/O (MPIO)。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 879fd0f9-c763-4fa0-a5ba-f589a825b2df
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/03/2017
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 77f06b6ef146bdea8dc2f6cb920979e827626e35
ms.lasthandoff: 04/06/2017


---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>为 StorSimple 设备配置多路径 I/O
Microsoft 针对 Windows Server 中的多路径 I/O (MPIO) 功能提供内置支持，帮助构建高度可用、可容错的 SAN 配置。 MPIO 使用冗余的物理路径组件（适配器、电缆和交换机）在服务器与存储设备之间创建逻辑路径。 如果发生组件故障，导致某条逻辑路径失败，则多路径逻辑将使用备用路径执行 I/O，以便应用程序仍然可以访问其数据。 另外，根据你的配置，MPIO 还可以通过在这些路径之间重新进行负载平衡来提高性能。 有关详细信息，请参阅 [MPIO 概述](https://technet.microsoft.com/library/cc725907.aspx "MPIO 的概述和功能")。  

为实现 StorSimple 解决方案的高可用性，应在 StorSimple 设备上配置 MPIO。 在运行 Windows Server 2012 R2 的主机服务器上安装 MPIO 后，服务器可以承受链路、网络或接口故障。 

MPIO 是 Windows 服务器上的一项可选功能，默认情况下并未安装。 应该通过服务器管理器将它作为一项功能安装。 本主题介绍在运行 Windows Server 2012 R2 且已连接到 StorSimple 物理设备的主机上安装和使用 MPIO 功能时应该遵循的步骤。

> [!NOTE]
> **此过程仅适用于 StorSimple 8000 系列。StorSimple 虚拟设备目前不支持 MPIO。**
> 
> 

需要执行以下步骤在 StorSimple 设备上配置 MPIO：

* 步骤 1：在 Windows Server 主机上安装 MPIO
* 步骤 2：为 StorSimple 卷配置 MPIO
* 步骤 3：在主机上装载 StorSimple 卷
* 步骤 4：配置 MPIO 以实现高可用性和负载均衡

以下各部分讨论了上述每个步骤。

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>步骤 1：在 Windows Server 主机上安装 MPIO
若要在 Windows Server 主机上安装此功能，请完成以下过程。

#### <a name="to-install-mpio-on-the-host"></a>在主机上安装 MPIO
1. 在 Windows Server 主机上打开服务器管理器。 默认情况下，当管理员组的成员登录到运行 Windows Server 2012 R2 或 Windows Server 2012 的计算机时，服务器管理器便会启动。 如果服务器管理器尚未打开，请依次单击“开始”>“服务器管理器”。
   ![服务器管理器](./media/storsimple-configure-mpio-windows-server/IC740997.png)
2. 依次单击“服务器管理器”>“仪表板”>“添加角色和功能”。 这将启动“添加角色和功能”向导。
   ![添加角色和功能向导 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. 在“添加角色和功能”向导中，执行以下操作：
   
   * 在“开始之前”页上，单击“下一步”。
   * 在“选择安装类型”页上，接受“基于角色或基于功能的安装”的默认设置。 单击“下一步”。![添加角色和功能向导 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   * 在“选择目标服务器”页上，选择“从服务器池中选择服务器”。 主机服务器应该会被自动发现。 单击“资源组名称” 的 Azure 数据工厂。
   * 在“选择服务器角色”页上，单击“下一步”。
   * 在“选择功能”页上，选择“多路径 I/O”，然后单击“下一步”。![添加角色和功能向导 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   * 在“确认安装选择”页上，确认选择，然后选择“如果需要，自动重新启动目标服务器”，如下所示。 单击“安装”。![添加角色和功能向导 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   * 安装完成后，你将收到通知。 单击“关闭”关闭向导。![添加角色和功能向导 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>步骤 2：为 StorSimple 卷配置 MPIO
需要将 MPIO 配置为识别 StorSimple 卷。 若要将 MPIO 配置为识别 StorSimple 卷，请执行以下步骤。

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>为 StorSimple 卷配置 MPIO
1. 打开“MPIO 配置”。 依次单击“服务器管理器”>“仪表板”>“工具”>“MPIO”。
2. 在“MPIO 属性”对话框中，选择“发现多路径”选项卡。
3. 选择“添加对 iSCSI 设备的支持”，然后单击“添加”。  
   ![MPIO 属性 - 发现多路径](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. 出现提示时，重新启动服务器。
5. 在“MPIO 属性”对话框中，单击“MPIO 设备”选项卡。 单击 **“添加”**。
    </br>![MPIO 属性 - MPIO 设备](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. 在“添加 MPIO 支持”对话框中的“设备硬件 ID”下，输入设备序列号。可以通过访问 StorSimple Manager 服务并导航到“设备”>“仪表板”获取设备序列号。 设备序列号显示在设备仪表板的右侧“速览”窗格中。
    </br>![添加 MPIO 支持](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. 出现提示时，重新启动服务器。

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>步骤 3：在主机上装载 StorSimple 卷
在 Windows Server 上配置 MPIO 后，可以装载在 StorSimple 设备上创建的卷，然后可以利用 MPIO 实现冗余。 若要装载卷，请执行以下步骤。

#### <a name="to-mount-volumes-on-the-host"></a>在主机上装载卷
1. 在 Windows Server 主机上打开“iSCSI 发起程序属性”窗口。 依次单击“服务器管理器”>“仪表板”>“工具”>“iSCSI 发起程序”。
2. 在“iSCSI 发起程序属性”对话框中，单击“发现”选项卡，然后单击“发现目标门户”。
3. 在“发现目标门户”对话框中执行以下操作：
   
   * 输入 StorSimple 设备的 DATA 端口的 IP 地址（例如，输入 DATA 0）。
   * 单击“确定”返回到“iSCSI 发起程序属性”对话框。
     
     > [!IMPORTANT]
     > **如果为 iSCSI 连接使用专用网络，请输入连接到专用网络的数据端口的 IP 地址。**
     > 
     > 
4. 针对设备上的第二个网络接口（例如 DATA 1）重复步骤 2-3。 请记住，这些接口应该启用 iSCSI。 若要了解更多相关信息，请参阅[修改网络接口](storsimple-modify-device-config.md#modify-network-interfaces)。
5. 在“iSCSI 发起程序属性”对话框中选择“目标”选项卡。 应会在“发现的目标”下面看到 StorSimple 设备目标 IQN。

   ![iSCSI 发起程序属性 - 目标选项卡](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. 单击“连接”，与 StorSimple 设备建立 iSCSI 会话。 此时将显示“连接到目标”对话框。
7. 在“连接到目标”对话框中，选中“启用多路径”复选框。 单击“高级”。
8. 在“高级设置”对话框中执行以下操作：                                        
   
   * 在“本地适配器”下拉列表中，选择“Microsoft iSCSI 发起程序”。
   * 在“发起程序 IP”下拉列表中，选择主机的 IP 地址。
   * 在“目标门户 IP”下拉列表中，选择设备接口的 IP。
   * 单击“确定”返回到“iSCSI 发起程序属性”对话框。
9. 单击“属性”。 在“属性”对话框中，单击“添加会话”。
10. 在“连接到目标”对话框中，选中“启用多路径”复选框。 单击“高级”。
11. 在“高级设置”对话框中：                                        
    
    * 在“本地适配器”下拉列表中，选择“Microsoft iSCSI 发起程序”。
    * 在“发起程序 IP”下拉列表中，选择与主机对应的 IP 地址。 在本例中，我们要将设备上的两个网络接口连接到主机上的单个网络接口。 因此，此接口与提供给第一个会话的接口相同。
    * 在“目标门户 IP”下拉列表中，选择在设备上启用的第二个数据接口的 IP 地址。
    * 单击“确定”返回到“iSCSI 发起程序属性”对话框。 现已向目标添加第二个会话。
12. 导航到“服务器管理器”>“仪表板”>“计算机管理”，打开“计算机管理”。 在左窗格中，单击“存储”>“磁盘管理”。 在 StorSimple 设备上创建的、对此主机可见的卷将在“磁盘管理”下显示为新磁盘。
13. 初始化磁盘并创建新卷。 在格式化过程中，请选择 64 KB 块大小。
    ![磁盘管理](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. 在“磁盘管理”下，右键单击“磁盘”并选择“属性”。
15. 在 StorSimple 型号 ####“多路径磁盘设备属性”对话框中，单击“MPIO”选项卡。
    ![StorSimple 8100 多路径磁盘设备属性。](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. 在“DSM 名称”部分中，单击“详细信息”并验证各个参数是否设置为默认参数。 默认参数为：
    
    * 路径验证期限 = 30
    * 重试计数 = 3
    * PDO 删除期限 = 20
    * 重试间隔 = 1
    * 已启用路径验证 = 不选中。

> [!NOTE]
> **不要修改默认参数。**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>步骤 4：配置 MPIO 以实现高可用性和负载均衡
为了实现基于多路径的高可用性和负载均衡，必须手动添加多个会话以声明多个可用路径。 例如，如果主机有两个接口连接到 SAN，设备有两个接口连接到 SAN，则需要配置具有正确路径排列的四个会话（如果每个 DATA 接口和主机接口在不同的 IP 子网上并且不可路由，则只需两个会话）。

**建议设备和应用程序主机之间至少具有 8 个活动平行会话。** 这可通过在 Windows Server 系统上启用 4 个网络接口实现。 在 Windows Server 主机上的硬件或操作系统级别使用物理网络接口或网络虚拟化技术。 设备上两个网络接口时，此配置会形成 8 个活动会话。 此配置有助于优化设备和云吞吐量。

> [!IMPORTANT]
> **建议不要将 1 GbE 和 10 GbE 网络接口混合使用。如果要使用两种网络接口，它们的类型应该相同。**

以下过程描述当有两个网络接口的 StorSimple 设备连接到有两个网络接口的主机时，如何添加会话。 仅提供 2 个活动会话。 对于 2 个网络接口连接到具有 4 个网络接口的主机的 StorSimple 设备，采用相同的过程。 需要配置 8 个而不是此处所述的 4 个会话。

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>配置 MPIO 以实现高可用性和负载均衡
1. 执行目标发现：在“iSCSI 发起程序属性”对话框中，单击“发现”选项卡，然后单击“发现门户”。
2. 在“连接到目标”对话框中，输入一个设备网络接口的 IP 地址。
3. 单击“确定”返回到“iSCSI 发起程序属性”对话框。
4. 在“iSCSI 发起程序属性”对话框中，选择“目标”选项卡，突出显示发现的目标，然后单击“连接”。 此时将显示“连接到目标”对话框。
5. 在“连接到目标”对话框中执行以下操作：
   
   * 保留“将此连接添加到收藏目标列表”的默认选定目标设置。 这样，每次重新启动此计算机时，设备就会自动尝试重新启动连接。
   * 选中“启用多路径”复选框。
   * 单击“高级”。
6. 在“高级设置”对话框中执行以下操作：
   
   * 在“本地适配器”下拉列表中，选择“Microsoft iSCSI 发起程序”。
   * 在“发起程序 IP”下拉列表中，选择主机的 IP 地址。
   * 在“目标门户 IP”下拉列表中，选择在设备上启用的数据接口的 IP 地址。
   * 单击“确定”返回到“iSCSI 发起程序属性”对话框。
7. 单击“属性”，然后在“属性”对话框中单击“添加会话”。
8. 在“连接到目标”对话框中，选中“启用多路径”复选框，然后单击“高级”。
9. 在“高级设置”对话框中执行以下操作：
   
   1. 在“本地适配器”下拉列表中，选择“Microsoft iSCSI 发起程序”。
   2. 在“发起程序 IP”下拉列表中，选择与主机上的第二个接口对应的 IP 地址。
   3. 在“目标门户 IP”下拉列表中，选择在设备上启用的第二个数据接口的 IP 地址。
   4. 单击“确定”返回到“iSCSI 发起程序属性”对话框。 现已向目标添加第二个会话。
10. 重复步骤 8-10，向目标添加其他会话（路径）。 主机上有两个接口，设备上也有两个接口，总共可以添加四个会话。
11. 在添加所需的会话（路径）后，在“iSCSI 发起程序属性”对话框中，选择目标并单击“属性”。 在“属性”对话框的“会话”选项卡上，记下与可能的路径排列对应的四个会话标识符。 若要取消会话，请选中会话标识符旁边的复选框，然后单击“断开连接”。
12. 若要查看会话内存在的设备，请选择“设备”选项卡。 若要为所选设备配置 MPIO 策略，请单击“MPIO”。 此时将显示“设备详细信息”对话框。 在“MPIO”选项卡上，可以选择适当的“负载均衡策略”设置。 还可以查看“活动”或“备用”路径类型。

## <a name="next-steps"></a>后续步骤
详细了解如何[使用 StorSimple Manager 服务修改 StorSimple 设备配置](storsimple-modify-device-config.md)。


