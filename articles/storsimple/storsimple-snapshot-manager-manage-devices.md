---
title: "使用 StorSimple Snapshot Manager 管理设备 | Microsoft Docs"
description: "介绍如何使用 StorSimple Snapshot Manager MMC 管理单元连接和管理 StorSimple 设备。"
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: f5e3186a4271e0be781f367fa75ada195c58c960
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>使用 StorSimple Snapshot Manager 连接和管理 StorSimple 设备
## <a name="overview"></a>概述
可以使用 StorSimple Snapshot Manager“作用域”窗格中的节点，验证导入的 StorSimple 设备数据并刷新已连接的存储设备。 此外，单击“设备”节点时，可以在“结果”窗格中看到已连接设备的列表和相应的状态信息。

![已连接的设备](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**图 1：StorSimple Snapshot Manager 已连接的设备** 

根据“**查看**”选择，“**结果**”窗格中会显示有关每个设备的以下信息。 有关配置视图的详细信息，请转到 [“查看”菜单](storsimple-use-snapshot-manager.md#view-menu)。

| 结果列 | 说明 |
|:--- |:--- |
| Name |在 Azure 经典门户中配置的设备的名称 |
| 模型 |设备的型号 |
| 版本 |在设备上安装的软件的版本 |
| 状态 |设备是否可用 |
| 上次同步时间 |上次同步设备时的日期和时间 |
| 序列号 |设备的序列号 |

如果在“作用域”窗格中右键单击“设备”节点，则可以选择以下操作：

* 添加或替换设备
* 连接设备并验证导入
* 刷新连接的设备

如果单击“**设备**”节点，并在“**结果**”窗格中右键单击设备名称，则可以选择以下操作：

* 对设备进行身份验证
* 查看设备详细信息
* 刷新设备
* 删除设备配置
* 更改设备密码

> [!NOTE]
> “操作”窗格中也提供了所有这些操作。


本教程介绍如何使用 StorSimple Snapshot Manager 连接和管理设备并执行以下任务：

* 添加或替换设备
* 连接设备并验证导入
* 刷新连接的设备
* 对设备进行身份验证
* 查看设备详细信息
* 刷新单个设备
* 删除设备配置
* 更改过期的设备密码
* 替换发生故障的设备

> [!NOTE]
> 有关使用 StorSimple Snapshot Manager 界面的常规信息，请转到 [StorSimple Snapshot Manager 用户界面](storsimple-use-snapshot-manager.md)。


## <a name="add-or-replace-a-device"></a>添加或替换设备
使用以下过程添加或替换 StorSimple 设备。

#### <a name="to-add-or-replace-a-device"></a>添加或替换设备
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“作用域”窗格中，右键单击“设备”节点，并单击“配置设备”。 “配置设备”对话框出现。
   
    ![配置 StorSimple 设备](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. 在“设备”下拉框中，选择设备或虚拟设备的 IP 地址。 
4. 在“密码”文本框中，键入为 Azure 经典门户中的设备创建的 StorSimple Snapshot Manager 密码。 单击“确定”。 StorSimple Snapshot Manager 搜索你所标识的设备。 
   
   * 如果设备可用，则 StorSimple Snapshot Manager 会添加一个连接。
   * 如果设备由于任何原因而不可用，则 StorSimple Snapshot Manager 会返回一条错误消息。 单击“**确定**”关闭错误消息，并单击“**取消**”关闭“**配置设备**”对话框。

## <a name="connect-a-device-and-verify-imports"></a>连接设备并验证导入
使用以下过程连接 StorSimple 设备并验证是否已导入具有关联备份的任何现有卷组。

#### <a name="to-connect-a-device-and-verify-imports"></a>连接设备并验证导入
1. 要将设备连接到 StorSimple Snapshot Manager，请按照“添加或替换设备”中的说明进行操作。 连接到设备后，StorSimpleSnapshot Manager 会做出如下响应：
   
   * 如果设备由于任何原因而不可用，则 StorSimple Snapshot Manager 会返回一条错误消息。 
   
   * 如果设备可用，则 StorSimple Snapshot Manager 会添加一个连接。 选择该设备后，它会显示在“结果”窗格中，而且状态字段指示该设备为“可用”。 StorSimple Snapshot Manager 会导入为该设备配置的任何卷组，前提是这些卷组具有关联的备份。 备份策略将不导入。 没有关联备份的卷组也不导入。
2. 单击桌面图标启动 StorSimple Snapshot Manager。
3. 右键单击“作用域”窗格中的顶级节点，并单击“切换导入显示”。
   
    ![选择“切换导入显示”](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. 此时会出现“切换导入显示”对话框，其中显示导入的卷组和备份的状态。 单击“确定”。

成功导入卷组和备份后，可以使用 StorSimple Snapshot Manager 对其进行管理，就像管理使用 StorSimple Snapshot Manager 创建和配置的卷组和备份一样。 

## <a name="refresh-connected-devices"></a>刷新连接的设备
使用以下过程同步连接的 StorSimple 设备与 StorSimple Snapshot Manager。

#### <a name="to-refresh-connected-devices"></a>刷新连接的设备
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，右键单击“**设备**”，并单击“**刷新设备**”。 这会同步连接的 StorSimple 设备和 StorSimple Snapshot Manager，以便查看卷组和备份，包括任何最近的添加件。 
   
    ![刷新 StorSimple 设备](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

“刷新设备”操作从连接的设备中检索任何新卷组和任何关联的备份。 与可用于“卷”节点的“重新扫描卷”操作不同，“刷新设备”不还原备份注册表。

## <a name="authenticate-a-device"></a>对设备进行身份验证
使用以下过程通过 StorSimple Snapshot Manager 对连接的 StorSimple 设备进行身份验证。

#### <a name="to-authenticate-a-device"></a>对设备进行身份验证
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“作用域”窗格中，单击“设备”。
3. 在“**结果**”窗格中，右键单击该设备名称，并单击“**身份验证**”。
4. “身份验证”对话框随即出现。 键入设备密码，并单击“确定”。
   
    ![“身份验证”对话框](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>查看设备详细信息
使用以下过程查看 StorSimple 设备的详细信息，如有必要，重新同步设备与 StorSimple Snapshot Manager。

#### <a name="to-view-and-resynchronize-device-details"></a>查看和重新同步设备详细信息
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“作用域”窗格中，单击“设备”。
3. 在“**结果**”窗格中，右键单击该设备的名称，并单击“**详细信息**”。

4.“设备详细信息”对话框随即出现。 此框会显示名称、型号、版本、序列号、状态、目标 iSCSI 限定名称 (IQN)，以及上次同步的日期和时间。

* 单击“重新同步”以同步该设备。
* 单击“确定”或“取消”以关闭该对话框。
  
  ![设备详细信息](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>刷新单个设备
使用以下过程重新同步单个 StorSimple 设备与 StorSimple Snapshot Manager。

#### <a name="to-refresh-a-device"></a>刷新设备
1. 单击桌面图标启动 StorSimple Snapshot Manager。 
2. 在“作用域”窗格中，单击“设备”。 
3. 在“结果”窗格中，右键单击该设备名称，并单击“刷新设备”。 这会同步该设备与 StorSimple Snapshot Manager。

## <a name="delete-a-device-configuration"></a>删除设备配置
使用以下过程从 StorSimple Snapshot Manager 中删除单个 StorSimple 设备配置。

#### <a name="to-delete-a-device-configuration"></a>删除设备配置
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“作用域”窗格中，单击“设备”。 
3. 在“结果”窗格中，右键单击该设备名称，并单击“删除”。 
4. 此时会显示以下消息。 单击“是”以删除该配置，或者单击“否”以取消删除。
   
    ![删除设备配置](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>更改过期的设备密码
必须输入密码才能使用 StorSimple Snapshot Manager 对 StorSimple 设备进行身份验证。 使用 Windows PowerShell 界面设置设备时，可以配置此密码。 但是，该密码会过期。 如果发生这种情况，可以使用 Azure 经典门户更改密码。 然后，由于之前的密码过期，则该设备已配置 StorSimple 快照管理器中，你必须重新进行身份验证设备 StorSimple 快照管理器中。

#### <a name="to-change-the-expired-password"></a>更改过期的密码
1. 在 Azure 经典门户中，启动 StorSimple Manager 服务。
2. 针对该设备单击“设备” > “配置”。
3. 向下滚动到 StorSimple Snapshot Manager 部分。 输入包含 14-15 个字符的密码。 请确保该密码包含大写、小写字母、数字和特殊字符的组合。
4. 再次输入密码进行确认。
5. 单击页面底部的“保存”。

#### <a name="to-re-authenticate-the-device"></a>重新对设备进行身份验证
1. 启动 StorSimple Snapshot Manager。
2. 在“作用域”窗格中，单击“设备”。 已配置设备的列表会显示在“结果”窗格中。
3. 选择设备，右键单击，并单击“**身份验证**”。
4. 在“身份验证”窗口中，输入新密码。
5. 选择设备，右键单击，并选择“**刷新设备**”。 这会同步该设备与 StorSimple Snapshot Manager。

## <a name="replace-a-failed-device"></a>替换发生故障的设备
如果 StorSimple 设备发生故障而且已由备用（故障转移）设备替换，请使用以下步骤连接到新设备并查看关联的备份。

#### <a name="to-connect-to-a-new-device-after-failover"></a>在故障转移后连接到新设备
1. 重新配置到新设备的 iSCSI 连接。 有关说明，请转到[部署本地 StorSimple 设备](storsimple-8000-deployment-walkthrough-u2.md)中的“步骤 7：装载、初始化和格式化卷”。

> [!NOTE]
> 如果新 StorSimple 设备与旧设备具有相同的 IP 地址，则可以连接旧配置。


1. 停止 Microsoft StorSimple Management Service：
   
   1. 启动服务器管理器。
   2. 在服务器管理器仪表板的“工具”菜单上，选择“服务”。
   3. 在“服务”窗口中，选择“Microsoft StorSimple Management Service”。
   4. 在右窗格的“Microsoft StorSimple Management Service”下，单击“停止服务”。
2. 删除与旧设备相关的配置信息：
   
   1. 在文件资源管理器中，浏览到 C:\ProgramData\Microsoft\StorSimple\BACatalog。
   2. 删除 BACatalog 文件夹中的文件。
3. 重新启动 Microsoft StorSimple Management Service：
   
   1. 在服务器管理器仪表板的“工具”菜单上，选择“服务”。
   2. 在“服务”窗口中，选择“Microsoft StorSimple Management Service”。
   3. 在右窗格的“Microsoft StorSimple Management Service”下，单击“重新启动服务”。
4. 启动 StorSimple Snapshot Manager。
5. 若要配置新 StorSimple 设备，请完成[部署 StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) 中“第 2 步：连接 StorSimple 设备”中的步骤。
6. 右键单击“**作用域**”窗格中的顶级节点（示例中为 StorSimple Snapshot Manager），并单击“**切换导入显示**”。 
7. 当导入的卷组和备份在 StorSimple Snapshot Manager 中可见时，则会出现一条消息。 单击“确定”。

## <a name="next-steps"></a>后续步骤
* 了解如何[使用 StorSimple Snapshot Manager 管理 StorSimple 解决方案](storsimple-snapshot-manager-admin.md)。
* 了解如何[使用 StorSimple Snapshot Manager 查看和管理卷](storsimple-snapshot-manager-manage-volumes.md)。

