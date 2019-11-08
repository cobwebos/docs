---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491537"
---
1. 找到创建的虚拟 WAN。 在虚拟 WAN 页上的“连接”部分下，选择“中心”   。
2. 在“中心”页上，选择“+ 新建中心”以打开“创建虚拟中心”页   。
3. 在“创建虚拟中心”页上的“基本”选项卡上，请填写以下字段   ：

   ![基础知识](./media/virtual-wan-tutorial-er-hub-include/hub1.png "基础")

    **项目详细信息**

   * 区域（之前称为位置）
   * Name
   * 中心专用地址空间。 用于创建中心的最小地址空间是 /24，这表示在创建过程中从 /25 到 /32 的任何范围都将产生错误。
4. 选择“ExpressRoute 选项卡”  。

5. 在“ExpressRoute 选项卡”上，填写以下字段  ：

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * 选择“是”以创建“ExpressRoute”网关   。
   * 从下拉列表中，选择“网关缩放单元”  。
6. 选择“查看 + 创建”以进行验证  。
7. 选择“创建”以创建中心  。 30 分钟后，“刷新”以在“中心”页上查看该中心   。 选择“转到资源”导航到资源  。