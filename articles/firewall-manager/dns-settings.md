---
title: Azure 防火墙策略 DNS 设置（预览）
description: 可以使用 DNS 服务器和 DNS 代理设置来配置 Azure 防火墙策略。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: bf189e4c9853d9f2ff6e8495423f4f36f14f41d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85611792"
---
# <a name="azure-firewall-policy-dns-settings-preview"></a>Azure 防火墙策略 DNS 设置（预览）

> [!IMPORTANT]
> Azure 防火墙 DNS 设置目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以为 Azure 防火墙策略配置自定义 DNS 服务器并启用 DNS 代理。 可以在部署防火墙时配置这些设置，或者稍后从“DNS 设置”页进行配置。

## <a name="dns-servers"></a>DNS 服务器

DNS 服务器维护域名并将它解析为 IP 地址。 默认情况下，Azure 防火墙将 Azure DNS 用于名称解析。 通过“DNS 服务器”设置，你可以配置自己的 DNS 服务器来用于 Azure 防火墙名称解析。 可以配置一台或多台服务器。

### <a name="configure-custom-dns-servers"></a>配置自定义 DNS 服务器

1. 选择你的防火墙策略。
2. 在“设置”下，选择“DNS 设置” 。
3. 在“DNS 服务器”下，可以键入或添加之前在虚拟网络中指定的现有 DNS 服务器。
4. 选择“保存” 。
5. 防火墙现在将 DNS 流量定向到指定的 DNS 服务器以进行名称解析。

## <a name="dns-proxy-preview"></a>DNS 代理（预览）

可以对 Azure 防火墙进行配置来充当 DNS 代理。 DNS 代理充当从客户端虚拟机到 DNS 服务器的 DNS 请求的中介。 如果配置自定义 DNS 服务器，应启用 DNS 代理以避免 DNS 解析不匹配，并在网络规则中启用 FQDN 筛选。

如果未启用 DNS 代理，来自客户端的 DNS 请求可能会在不同的时间传到 DNS 服务器，或者返回与防火墙不同的响应。 DNS 代理将 Azure 防火墙放置在客户端请求的路径中以避免不一致。

配置 DNS 代理需要三个步骤：
1. 在 Azure 防火墙 DNS 设置中启用 DNS 代理。
2. （可选）配置自定义 DNS 服务器或使用提供的默认设置。
3. 最后，必须在虚拟网络 DNS 服务器设置中将 Azure 防火墙的专用 IP 地址配置为自定义 DNS 地址。 这可确保 DNS 流量定向到 Azure 防火墙。

### <a name="configure-dns-proxy-preview"></a>配置 DNS 代理（预览）

若要配置 DNS 代理，必须将你的虚拟网络 DNS 服务器设置配置为使用防火墙专用 IP 地址。 然后，在 Azure 防火墙策略“DNS 设置”中启用 DNS 代理。

#### <a name="configure-virtual-network-dns-servers"></a>配置虚拟网络 DNS 服务器

1. 选择 DNS 流量将通过 Azure 防火墙路由的虚拟网络。
2. 在“设置”下，选择“DNS 服务器”。 
3. 在“DNS 服务器”下，选择“自定义” 。
4. 输入防火墙的专用 IP 地址。
5. 选择“保存” 。

#### <a name="enable-dns-proxy-preview"></a>启用 DNS 代理（预览）

1. 选择你的 Azure 防火墙策略。
2. 在“设置”下，选择“DNS 设置” 。
3. 默认情况下，“DNS 代理”已禁用。 启用后，防火墙会在端口 53 上进行侦听，并将 DNS 请求转发到配置的 DNS 服务器。
4. 查看“DNS 服务器”配置以确保设置适用于你的环境。
5. 选择“保存”。

## <a name="next-steps"></a>后续步骤

[网络规则中的 FQDN 筛选](fqdn-filtering-network-rules.md)