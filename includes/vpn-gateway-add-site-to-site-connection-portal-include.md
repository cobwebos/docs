---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0dcb591db5f487a103feccf92ffa577a1cbf8b23
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
1. 导航到虚拟网络网关页面并将其打开。 可通过多种方法进行导航。 转到“TestVNet1”- >“概览”- >“连接的设备”- >“VNet1GW”即可导航到网关“VNet1GW”。
2. 在 VNet1GW 的页面上，单击“连接”。 在“连接”页的顶部，单击“+添加”打开“添加连接”页。

  ![创建站点到站点连接](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. 在“添加连接”页上，配置连接的值。

  - “名称”：命名连接。
  - “连接类型”：选择“站点到站点(IPSec)”。
  - “虚拟网络网关”：由于要从此网关连接，因此该值是固定的。
  - “本地网络网关”：单击“选择本地网络网关”并选择要使用的本地网络网关。
  - “共享密钥”：此处的值必须与用于本地 VPN 设备的值匹配。 此示例使用“abc123”，但可以（而且应该）使用更复杂的。 重要的是，此处指定的值必须与配置 VPN 设备时指定的值相同。
  - 剩下的“订阅”、“资源组”和“位置”值是固定的。

4. 单击“确定”以创建连接。 会看到屏幕上闪烁“正在创建连接”。
5. 可在虚拟网络网关的“连接”页中查看连接。 “状态”会从“未知”转换为“正在连接”，再转换为“成功”。