---
title: 远程使用 P2S：Azure VPN 网关
description: 本页介绍如何利用 Azure 堡垒启用由于 COVID-19 大流行而远程工作。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: alzam
ms.openlocfilehash: b751817467bd263e8b7c64ccc95ec82ef1579836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337106"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>使用 Azure VPN 网关点对点的远程工作

>[!NOTE]
>本文介绍如何利用 Azure VPN 网关、Azure、Microsoft 网络和 Azure 合作伙伴生态系统远程工作并缓解因 COVID-19 危机而面临的网络问题。
>

本文介绍了组织在 COVID-19 流行期间为用户设置远程访问或以额外容量补充其现有解决方案的选项。

Azure 点对点解决方案基于云，可以快速调配，以满足用户在家工作日益增长的需求。 当不再需要增加的容量时，它可以轻松、快速地扩展并关闭。

## <a name="about-point-to-site-vpn"></a>关于点到站点 VPN

点到站点 (P2S) VPN 网关连接用于创建从单个客户端计算机到虚拟网络的安全连接。 可通过从客户端计算机启动连接来建立 P2S 连接。 此解决方案对于希望从远程位置（如从家庭或会议）连接到 Azure VNet 或本地数据中心的远程办公人员非常有用。 本文介绍如何使用户能够根据各种方案远程工作。

下表显示了客户端操作系统及其可用的身份验证选项。 根据已在使用的客户端操作系统选择身份验证方法会很有帮助。 例如，如果您有需要连接的客户端操作系统的混合，请选择具有基于证书的身份验证的 OpenVPN。 此外，请注意，点对站点 VPN 仅在基于路由的 VPN 网关上受支持。

![点对点](./media/working-remotely-support/ostable.png "OS")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a>方案 1 - 用户仅需要访问 Azure 中的资源

在这种情况下，远程用户只需要访问 Azure 中的资源。

![点对点](./media/working-remotely-support/scenario1.png "斯卡纳里奥 1")

在高级别上，需要以下步骤才能使用户能够安全地连接到 Azure 资源：

1. 创建虚拟网络网关（如果不存在）
2. 在网关上配置点对点 VPN
    3. [对于证书身份验证，请按照此链接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw)操作 。
    2.  [对于 OpenVPN，请按照此链接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn)操作 。
    3.  [对于 Azure AD 身份验证，请按照此链接](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-tenant)操作。
    4.  [要对点对点连接进行故障排除，请按照此链接进行](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)。
3. 下载并分发 VPN 客户端配置
4. 将证书（如果选择证书身份验证）分发到客户端
5. 连接到 Azure VPN

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a>方案 2 - 用户需要访问 Azure 和/或预置资源中的资源

在这种情况下，远程用户需要访问 Azure 和内部本地数据中心中的资源。

![点对点](./media/working-remotely-support/scenario2.png "方案 2")

在高级别上，需要以下步骤才能使用户能够安全地连接到 Azure 资源：

1. 创建虚拟网络网关（如果不存在）
2. 在网关上配置点对点 VPN（请参阅上述方案 1）
3. 在启用了 BGP 的 Azure 虚拟网络网关上配置站点到站点隧道
4. 配置本地设备以连接到 Azure 虚拟网络网关
5. 从 Azure 门户下载点对点配置文件并分发给客户端

[请按照此链接了解如何设置站点到站点 VPN 隧道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>本机 Azure 证书身份验证常见问题解答

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>RADIUS 身份验证常见问题解答

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>后续步骤

* [配置 P2S 连接 - Azure AD 身份验证](openvpn-azure-ad-tenant.md)

* [配置 P2S 连接 - RADIUS 身份验证](point-to-site-how-to-radius-ps.md)

* [配置 P2S 连接 - Azure 本机证书身份验证](vpn-gateway-howto-point-to-site-rm-ps.md)

**“OpenVPN”是 OpenVPN Inc. 的商标。**