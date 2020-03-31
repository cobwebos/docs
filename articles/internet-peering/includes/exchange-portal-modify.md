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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774557"
---
本节介绍如何为 Direct 对等互连执行以下修改操作：

### <a name="add-exchange-peering-connections"></a>添加 Exchange 对等互连连接

1. 单击顶部的 **" 添加连接**"按钮并配置新的对等互连连接。
    > [!div class="mx-imgBorder"]
    > ![对等资源视图](../media/setup-exchange-modify-addconnection.png)
1. 填写**Exchange 对等连接**窗体，然后单击"**保存**"。 有关配置对等互连连接的帮助，请查看上面"创建和预配直接对等互连"部分下的步骤。
    > [!div class="mx-imgBorder"]
    > ![对等资源视图](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>删除 Exchange 对等互连连接

1. 单击要删除的对等互连连接，然后单击 **...** > **删除连接**按钮。
    > [!div class="mx-imgBorder"]
    > ![对等连接删除](../media/setup-exchange-modify-deleteconnection.png)
1. 在突出显示的框中的 **"确认删除**"框中输入资源 ID，然后单击"**删除**"。
    > [!div class="mx-imgBorder"]
    > ![对等连接删除确认](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>在活动连接上添加 IPv4/IPv6 会话

1. 单击要修改的对等互连连接，然后单击 **...** > **编辑连接**按钮。
    > [!div class="mx-imgBorder"]
    > ![对等连接编辑](../media/setup-exchange-modify-editconnection.png)
1. 添加**IPv4 地址**或**IPv6 地址**信息，然后单击"**保存**"。
    > [!div class="mx-imgBorder"]
    > ![对等连接修改](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>在活动连接上删除 IPv4/IPv6 会话

门户当前不支持从现有连接中删除 IPv4/IPv6 会话。 联系[微软对等互连](mailto:peeringexperience@microsoft.com)。