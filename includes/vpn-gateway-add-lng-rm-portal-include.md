---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f708052e6c1b474d1447a352d10eb1defe311fc4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "91024966"
---
1. 在门户中，单击“+创建资源”  。
2. 在搜索框中键入“本地网关”，然后按 **Enter** 进行搜索。 这会返回一个结果列表。 单击“本地网关”，然后单击“创建”按钮，打开“创建本地网关”页    。

   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-ip.png" alt-text="创建本地网络网关":::

3. 在“创建本地网络网关”页上，指定本地网络网关的值  。

   - “名称”：指定本地网络网关对象的名称  。
   - **终结点：** 为本地 VPN 设备选择终结点类型- **IP 地址** 或 **FQDN (完全限定的域名) **。
      - **IP 地址**：如果已为 VPN 设备的 Internet 服务提供商分配静态公共 IP 地址，请选择 "ip 地址" 选项并填写 ip 地址，如示例中所示。 这是你希望 Azure VPN 网关连接到的 VPN 设备的公共 IP 地址。 如果目前没有 IP 地址，可以使用示例中显示的值，但是需要返回并将占位符 IP 地址替换为 VPN 设备的公共 IP 地址。 否则，Azure 不能连接。
      - **FQDN**：如果你有一个动态公共 IP 地址，该地址在特定时间段（通常由 Internet 服务提供商确定）之后可能会更改，则可以使用包含动态 dns 服务的常量 dns 名称指向你的 VPN 设备的当前公共 ip 地址。 Azure VPN 网关将解析 FQDN，以确定要连接到的公共 IP 地址。 下面的屏幕截图显示了使用 FQDN 而非 IP 地址的示例。
   - ****“地址空间”指的是此本地网络所代表的网络的地址范围。 可以添加多个地址空间范围。 请确保此处所指定的范围没有与要连接到的其他网络的范围相重叠。 Azure 会将指定的地址范围路由到本地 VPN 设备 IP 地址。 如果需要连接到本地站点，请在此处使用自己的值，而不是示例中显示的值。**
   - **配置 BGP 设置：** 仅在配置 BGP 时使用。 否则，不选择此项。
   - **订阅：** 确保显示正确的订阅。
   - **资源组：** 选择要使用的资源组。 可以创建新的资源组或选择已创建的资源组。
   - **位置：** 选择将在其中创建此对象的位置。 可选择 VNet 所在的位置，但这不是必须的。

    这是同一页，但会突出显示 FQDN：
   
   :::image type="content" source="./media/vpn-gateway-add-lng-rm-portal-include/create-local-gateway-fqdn.png" alt-text="创建本地网络网关-FQDN":::
   
   > [!NOTE]
   >
   > * 对于每个 FQDN，Azure VPN 仅支持一个 IPv4 地址。 如果域名解析为多个 IP 地址，Azure VPN 网关将使用 DNS 服务器返回的第一个 IP 地址。 为了消除不确定性，建议你的 FQDN 始终解析为单个 IPv4 地址。 不支持 IPv6。
   > * Azure VPN 网关维护每5分钟刷新一次 DNS 缓存。 网关仅尝试为断开连接的隧道解析 Fqdn。 重置网关还会触发 FQDN 解析。
   >

4. 完成指定值后，选择 " **创建** " 按钮创建网关。
