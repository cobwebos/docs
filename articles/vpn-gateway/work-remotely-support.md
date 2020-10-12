---
title: 使用 P2S：Azure VPN 网关
description: 本页介绍如何在 COVID-19 疫情下利用 VPN 网关完成远程工作。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 028250e25f4d67628c08970e25b4621e78526b6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440827"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>使用 Azure VPN 网关点到站点连接实现远程工作

>[!NOTE]
>本文介绍如何利用 Azure VPN 网关、Azure、Microsoft 网络和 Azure 合作伙伴生态系统远程进行工作，以及如何缓解因 COVID-19（新冠病毒）危机而面临的网络问题。
>

本文将介绍可供组织用来为其用户设置远程访问的选项，或者在 COVID-19 疫情期间通过附加容量补充其现有解决方案的选项。

Azure 点到站点解决方案基于云且可快速预配，以满足在家办公用户的不断增长需求。 可以轻松纵向扩展该解决方案，不再需要多出的容量时，同样可以快速轻松地将其关闭。

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>关于点到站点 VPN

点到站点 (P2S) VPN 网关连接用于创建从单个客户端计算机到虚拟网络的安全连接。 可通过从客户端计算机启动连接来建立 P2S 连接。 对于要从远程位置（例如在家里或会议室）连接到 Azure VNet 或本地数据中心的远程工作者，此解决方案很有用。 本文根据各种场景介绍如何让用户实现远程工作。

下表显示了客户端操作系统及其可用的身份验证选项。 这些信息可以帮助用户根据已在使用的客户端 OS 选择身份验证方法。 例如，如果混合使用了需要连接的客户端操作系统，请选择使用基于证书的身份验证的 OpenVPN。 另请注意，只有基于路由的 VPN 网关才支持点到站点 VPN。

![显示客户端操作系统和可用身份验证选项的屏幕截图。](./media/working-remotely-support/ostable.png "操作系统")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>方案 1 - 用户只需访问 Azure 中的资源

在此场景中，远程用户只需访问 Azure 中的资源。

![此图显示了只需要访问 Azure 中的资源的用户的点到站点方案。](./media/working-remotely-support/scenario1.png "方案 1")

从较高层面讲，需要执行以下步骤才能使用户安全连接到 Azure 资源：

1. 创建虚拟网关（如果该网关不存在）。
2. 在网关上配置点到站点 VPN。

   * 对于证书身份验证，请打开[此链接](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)。
   * 对于 OpenVPN，请打开[此链接](vpn-gateway-howto-openvpn.md)。
   * 对于 Azure AD 身份验证，请打开[此链接](openvpn-azure-ad-tenant.md)。
   * 若要排查点到站点连接问题，请打开[此链接](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
3. 下载并分发 VPN 客户端配置。
4. 向客户端分发证书（如果选择了证书身份验证）。
5. 连接到 Azure VPN。

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>场景 2 - 用户需要访问 Azure 中的资源和/或本地资源

在此场景中，远程用户需要访问 Azure 中以及本地数据中心内的资源。

![此图显示了需要访问 Azure 中的资源的用户的点到站点方案。](./media/working-remotely-support/scenario2.png "方案 2")

从较高层面讲，需要执行以下步骤才能使用户安全连接到 Azure 资源：

1. 创建虚拟网关（如果该网关不存在）。
2. 在网关上配置点到站点 VPN（请参阅[方案 1](#scenario1)）。
3. 在启用了 BGP 的 Azure 虚拟网关上配置站点到站点隧道。
4. 配置本地设备以连接到 Azure 虚拟网关。
5. 从 Azure 门户下载点到站点配置文件并将其分发到客户端

若要了解如何设置站点到站点 VPN 隧道，请参阅[此链接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>本机 Azure 证书身份验证常见问题解答

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>RADIUS 身份验证常见问题解答

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>后续步骤

* [配置 P2S 连接 - Azure AD 身份验证](openvpn-azure-ad-tenant.md)

* [配置 P2S 连接 - RADIUS 身份验证](point-to-site-how-to-radius-ps.md)

* [配置 P2S 连接 - Azure 本机证书身份验证](vpn-gateway-howto-point-to-site-rm-ps.md)

**“OpenVPN”是 OpenVPN Inc. 的商标。**