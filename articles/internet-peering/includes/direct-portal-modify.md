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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681080"
---
本节介绍如何为 Direct 对等互连执行以下修改操作。

### <a name="add-direct-peering-connections"></a>添加直接对等互连连接
1. 选择 **" 添加连接**"按钮，然后配置新的对等互连连接。
    > [!div class="mx-imgBorder"]
    > ![对等资源视图](../media/setup-direct-modify-addconnection.png)

1. 填写**直接对等连接**窗体，然后选择 **"保存**"。 有关配置对等互连连接的帮助，请查看"创建和预配直接对等互连"部分中的步骤。
    > [!div class="mx-imgBorder"]
    > ![直接对等连接窗体](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>删除直接对等互连连接

Azure 门户当前不支持删除连接。 有关详细信息，请与[微软对等互连](mailto:peeringexperience@microsoft.com)。

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>在活动连接上升级或降级带宽
1. 选择要修改的对等互连连接，然后选择 **...** > **编辑连接**。
    > [!div class="mx-imgBorder"]
    > ![编辑连接](../media/setup-direct-modify-editconnection.png)

1. 通过移动滑块修改带宽，然后选择 **"保存**"。
    > [!div class="mx-imgBorder"]
    > ![修改带宽](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>在活动连接上添加 IPv4 或 IPv6 会话信息
1. 选择要修改的对等互连连接，然后选择 **...** > **编辑连接**，如步骤 1 所示。
1. 输入**会话 IPv4 前缀**或**会话 IPv6 前缀**信息，然后选择 **"保存**"。

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>删除活动连接上的 IPv4 或 IPv6 会话信息
门户当前不支持删除**会话 IPv4 前缀**或**会话 IPv6 前缀**信息。 有关详细信息，请与[微软对等互连](mailto:peeringexperience@microsoft.com)。
