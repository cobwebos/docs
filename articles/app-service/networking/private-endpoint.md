---
title: 使用专用终结点将专用连接到 Azure Web 应用
description: 使用 Azure 专用终结点以私密方式连接到 Web 应用
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 08/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 773e63cb5eb2a9825975402f65439acd6ad192ae
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135379"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>为 Azure Web 应用使用专用终结点（预览版）

> [!Note]
> 通过预览刷新，我们发布了数据泄露保护功能。
>
> 预览版适用于 PremiumV2 Windows 和 Linux Web 应用和弹性高级功能的所有公共区域。 

可以为 Azure Web 应用使用专用终结点，以允许位于专用网络中的客户端通过专用链接安全地访问应用。 专用终结点使用你的 Azure VNet 地址空间中的 IP 地址。 专用网络上客户端与 Web 应用之间的网络流量将通过 VNet 以及 Microsoft 主干网络上的专用链接，因此不会从公共 Internet 公开。

对 Web 应用使用专用终结点的目的如下：

- 通过配置专用终结点来保护 Web 应用，避免公开。
- 从那些使用 VPN 或 ExpressRoute 专用对等互连连接到 VNet 的本地网络安全地连接到 Web 应用。
- 避免从 VNet 泄露任何数据。 

如果你只是在 VNet 与 Web 应用之间需要安全连接，则服务终结点是最简单的解决方案。 如果还需要通过 Azure 网关从本地访问 web 应用，请使用突破对等互连 VNet 或全局对等互连 VNet，专用终结点是解决方案。  

有关详细信息，请参阅[服务终结点][serviceendpoint]。

## <a name="conceptual-overview"></a>概念概述

专用终结点是一个特殊的网络接口 (NIC)，适用于虚拟网络 (VNet) 的子网中的 Azure Web 应用。
为 Web 应用创建专用终结点时，它会在专用网络上的客户端与 Web 应用之间提供安全连接。 专用终结点是从 VNet 的 IP 地址范围分配的 IP 地址。
专用终结点与 Web 应用之间的连接使用安全的[专用链接][privatelink]。 专用终结点仅用于到 Web 应用的传入流。 传出流将不使用此专用终结点，但你可以通过 [VNet 集成功能][vnetintegrationfeature]将传出流注入到你的网络中的另一个子网。

在其中插入专用终结点的子网中可以有其他资源，不需要是专用的空子网。
你还可以在与 Web 应用不同的区域中部署专用终结点。 

> [!Note]
>VNet 集成功能不能使用与专用终结点相同的子网，这是 VNet 集成功能的限制。

从安全角度来看：

- 当你启用到 Web 应用的专用终结点时，将禁用所有公共访问权限。
- 可以在其他 VNet 和子网（包括其他区域中的 VNet）中启用多个专用终结点。
- 专用终结点 NIC 的 IP 地址必须是动态的，但在你删除专用终结点之前，该地址会保持不变。
- 专用终结点的 NIC 不能有关联的 NSG。
- 承载专用终结点的子网可以有一个关联的 NSG，但你必须禁用专用终结点的网络策略实施：请参阅[禁用专用终结点的网络策略][disablesecuritype]。 因此，不能按任何 NSG 来筛选对专用终结点的访问权限。
- 当你启用到 Web 应用的专用终结点时，系统不会评估对 Web 应用的[访问限制][accessrestrictions]配置。
- 可以通过删除目标为标记“Internet”或“Azure 服务”的所有 NSG 规则来消除 VNet 中的数据泄露风险。 为 Web 应用部署专用终结点时，只能通过专用终结点访问此特定 Web 应用。 如果有另一个 Web 应用，则必须为该 Web 应用部署另一个专用终结点。

