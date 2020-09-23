---
title: VPN 网关：修改网关 IP 地址设置： Azure 门户
description: 本文介绍了如何使用 Azure 门户更改本地网络网关的 IP 地址前缀。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983188"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>使用 Azure 门户修改本地网络网关设置

有时本地网络网关 AddressPrefix 或 GatewayIPAddress 的设置会变更。 本文演示如何修改本地网络网关设置。 还可以选择以下列表中的其他选项，使用另一种方法来修改这些设置：

在删除连接之前，可能需要下载用于连接设备的配置，以便获取已定义的 PSK。 这样，便无需在另一端重新定义它。

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>本地网络网关配置

下面的屏幕截图显示了使用公共 IP 地址终结点的本地网络网关资源的 **配置** 页：

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="配置本地网络网关-IP 地址":::

此配置页与 FQDN 终结点相同：

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="配置本地网络网关-FQDN":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>修改网关 IP 地址

如果要连接的 VPN 设备已更改其公共 IP 地址，则需根据该更改修改本地网关。

1. 在“本地网络网关”资源的“设置”**** 部分，单击“配置”****。
2. 在“IP 地址”**** 框中，修改 IP 地址。
3. 单击“保存”**** 保存这些设置。

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>修改网关 FQDN

如果要连接到的 VPN 设备已更改其 FQDN (完全限定的域名) ，则需要修改本地网络网关以反映此更改。

1. 在“本地网络网关”资源的“设置”**** 部分，单击“配置”****。
2. 在 " **FQDN** " 框中，修改域名。
3. 单击“保存”**** 保存这些设置。

> !纪录不能在 FQDN 终结点和 IP 地址终结点之间更改本地网络网关。 必须删除与此本地网络网关关联的所有连接，使用新终结点 (IP 地址或 FQDN) 创建新的连接，然后重新创建连接。

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>修改 IP 地址前缀

### <a name="to-add-additional-address-prefixes"></a>添加其他地址前缀：

1. 在“本地网络网关”资源的“设置”**** 部分，单击“配置”****。
2. 在“添加更多地址范围”** 框中添加 IP 地址空间。
3. 单击“保存”**** 以保存设置。

### <a name="to-remove-address-prefixes"></a>删除地址前缀：

1. 在“本地网络网关”资源的“设置”**** 部分，单击“配置”****。
2. 在包含要删除的前缀的行上，单击“...”****。
3. 单击 **“删除”** 。
4. 单击“保存”**** 以保存设置。

## <a name="modify-bgp-settings"></a><a name="bgp"></a>修改 BGP 设置

### <a name="to-add-or-update-bgp-settings"></a>添加或更新 BGP 设置：

1. 在“本地网络网关”资源的“设置”**** 部分，单击“配置”****。
2. 选择 **"配置 BGP 设置"** 以显示或更新此本地网络网关的 BGP 配置
3. 在相应的字段中添加或更新自治系统编号或 BGP 对等节点 IP 地址
4. 单击“保存”**** 以保存设置。

### <a name="to-remove-bgp-settings"></a>删除 BGP 设置：

1. 在“本地网络网关”资源的“设置”**** 部分，单击“配置”****。
2. 取消选中 **"配置 BGP 设置"** 以删除现有的 bgp ASN 和 BGP 对等节点 IP 地址
3. 单击“保存”**** 以保存设置。

## <a name="next-steps"></a>后续步骤

可验证网关连接。 请参阅[验证网关连接](vpn-gateway-verify-connection-resource-manager.md)。
