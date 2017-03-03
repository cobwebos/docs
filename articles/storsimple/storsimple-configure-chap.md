---
title: "为 StorSimple 8000 系列设备配置 CHAP | Microsoft 文档"
description: "介绍如何在 StorSimple 设备上配置质询握手身份验证协议 (CHAP)。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 467044d7-7885-4382-90bd-3148dbbd341f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 83dc91972ad5fec85e562e45227747568b1fea75
ms.openlocfilehash: 36b4e73d0336deb9560d44163fc5330d1c9d775c
ms.lasthandoff: 01/26/2017


---
# <a name="configure-chap-for-your-storsimple-device"></a>为 StorSimple 设备配置 CHAP
本教程介绍如何为 StorSimple 设备配置 CHAP。 本文详述的过程适用于 StorSimple 8000 系列和 StorSimple 1200 设备。

CHAP 表示质询握手身份验证协议。 它是服务器用于验证远程客户端身份的身份验证方案。 该验证基于共享密码或机密。 CHAP 可以是单向或相互（双向）的。 单向 CHAP 是指目标对发起程序进行身份验证的情况。 另一方面，相互或反向 CHAP 则要求目标对发起程序进行身份验证，再由发起程序对目标进行身份验证。 发起程序身份验证可以在不进行目标身份验证的情况下实施。 但是，目标身份验证只能在也实施发起程序身份验证的情况下实施。 

建议使用 CHAP 增强 iSCSI 安全性，此为最佳做法。

> [!NOTE]
> 请记住，StorSimple 设备目前不支持 IPSEC。
> 
> 

可以通过以下方式配置 StorSimple 设备上的 CHAP 设置：

* 单向身份验证
* 双向/相互/反向身份验证

每一种此类情况都需配置设备门户和服务器 iSCSI 发起程序软件。 以下教程介绍了此配置的详细步骤。

## <a name="unidirectional-or-one-way-authentication"></a>单向身份验证
在单向身份验证中，目标对发起程序进行身份验证。 此身份验证要求在 StorSimple 设备上配置 CHAP 发起程序设置，在主机上配置 iSCSI 发起程序软件。 接下来介绍针对 StorSimple 设备和 Windows 主机的详细过程。

#### <a name="to-configure-your-device-for-one-way-authentication"></a>配置设备的单向身份验证
1. 在 Azure 经典门户的“设备”页上，单击“配置”选项卡。
   
    ![CHAP 发起程序](./media/storsimple-configure-chap/IC740943.png)
2. 在此页上向下滚动，然后在“CHAP 发起程序”部分执行以下操作：
   
   1. 提供 CHAP 发起程序的用户名。
   2. 提供 CHAP 发起程序的密码。
      
    > [!IMPORTANT]
    > CHAP 用户名包含的字符必须少于 233 个。 CHAP 密码必须介于 12 到 16 个字符之间。 用户名或密码过长会导致在 Windows 主机上进行身份验证失败。
   
   3. 确认该密码。
3. 单击“保存” 。 此时会显示确认消息。 单击“确定”以保存更改  。

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>在 Windows 主机服务器上配置单向身份验证
1. 在 Windows 主机服务器上，启动 iSCSI 发起程序。
2. 在“iSCSI 发起程序属性”窗口中，执行以下步骤：
   
   1. 单击“发现”选项卡。
      
       ![iSCSI 发起程序属性](./media/storsimple-configure-chap/IC740944.png)
   2. 单击“发现门户”。
3. 在“发现目标门户”对话框中执行以下操作：
   
   1. 指定设备的 IP 地址。
   2. 单击“高级”。
      
       ![发现目标门户](./media/storsimple-configure-chap/IC740945.png)
4. 在“高级设置”对话框中执行以下操作：
   
   1. 选中“启用 CHAP 登录”复选框。
   2. 在“名称”字段中，提供在经典门户中为 CHAP 发起程序指定的用户名。
   3. 在“目标机密”字段中，提供在经典门户中为 CHAP 发起程序指定的密码。
   4. 单击 **“确定”**。
      
       ![高级设置常规](./media/storsimple-configure-chap/IC740946.png)
