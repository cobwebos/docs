---
title: 部署 Azure 防火墙管理器受信任的安全合作伙伴
description: 了解如何使用 Azure 门户部署 Azure 防火墙管理器受信任的安全性。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931305"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>部署信任的安全合作伙伴（预览版）

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure 防火墙管理器中的*可信安全合作伙伴*允许您使用您熟悉的、同类最佳的第三方安全即服务 （SECaaS） 产品来保护用户的 Internet 访问。

要了解有关受支持方案和最佳实践指南的更多内容，请参阅[什么是受信任的安全合作伙伴（预览）？](trusted-security-partners.md)

支持的安全合作伙伴是**ZScaler**和**iboss。** 这些预览。 支持的区域是西中环、北中美、西US、西US2和东US。

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

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>在新中心中部署第三方安全提供程序

1. 通过 https://portal.azure.com 登录到 Azure 门户。
2. 在 **"搜索"** 中，键入**防火墙管理器**并在**服务**下选择它。
3. 导航到**入门**。 选择**创建安全虚拟中心**。 
4. 输入订阅和资源组，选择受支持的区域，然后添加中心和虚拟广域网信息。 
5. 默认情况下，**部署 VPN 网关**已启用。 在集线器中部署受信任的安全伙伴需要 VPN 网关。 
6. 选择**下一步：Azure 防火墙**
   > [!NOTE]
   > 受信任的安全合作伙伴使用 VPN 网关隧道连接到您的集线器。 如果删除 VPN 网关，则与受信任安全合作伙伴的连接将丢失。
7. 如果要部署 Azure 防火墙以筛选专用流量以及第三方服务提供商以筛选 Internet 流量，请选择 Azure 防火墙的策略。 请参阅[受支持的方案](trusted-security-partners.md#key-scenarios)。
8. 如果只想在中心部署第三方安全提供程序，请选择**Azure 防火墙：已启用/禁用**以将其设置为 **"已禁用**"。 
9. 选择 **"下一步"：受信任的安全合作伙伴**。
10. 选择 **"受信任的安全合作伙伴**"将其设置为 **"已启用**"。 选择合作伙伴。 
11. 选择“下一步”。 
12. 查看内容，然后选择 **"创建**"。

VPN 网关部署可能需要 30 分钟以上。

要验证中心是否已创建，请导航到 Azure 防火墙管理器>安全中心。 选择中心>概述页面以显示合作伙伴名称和状态为 **"安全连接挂起**"。

创建集线器并设置安全伙伴后，继续将安全提供程序连接到集线器。

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>在现有中心部署第三方安全提供程序

您还可以在虚拟 WAN 中选择现有集线器并将其转换为*安全的虚拟集线器*。

1. 在**入门时**，选择 **"转换现有集线器**"。
2. 选择订阅和现有中心。 按照其余步骤在新中心部署第三方提供程序。

请记住，必须部署 VPN 网关才能将现有集线器转换为具有第三方提供商的安全集线器。

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>配置第三方安全提供程序以连接到安全集线器

要设置虚拟中心 VPN 网关的隧道，第三方提供商需要访问您的中心的权限。 为此，请将服务主体与您的订阅或资源组相关联，并授予访问权限。 然后，您必须使用第三方门户将这些凭据授予第三方。

1. 创建 Azure 活动目录 （AD） 服务主体：可以跳过重定向 URL。 

   [如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. 添加服务主体的访问权限和作用域。
   [如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > 您可以仅限制对资源组的访问，以便进行更精细的控制。
3. 按照[ZScaler：配置 Microsoft Azure 虚拟 WAN 集成](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration)说明，以便：

   - 登录到合作伙伴门户并添加凭据，以便授予受信任的合作伙伴对安全中心的权限。
   - 同步合作伙伴门户中的虚拟中心，并将隧道设置为虚拟中心。 验证 Azure AD 身份验证凭据后，可以执行此操作。
   
4. 您可以在 Azure 中的 Azure 虚拟 WAN 门户上查看隧道创建状态。 隧道在 Azure 和合作伙伴门户上显示**连接**后，继续执行后续步骤以设置路由，以选择哪些分支和 VNet 应向合作伙伴发送 Internet 流量。

## <a name="configure-route-settings"></a>配置路由设置

1. 浏览到 Azure 防火墙管理器 ->安全集线器。 
2. 选择中心。 中心状态现在应显示**预配**，而不是**安全连接挂起**。

   确保第三方提供商可以连接到集线器。 VPN 网关上的隧道应处于**连接**状态。 与以前的状态相比，此状态更能反映集线器与第三方合作伙伴之间的连接运行状况。
3. 选择中心，然后导航到**路由设置**。

   将第三方提供程序部署到中心时，它会将中心转换为*安全的虚拟中心*。 这可确保第三方提供商向中心通告 0.0.0.0/0（默认）路由。 但是，VNet 连接和连接到集线器的站点不会获取此路由，除非您选择在哪些连接上获取此默认路由。
4. 在**互联网流量**下，选择**VNet 到 Internet**或**分支到 Internet，** 或者通过第三方配置路由。

   这仅指示应路由到集线器的流量类型，但它尚未影响 VNet 或分支上的路由。 默认情况下，这些路由不会传播到连接到中心的所有 VNet/分支。
5. 您必须选择**安全连接**并选择应设置这些路由的连接。 这表示哪些 VNet/分支可以开始向第三方提供商发送 Internet 流量。
6. 从**路由设置**中，选择 Internet 流量下**的安全连接**，然后选择要保护的 VNet 或分支（虚拟 WAN 中的*站点*）。 选择**安全互联网流量**。
   ![安全的互联网流量](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. 导航回集线器页面。 集线器的**受信任安全伙伴**状态现在应该**是安全的**。

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>通过第三方服务的分支机构或 VNet 互联网流量

接下来，您可以检查 VNet 虚拟机或分支站点是否可以访问 Internet，并验证流量是否流向第三方服务。

完成路由设置步骤后，VNet 虚拟机以及分支站点将发送 0/0 到第三方服务路由。 不能将 RDP 或 SSH 引入这些虚拟机。 要登录，可以在对等 VNet 中部署[Azure 堡垒](../bastion/bastion-overview.md)服务。

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure 门户使用 Azure 防火墙管理器预览保护云网络](secure-cloud-network.md)




