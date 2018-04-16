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
ms.openlocfilehash: 9ab0dfac298ab7246935649010100c14dbe9c76a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
1. 在门户中，单击“+创建资源”。
2. 在搜索框中键入“本地网关”，然后按 **Enter** 进行搜索。 这会返回一个结果列表。 单击“本地网关”，然后单击“创建”按钮，打开“创建本地网关”页。

  ![创建本地网关](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "创建本地网关")

3. 在“创建本地网络网关”页上，指定本地网络网关的值。

  - “名称”：指定本地网络网关对象的名称。
  - “IP 地址”：这是 Azure 要连接的 VPN 设备的公共 IP 地址。 指定有效的公共 IP 地址。 IP 地址不能位于 NAT 后面，并且必须可让 Azure 访问。 如果目前没有 IP 地址，可以使用示例中显示的值，但是需要返回并将占位符 IP 地址替换为 VPN 设备的公共 IP 地址。 否则，Azure 不能连接。
  - “地址空间”指的是此本地网络所代表的网络的地址范围。 可以添加多个地址空间范围。 请确保此处所指定的范围没有与要连接到的其他网络的范围相重叠。 Azure 会将指定的地址范围路由到本地 VPN 设备 IP 地址。 如果需要连接到本地站点，请在此处使用自己的值，而不是示例中显示的值。
  - **配置 BGP 设置：**仅在配置 BGP 时使用。 否则，不选择此项。
  - “订阅”：确保显示的是正确订阅。
  - “资源组”：选择要使用的资源组。 可以创建新的资源组或选择已创建的资源组。
  - “位置”：选择将在其中创建此对象的位置。 可选择 VNet 所在的位置，但这不是必须的。

4. 完成指定值后，单击页底部的“创建”按钮即可创建本地网关。