5. 在“iSCSI 发起程序属性”窗口的“目标”选项卡中，设备状态应显示为“已连接”。 如果使用的是 StorSimple 1200 设备，则会将每个卷装载为 iSCSI 目标，如下所示。 因此，需为每个卷重复步骤 3-4。
   
    ![装载为独立目标的卷](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > 如果更改 iSCSI 名称，新的名称将适用于新的 iSCSI 会话。 新的设置不适用于现有会话，除非注销后重新登录。
   > 
   > 

有关在 Windows 主机服务器上配置 CHAP 的详细信息，请转到[其他注意事项](#additional-considerations)。

## <a name="bidirectional-or-mutual-authentication"></a>双向或相互身份验证
在双向身份验证中，目标会对发起程序进行身份验证，然后发起程序也对目标进行身份验证。 这要求用户配置 CHAP 发起程序设置，在设备上配置反向 CHAP 设置，以及在主机上配置 iSCSI 发起程序软件。 以下过程介绍在设备和 Windows 主机上配置相互身份验证的步骤。

#### <a name="to-configure-your-device-for-mutual-authentication"></a>配置设备的相互身份验证
1. 在 Azure 经典门户的“设备”页上，单击“配置”选项卡。
   
    ![CHAP 目标](./media/storsimple-configure-chap/IC740948.png)
2. 在此页上向下滚动，然后在“CHAP 目标”部分执行以下操作：
   
   1. 提供设备的“反向 CHAP 用户名”。
   2. 提供设备的“反向 CHAP 密码”。
   3. 确认该密码。
3. 在“CHAP 发起程序”部分执行以下操作：
   
   1. 提供设备的“用户名”。
   2. 提供设备的“密码”。
   3. 确认该密码。
4. 单击“保存” 。 此时会显示确认消息。 单击“确定”以保存更改  。

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>在 Windows 主机服务器上配置双向身份验证
1. 在 Windows 主机服务器上，启动 iSCSI 发起程序。
2. 在“iSCSI 发起程序属性”窗口中，单击“配置”选项卡。
3. 单击“CHAP”。
4. 在“iSCSI 发起程序相互 CHAP 机密”对话框中执行以下操作：
   
   1. 键入在 Azure 经典门户中配置的“反向 CHAP 密码”。
   2. 单击 **“确定”**。
      
       ![iSCSI 发起程序相互 CHAP 机密](./media/storsimple-configure-chap/IC740949.png)
5. 单击“目标”选项卡。
6. 单击“连接”按钮。 
7. 在“连接到目标”对话框中，单击“高级”。
8. 在“高级属性”对话框中执行以下操作：
   
   1. 选中“启用 CHAP 登录”复选框。
   2. 在“名称”字段中，提供在经典门户中为 CHAP 发起程序指定的用户名。
   3. 在“目标机密”字段中，提供在经典门户中为 CHAP 发起程序指定的密码。
   4. 选择“执行相互身份验证”复选框。
      
       ![高级设置相互身份验证](./media/storsimple-configure-chap/IC740950.png)
   5. 单击“确定”完成 CHAP 配置

有关在 Windows 主机服务器上配置 CHAP 的详细信息，请转到[其他注意事项](#additional-considerations)。

## <a name="additional-considerations"></a>其他注意事项
“快速连接”功能不支持启用了 CHAP 的连接。 启用 CHAP 时，请确保使用“目标”选项卡上提供的“连接”按钮连接到目标。

![连接到目标](./media/storsimple-configure-chap/IC740947.png)

在显示的“连接到目标”对话框中，选中“将此连接添加到收藏目标列表”复选框。 这可确保每次重新启动计算机时，都会尝试还原到 iSCSI 收藏目标的连接。

## <a name="errors-during-configuration"></a>配置期间的错误
如果 CHAP 配置不正确，可能会出现错误消息“身份验证失败”。

## <a name="verification-of-chap-configuration"></a>验证 CHAP 配置
可通过以下步骤验证是否正在使用 CHAP。

#### <a name="to-verify-your-chap-configuration"></a>验证 CHAP 配置
1. 单击“收藏目标”。
2. 选择为其启用了身份验证的目标。
3. 单击“详细信息”。
   
    ![iSCSI 发起程序属性收藏目标](./media/storsimple-configure-chap/IC740951.png)
4. 在“收藏目标详细信息”对话框中，记下“身份验证”字段中的条目。 如果配置成功，则会显示“CHAP”。
   
    ![收藏目标详细信息](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>后续步骤
* 详细了解 [StorSimple 安全性](storsimple-security.md)。
* 了解有关如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)的详细信息。