在 Web 应用的 Web HTTP 日志中，可找到客户端源 IP。 此功能是使用 TCP 代理协议实现的，并将客户端 IP 属性转发到 Web 应用。 有关详细信息，请参阅[使用 TCP 代理 v2 获取连接信息][tcpproxy]。


  > [!div class="mx-imgBorder"]
  > ![Web 应用专用终结点全局概述](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

使用 Web 应用的专用终结点时，请求的 URL 必须与 Web 应用的名称匹配。 默认情况下，mywebappname.azurewebsites.net。

默认情况下，如果没有专用终结点，则 web 应用的公用名称是群集的规范名称。
例如，名称解析将为：

|名称 |类型 |值 |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


部署专用终结点时，将更新 DNS 条目以指向规范名称 mywebapp.privatelink.azurewebsites.net。
例如，名称解析将为：

|名称 |类型 |值 |备注 |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|<--此公共 IP 不是你的专用终结点，你将收到403错误|

你必须设置专用 DNS 服务器或 Azure DNS 专用区域，对于测试，你可以修改测试计算机的主机条目。
需要创建的 DNS 区域是： **privatelink.azurewebsites.net**。 使用 A 记录和专用终结点 IP 为 Web 应用注册记录。
例如，名称解析将为：

|名称 |类型 |值 |备注 |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|<--在 DNS 系统中管理此项以指向专用终结点 IP 地址|

在此 DNS 配置之后，可以通过默认名称 mywebappname.azurewebsites.net 访问 Web 应用。


如果你需要使用自定义 DNS 名称，则必须在 Web 应用中添加自定义名称。 在预览版期间，与任何自定义名称一样，必须使用公共 DNS 解析来验证该自定义名称。 有关详细信息，请参阅[自定义 DNS 验证][dnsvalidation]。

对于 Kudu 控制台或 Kudu REST API 部署与 Azure DevOps 自托管代理 (例如) ，必须在 Azure DNS 专用区域或自定义 DNS 服务器中创建两个记录。 

| 名称 | 类型 | 值 |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | A | PrivateEndpointIP | 



## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价][pricing]。

## <a name="limitations"></a>限制

将弹性高级计划中的 Azure 函数与专用终结点配合使用时，若要在 Azure Web 门户中运行或执行函数，你必须具有直接网络访问权限，否则会收到 HTTP 403 错误。 换句话说，浏览器必须能够访问专用终结点，以便从 Azure Web 门户执行该功能。 

在预览版期间，只会在专用终结点后面公开生产槽，其他槽则必须通过公共终结点访问。

我们会定期改进专用链接功能和专用终结点。若要了解有关限制的最新信息，请查看[此文][pllimitations]。

## <a name="next-steps"></a>后续步骤

- 若要通过门户部署 Web 应用的专用终结点，请参阅[如何使用门户将专用连接到 Web 应用][howtoguide1]
- 若要使用 Azure CLI 为 Web 应用部署专用终结点，请参阅[如何使用 Azure CLI 私下连接到 Web 应用][howtoguide2]
- 若要使用 PowerShell 为你的 Web 应用部署专用终结点，请参阅[如何使用 powershell 通过专用连接到 Web 应用][howtoguide3]
- 若要使用 Azure 模板为你的 Web 应用部署专用终结点，请参阅[如何使用 azure 模板私下连接到 Web 应用][howtoguide4]
- 端到端示例，如何使用 VNet 注入和专用终结点将前端 web 应用连接到受保护的后端 web 应用，请参阅此[快速入门][howtoguide5]
- 端到端示例，如何使用 terraform 将前端 web 应用连接到具有 VNet 注入和专用终结点的安全后端 web 应用，请参阅此[示例][howtoguide6]


<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
[howtoguide2]: https://docs.microsoft.com/azure/app-service/scripts/cli-deploy-privateendpoint
[howtoguide3]: https://docs.microsoft.com/azure/app-service/scripts/powershell-deploy-private-endpoint
[howtoguide4]: https://docs.microsoft.com/azure/app-service/scripts/template-deploy-private-endpoint
[howtoguide5]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection
[howtoguide6]: https://docs.microsoft.com/azure/app-service/scripts/terraform-secure-backend-frontend
