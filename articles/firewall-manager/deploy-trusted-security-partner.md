---
title: 部署 Azure 防火墙管理器安全合作伙伴提供程序
description: 了解如何使用 Azure 门户部署 Azure 防火墙管理器安全合作伙伴提供程序。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 3323f73c137905fbe677c68d3830d7f609fa0172
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85611571"
---
# <a name="deploy-a-security-partner-provider"></a>部署安全合作伙伴提供程序

使用 Azure 防火墙管理器中的*安全合作伙伴提供程序*，可以使用熟悉的、同类最佳的第三方安全即服务 (SECaaS) 产品，为用户保护 Internet 访问。

要详细了解支持的场景和最佳做法指南，请参阅[什么是安全合作伙伴提供程序？](trusted-security-partners.md)


集成的第三方安全即服务 (SECaaS) 合作伙伴现已在所有 Azure 公有云区域中提供。 Zscaler 集成将于 2020 年 7 月 3 日正式发布。 Check Point 是受支持的 SECaaS 合作伙伴，将于 2020 年 7 月 3 日提供预览版。 iboss 集成将于 2020 年 7 月 31 日正式发布。

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>在新中心部署第三方安全提供程序

如果要将第三方提供程序部署到现有中心，请跳过此部分。

1. 通过 https://portal.azure.com 登录到 Azure 门户。
2. 在“搜索”中键入“防火墙管理器”，然后在“服务”下选择“防火墙管理器”  。
3. 导航到“入门”。 选择“查看安全虚拟中心”。
4. 选择“新建安全虚拟中心”。
5. 输入你的订阅和资源组，选择受支持的区域，并添加中心和虚拟 WAN 信息。 
6. 选择“包含 VPN 网关以启用安全合作伙伴提供程序”。
7. 根据要求选择适当的“网关缩放单元”。
8. 在完成时选择“下一步:Azure 防火墙”
   > [!NOTE]
   > 安全合作伙伴提供程序使用 VPN 网关隧道连接到你的中心。 如果删除 VPN 网关，与安全合作伙伴提供程序的连接将会丢失。
9. 如果要部署 Azure 防火墙来筛选专用流量，并部署第三方服务提供程序来筛选 Internet 流量，请选择 Azure 防火墙的策略。 请参阅[支持的场景](trusted-security-partners.md#key-scenarios)。
10. 如果只想在中心部署第三方安全提供程序，请选择“Azure 防火墙:启用/禁用”，并将其设置为“禁用”。 
11. 选择“下一步:安全合作伙伴提供程序”。
12. 将“安全合作伙伴提供程序”设置为“启用” 。 
13. 选择一个合作伙伴。 
14. 在完成时选择“下一步:查看 + 创建”。 
15. 查看内容，然后选择“创建”。

部署 VPN 网关可能需要超过 30 分钟的时间。

要验证是否已创建中心，请导航到“Azure 防火墙管理器”->“安全中心”。 选择“中心”>“概述页面”，以显示合作伙伴名称，状态为“安全连接挂起”。

创建中心并设置安全合作伙伴后，请继续将安全提供程序连接到中心。

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>在现有中心部署第三方安全提供程序

还可以选择虚拟 WAN 中的现有中心，并将其转换为安全虚拟中心。

1. 在“入门”中，选择“查看安全虚拟中心” 。
2. 选择“转换现有中心”。
3. 选择订阅和现有中心。 按照剩余步骤在新中心部署第三方安全提供程序。

请记住，必须部署 VPN 网关才能使用第三方提供程序将现有中心转换为安全中心。

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>配置第三方安全提供程序以连接到安全中心

要设置连接虚拟中心 VPN 网关的隧道，第三方提供程序需要具有对中心的访问权限。 为此，请将服务主体与订阅或资源组关联，并授予访问权限。 然后，必须使用第三方的门户将这些凭据分配给第三方。

### <a name="create-and-authorize-a-service-principal"></a>创建服务主体并为其授权

1. 创建 Azure Active Directory (AD) 服务主体：可以跳过重定向 URL。 

   [如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. 为服务主体添加访问权限和作用域。
   [如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > 可以将访问权限限制为仅针对你的资源组，以便进行更精细的控制。

### <a name="visit-partner-portal"></a>访问合作伙伴门户

1. 按照合作伙伴提供的说明完成设置。 这包括提交 AAD 信息以检测并连接到中心、更新出口策略，以及检查连接状态和日志。

   - [Zscaler：配置 Microsoft Azure 虚拟 WAN 集成](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration)。
   - [Check Point（预览版）：配置 Microsoft Azure 虚拟 WAN 集成](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm)。
   - [iboss（预览版）：配置 Microsoft Azure 虚拟 WAN 集成](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security)。 
   
2. 可以在 Azure 的 Azure 虚拟 WAN 门户中查看隧道创建状态。 Azure 门户和合作伙伴门户中的隧道状态均显示“已连接”后，请继续执行后续步骤，以设置路由，并选择哪些分支和 Vnet 应将 Internet 流量发送到合作伙伴。

## <a name="configure-route-settings"></a>配置路由设置

1. 浏览到“Azure 防火墙管理器”->“安全中心”。 
2. 选择一个中心。 现在，中心状态应显示“已预配”，而不是“安全连接挂起” 。

   请确保第三方提供程序可以连接到中心。 VPN 网关上的隧道应处于“已连接”状态。 与之前的状态相比，此状态更能反映中心与第三方合作伙伴之间的连接运行状况。
3. 选择中心，并导航到“路由设置”。

   向中心部署第三方提供程序时，该第三方提供程序会将该中心转换为安全虚拟中心。 这可确保第三方提供程序向中心播发 0.0.0.0/0（默认）路由。 但是，除非你选择了哪些连接应获得此默认路由，否则连接到中心的 VNet 连接和站点不会获得此路由。
4. 在“Internet 流量”下，选择“VNet 到 Internet”和/或“分支到 Internet”，以便将路由配置为通过第三方发送  。

   这仅指示应将那种类型的流量路由到中心，但还不会影响 VNet 或分支上的路由。 默认情况下，这些路由不会传播到附加到中心的所有 Vnet/分支。
5. 必须选择“安全连接”，并选择应在哪些连接上设置这些路由。 这指示哪些 Vnet/分支可以开始向第三方提供程序发送 Internet 流量。
6. 在“路由设置”中，选择“Internet 流量”下的“安全连接”，然后选择要保护的 VNet 或分支（虚拟 WAN 中的站点） 。 选择“保护 Internet 流量”。
   ![保护 Internet 流量](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. 导航回中心页面。 现在，中心的“安全合作伙伴提供程序”状态应为“安全” 。

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>通过第三方服务的分支或 VNet Internet 流量

接下来，你可以检查 VNet 虚拟机或分支站点是否可以访问 Internet，并验证流量是否流向第三方服务。

完成路由设置步骤后，系统将向 VNet 虚拟机和分支站点发送 0/0 到第三方服务的路由。 无法通过 RDP 或 SSH 登录这些虚拟机。 若要登录，可以在互连 VNet 中部署 [Azure Bastion](../bastion/bastion-overview.md) 服务。

## <a name="next-steps"></a>后续步骤

- [教程：在 Azure 门户中使用 Azure 防火墙管理器保护云网络](secure-cloud-network.md)