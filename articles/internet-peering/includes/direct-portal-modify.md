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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81681080"
---
本部分介绍如何执行以下修改操作以实现直接对等互连。

### <a name="add-direct-peering-connections"></a>添加直接对等连接
1. 选择 " **+ 添加连接** " 按钮，然后配置新的对等连接。
    > [!div class="mx-imgBorder"]
    > ![对等互连资源视图](../media/setup-direct-modify-addconnection.png)

1. 填写直接对 **等连接** 窗体，并选择 " **保存**"。 有关配置对等互连连接的帮助，请查看 "创建和预配直接对等互连" 部分中的步骤。
    > [!div class="mx-imgBorder"]
    > ![直接对等互连连接窗体](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>删除直接对等连接

Azure 门户当前不支持删除连接。 有关详细信息，请联系 [Microsoft 对等互连](mailto:peeringexperience@microsoft.com)。

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>升级或降级活动连接上的带宽
1. 选择要修改的对等互连连接，然后选择 " **...**  >  "**编辑连接**。
    > [!div class="mx-imgBorder"]
    > ![编辑连接](../media/setup-direct-modify-editconnection.png)

1. 通过移动滑块来修改带宽，然后选择 " **保存**"。
    > [!div class="mx-imgBorder"]
    > ![修改带宽](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>在活动连接上添加 IPv4 或 IPv6 会话信息
1. 选择要修改的对等互连连接，然后选择 " **...**  >  "**编辑连接**，如步骤1中所示。
1. 输入 **会话 IPv4 前缀** 或 **会话 IPv6 前缀** 信息，然后选择 " **保存**"。

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>删除活动连接上的 IPv4 或 IPv6 会话信息
门户目前不支持删除 **会话 IPv4 前缀** 或 **会话 IPv6 前缀** 信息。 有关详细信息，请联系 [Microsoft 对等互连](mailto:peeringexperience@microsoft.com)。
