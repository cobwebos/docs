---
title: VPN 网关：修改网关 IP 地址设置： Azure 门户
description: 本文介绍了如何使用 Azure 门户更改本地网络网关的 IP 地址前缀。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: 3a59f618536d44e838bf840264e70b0b2a43cced
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014909"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>使用 Azure 门户修改本地网络网关设置

有时本地网络网关 AddressPrefix 或 GatewayIPAddress 的设置会变更。 本文演示如何修改本地网络网关设置。 还可以通过选择以下列表中的其他选项，使用另一种方法来修改这些设置：

在删除连接之前，可能需要下载用于连接设备的配置，以便获取已定义的 PSK。 这样，便无需在另一端重新定义它。

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>修改 IP 地址前缀

修改 IP 地址前缀时，执行的步骤取决于本地网络网关是否具有连接。

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>修改网关 IP 地址

如果要连接的 VPN 设备已更改其公共 IP 地址，则需根据该更改修改本地网关。 更改公共 IP 地址时，执行的步骤取决于本地网络网关是否具有连接。

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>后续步骤

可验证网关连接。 请参阅[验证网关连接](vpn-gateway-verify-connection-resource-manager.md)。
