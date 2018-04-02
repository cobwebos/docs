---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ea616786d69d41435be2a46e90d4973b21270935
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
1. 导航到虚拟网络网关页面并将其打开。 可通过多种方法进行导航。 在本示例中，通过转到“TestVNet1”- >“概述”- >“连接的设备”- >“VNet1GW” 导航到网关“VNet1GW”。
2. 在 VNet1GW 的页面上，单击“连接”。 在“连接”页的顶部，单击“+添加”打开“添加连接”页。

    ![创建站点到站点连接](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. 在“添加连接”页上，填写用于创建连接的相关值。

  - “名称”：命名连接。 此示例中使用 VNet1toSite2。
  - “连接类型”：选择“站点到站点(IPSec)”。
  - “虚拟网络网关”：由于要从此网关连接，因此该值是固定的。
  - “本地网络网关”：单击“选择本地网络网关”并选择要使用的本地网络网关。 本示例中使用 Site2。
  - “共享密钥”：此处的值必须与用于本地 VPN 设备的值匹配。 在示例中，我们使用的是“abc123”，但可以（而且应该）使用更复杂的。 重要的是，此处指定的值必须与配置 VPN 设备时指定的值相同。
  - 剩下的“订阅”、“资源组”和“位置”值是固定的。

4. 单击“确定”以创建连接。 会看到屏幕上闪烁“正在创建连接”。
5. 可在虚拟网络网关的“连接”页中查看连接。 “状态”会从“未知”转换为“正在连接”，再转换为“成功”。