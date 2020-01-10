---
title: include 文件
titleSuffix: Azure
description: include 文件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775337"
---
本部分介绍如何对直接对等互连执行以下修改操作：

### <a name="add-direct-peering-connections"></a>添加直接对等连接
1. 单击顶部的 " **+ 添加连接**" 按钮，并配置新的对等连接。
    > [!div class="mx-imgBorder"]
    > ![对等互连资源视图](../media/setup-direct-modify-addconnection.png)
1. 填写**直接对等连接**窗体，并单击 "**保存**"。 有关配置对等连接的帮助，请查看上面 "创建和预配直接对等互连" 部分下的步骤。
    > [!div class="mx-imgBorder"]
    > ![对等互连资源视图](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>删除直接对等连接

门户目前不支持删除连接。 请联系[Microsoft 对等互连](mailto:peeringexperience@microsoft.com)。

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>升级或降级活动连接上的带宽
1. 单击要修改的对等互连连接，然后单击 **...**  > **编辑连接**按钮。
    > [!div class="mx-imgBorder"]
    > ![对等互连连接编辑](../media/setup-direct-modify-editconnection.png)
1. 按如下所示修改带宽，并单击 "**保存**"。
    > [!div class="mx-imgBorder"]
    > ![对等互连修改带宽](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>在活动连接上添加 IPv4/IPv6 会话。
1. 单击要修改的对等互连连接，然后单击 " **...** " > **编辑连接**"按钮，如上所示。
1. 添加**会话 IPv4 前缀**或**会话 IPv6 前缀**信息，然后单击 "**保存**"。

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>删除活动连接上的 IPv4/IPv6 会话。
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
