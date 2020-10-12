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
ms.openlocfilehash: bc9e18427bb3b8094911d2ac7f285d271ecd1a21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025329"
---
1. 在 [Azure 门户](https://portal.azure.com)菜单中，选择“创建资源”  。

   ![资源](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. 在“搜索市场”  字段中，键入“本地网关”  ，然后按 **Enter** 进行搜索。 这会返回一个结果列表。 单击“本地网关”，然后单击“创建”按钮，打开“创建本地网关”页    。

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway-ip.png" alt-text="创建本地网络网关 IP 地址":::

3. 在“创建本地网络网关”页上，指定本地网络网关的值  。

   - **名称：** 指定本地网络网关对象的名称。
   - **终结点：** 为本地 VPN 设备选择终结点类型 - IP 地址或 FQDN（完全限定的域名） 。
      - **IP 地址**：如果你的 Internet 服务提供商向你分配了静态公共 IP 地址可用于 VPN 设备，那么请选择“IP 地址”选项，再填写示例所示的 IP 地址。 这是 Azure VPN 网关要连接的 VPN 设备的公共 IP 地址。 如果目前没有 IP 地址，可以使用示例中显示的值，但是需要返回并将占位符 IP 地址替换为 VPN 设备的公共 IP 地址。 否则，Azure 不能连接。
      - **FQDN：** 如果你有动态公共 IP 地址，且该地址可能会在某段时间（通常由你的 Internet 服务提供商决定）后发生变化，那么你可对动态 DNS 服务使用常量 DNS 名称来指向你的 DNS 设备的当前公共 IP 地址。 你的 Azure VPN 网关将解析 FQDN 来确定要连接到的公共 IP 地址。 下面的屏幕截图显示了一个使用 FQDN 而不是 IP 地址的示例。
   - ****“地址空间”指的是此本地网络所代表的网络的地址范围。 可以添加多个地址空间范围。 请确保此处所指定的范围没有与要连接到的其他网络的范围相重叠。 Azure 会将指定的地址范围路由到本地 VPN 设备 IP 地址。 如果需要连接到本地站点，请在此处使用自己的值，而不是示例中显示的值。**
   - **配置 BGP 设置：** 仅在配置 BGP 时使用。 否则，不选择此项。
   - **订阅：** 确保显示正确的订阅。
   - **资源组：** 选择要使用的资源组。 可以创建新的资源组或选择已创建的资源组。
   - **位置：** 该位置与其他设置中的“区域”相同。 选择将在其中创建此对象的位置。 可选择 VNet 所在的位置，但这不是必须的。

   还是在该页面上，但突出显示了 FQDN：

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-gateway-fqdn.png" alt-text="创建本地网络网关 IP 地址":::

   > [!NOTE]
   >
   > * Azure VPN 支持一个 FQDN 只使用一个 IPv4 地址。 如果域名解析为多个 IP 地址，Azure VPN 网关将使用 DNS 服务器返回的第一个 IP 地址。 为了消除这种不确定性，建议你的 FQDN 始终解析为一个 IPv4 地址。 不支持 IPv6。
   > * Azure VPN 网关维护一个 DNS 缓存，该缓存每 5 分钟刷新一次。 此网关仅尝试解析已断开连接的隧道的 FQDN。 重置网关也将触发 FQDN 解析。
   >

4. 将值指定完以后，选择页面底部的“创建”按钮即可创建本地网关****。
