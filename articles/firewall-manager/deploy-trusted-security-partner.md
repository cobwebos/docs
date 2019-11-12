---
title: 部署 Azure 防火墙管理器可信安全合作伙伴
description: 了解如何使用 Azure 门户部署 Azure 防火墙管理器信任的安全。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931305"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>部署信任的安全合作伙伴（预览版）

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure 防火墙管理器中的*受信任安全合作伙伴*允许使用熟悉的、同类最佳的第三方安全即服务（SECaaS）产品来保护用户对 Internet 的访问。

若要详细了解受支持的方案和最佳实践指南，请参阅[什么是可信安全合作伙伴（预览版）？](trusted-security-partners.md)。

此预览版支持的安全合作伙伴是**ZScaler**和**iboss** 。 支持的区域包括： WestCentralUS、NorthCentralUS、WestUS、WestUS2 和 EastUS。

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> 必须使用 `Register-AzProviderFeature` PowerShell 命令显式启用 Azure 防火墙管理器预览版。

在 PowerShell 命令提示符下，运行以下命令：

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
功能注册最多需要 30 分钟即可完成。 运行以下命令来检查注册状态：

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>在新的中心部署第三方安全提供程序

1. 通过 https://portal.azure.com 登录到 Azure 门户。
2. 在 "**搜索**" 中，键入**防火墙管理器**，然后在 "**服务**" 下选择它。
3. 导航到**入门**。 选择 "**创建受保护的虚拟中心**"。 
4. 输入 "订阅" 和 "资源组"，选择受支持的区域，并添加中心和虚拟 WAN 信息。 
5. 默认情况下，已启用**部署 VPN 网关**。 需要使用 VPN 网关，才能在中心部署受信任的安全合作伙伴。 
6. 选择**下一步： Azure 防火墙**
   > [!NOTE]
   > 受信任的安全合作伙伴使用 VPN 网关隧道连接到你的中心。 如果删除 VPN 网关，与受信任的安全合作伙伴的连接将丢失。
7. 如果要部署 Azure 防火墙来筛选专用流量以及第三方服务提供商来筛选 Internet 流量，请选择 Azure 防火墙的策略。 请参阅[支持的方案](trusted-security-partners.md#key-scenarios)。
8. 如果只想在中心部署第三方安全提供程序，请选择 " **Azure 防火墙：启用/禁用**"，将其设置为 "**已禁用**"。 
9. 选择**下一步：受信任的安全合作伙伴**。
10. 选择 "**受信任的安全合作伙伴**"，将其设置为 "**启用**"。 选择合作伙伴。 
11. 选择“下一步”。 
12. 查看内容，然后选择 "**创建**"。

VPN 网关部署可能需要30多分钟。

若要验证是否已创建了中心，请导航到 "Azure 防火墙管理器"-> 安全中心 "。 选择 "中心 > 概述" 页，以显示 "**安全连接挂起**" 的伙伴名称和状态。

创建中心并设置安全合作伙伴后，请继续将安全提供程序连接到中心。

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>在现有中心部署第三方安全提供程序

还可以选择虚拟 WAN 中的现有中心，并将其转换为*受保护的虚拟中心*。

1. 在**入门**中，选择 "**转换现有中心**"。
2. 选择订阅和现有中心。 按照其余步骤，在新的中心部署第三方提供商。

请记住，必须部署 VPN 网关，才能使用第三方提供程序将现有的集线器转换为安全中心。

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>配置第三方安全提供程序以连接到受保护的中心

若要设置虚拟中心 VPN 网关的隧道，第三方提供商需要访问中心的权限。 为此，请将服务主体与订阅或资源组相关联，并授予访问权限。 然后，必须使用其门户将这些凭据分配给第三方。

1. 创建 Azure Active Directory （AD）服务主体：可以跳过重定向 URL。 

   [如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. 添加服务主体的访问权限和作用域。
   [如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > 可以限制对资源组的访问，以便进行更精细的控制。
3. 按照[ZScaler：配置 Microsoft Azure 虚拟 WAN 集成](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration)说明：

   - 登录到合作伙伴门户并添加凭据，以授予受信任的合作伙伴访问受保护的中心的权限。
   - 在合作伙伴门户中同步虚拟中心，并设置虚拟中心的隧道。 验证 Azure AD 身份验证凭据后，可以执行此操作。
   
4. 可以在 azure 中的 Azure 虚拟 WAN 门户上查看隧道创建状态。 隧道显示**连接**到 Azure 和合作伙伴门户后，继续执行后续步骤，设置路由，以选择哪些分支和 vnet 应将 Internet 流量发送到合作伙伴。

## <a name="configure-route-settings"></a>配置路由设置

1. 浏览到 Azure 防火墙管理器-> 安全中心。 
2. 选择一个中心。 现在，集线器状态应为 "已**设置**"，而不是 "**挂起安全连接**"。

   确保第三方提供程序可以连接到中心。 VPN 网关上的隧道应处于**连接**状态。 与以前的状态相比，此状态更能反映中心和第三方合作伙伴之间的连接运行状况。
3. 选择中心，并导航到 "**路由设置**"。

   向中心部署第三方提供程序时，它会将集线器转换为*受保护的虚拟中心*。 这可确保第三方提供程序向中心播发 0.0.0.0/0 （默认）路由。 但是，除非你选择启用了此默认路由，否则连接到中心的 VNet 连接和站点不会获得此路由。
4. 在 " **internet 流量**" 下，选择 " **VNet 到 internet** " 或 "**分支到 internet** "，或同时选择两者，以通过第三方配置路由。

   这仅指示应将哪种类型的流量路由到中心，但它不会影响 Vnet 或分支上的路由。 默认情况下，这些路由不会传播到连接到中心的所有 Vnet/分支。
5. 你必须选择 "**安全连接**"，并选择要在其上设置这些路由的连接。 这表示可以开始向第三方提供商发送 Internet 流量的 Vnet/分支。
6. 在 "**路由设置**" 中，选择 "Internet 流量" 下的 "**安全连接**"，然后选择要保护的 VNet 或分支（虚拟 WAN 中的*站点*）。 选择 "**安全 Internet 流量**"。
   ![保护 Internet 流量](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. 向后导航到 "中心" 页。 现在，中心的**受信任安全合作伙伴**状态应**为 "安全"** 。

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>通过第三方服务的分支或 VNet Internet 流量

接下来，你可以检查 VNet 虚拟机或分支站点是否可以访问 Internet，并验证流量是否流向第三方服务。

完成路由设置步骤后，VNet 虚拟机和分支站点将发送到0/0 到第三方服务路由。 无法通过 RDP 或 SSH 连接到这些虚拟机。 若要登录，可以在对等互连 VNet 中部署[Azure 堡垒](../bastion/bastion-overview.md)服务。

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure 防火墙管理器预览保护云网络使用 Azure 门户](secure-cloud-network.md)




