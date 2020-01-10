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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774557"
---
本部分介绍如何对直接对等互连执行以下修改操作：

### <a name="add-exchange-peering-connections"></a>添加 Exchange 对等互连连接

1. 单击顶部的 " **+ 添加连接**" 按钮，并配置新的对等连接。
    > [!div class="mx-imgBorder"]
    > ![对等互连资源视图](../media/setup-exchange-modify-addconnection.png)
1. 填写 Exchange 对**等互连连接**窗体，并单击 "**保存**"。 有关配置对等连接的帮助，请查看上面 "创建和预配直接对等互连" 部分下的步骤。
    > [!div class="mx-imgBorder"]
    > ![对等互连资源视图](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>删除 Exchange 对等互连连接

1. 单击要删除的对等互连连接，然后单击 **...**  > **删除连接**按钮。
    > [!div class="mx-imgBorder"]
    > ![对等互连连接删除](../media/setup-exchange-modify-deleteconnection.png)
1. 将资源 ID 输入到 "**确认删除**" 框中，如突出显示的框中所示，然后单击 "**删除**"。
    > [!div class="mx-imgBorder"]
    > ![对等互连连接 DeleteConfirm](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>在活动连接上添加 IPv4/IPv6 会话

1. 单击要修改的对等互连连接，然后单击 **...**  > **编辑连接**按钮。
    > [!div class="mx-imgBorder"]
    > ![对等互连连接编辑](../media/setup-exchange-modify-editconnection.png)
1. 添加**IPv4 地址**或**IPv6 地址**信息，然后单击 "**保存**"。
    > [!div class="mx-imgBorder"]
    > ![对等互连连接修改](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>删除活动连接上的 IPv4/IPv6 会话

门户当前不支持从现有连接中删除 IPv4/IPv6 会话。 请联系[Microsoft 对等互连](mailto:peeringexperience@microsoft.com)。