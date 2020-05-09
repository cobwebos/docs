---
title: 使用流量管理器配置 DNS 名称
description: 了解如何为与流量管理器集成以实现负载均衡的 Azure 应用服务应用配置自定义域。
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 5ae68a8871bc2894191644e4ab183be4b469bf16
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610235"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>使用流量管理器集成在 Azure 应用服务中配置自定义域名

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> 对于云服务，请查看[为 Azure 云服务配置自定义域名](../cloud-services/cloud-services-custom-domain-name.md)。

使用 [Azure 流量管理器](/azure/traffic-manager/)将流量负载均衡到 [Azure 应用服务](overview.md)时，可以使用 **\<traffic-manager-endpoint>.trafficmanager.net** 来访问应用服务应用。 还可以将自定义域名（例如 www\.contoso.com）与应用服务应用相关联，为用户提供更易识别的域名。

本文介绍如何使用与[流量管理器](../traffic-manager/traffic-manager-overview.md)集成的应用服务应用配置自定义域名。

> [!NOTE]
> 使用流量管理器终结点配置域名时，仅支持 [CNAME](https://en.wikipedia.org/wiki/CNAME_record) 记录。 由于不支持 A 记录，因此也不支持根域映射，例如 contoso.com。
> 

## <a name="prepare-the-app"></a>准备应用

若要将自定义 DNS 名称映射到与 Azure 流量管理器集成的应用，Web 应用的[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须位于“标准”  层或更高层。 在此步骤中，需确保应用服务计划位于受支持的定价层。

### <a name="check-the-pricing-tier"></a>检查定价层

在 [Azure 门户](https://portal.azure.com)中，搜索并选择“应用服务”  。

在“应用服务”页上，选择 Azure 应用的名称  。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/select-app.png)

在应用页面的左侧导航栏中，选择“纵向扩展(应用服务计划)”。 

![扩展菜单](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

蓝色边框突出显示了应用的当前层。 进行检查，确保应用处于“标准”  层或更高层（“生产”  或“隔离”类别中的任何层  ）。 如果是，请关闭“纵向扩展”  页，跳到[创建 CNAME 映射](#create-the-cname-mapping)。

![检查定价层](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>增加应用服务计划

如果需要纵向扩展应用，请选择“生产”  类别中的任何定价层。 有关其他选项，请单击“查看其他选项”  。

单击“应用”  。

## <a name="create-traffic-manager-endpoint"></a>创建流量管理器终结点

按[添加或删除终结点](../traffic-manager/traffic-manager-endpoints.md)中的步骤操作，在流量管理器配置文件中将应用服务应用添加为终结点。

如果应用服务应用已处于受支持的定价层，则当你添加终结点时，它会显示在可用应用服务目标列表中。 如果应用未列出，请[验证应用的定价层](#prepare-the-app)。

## <a name="create-the-cname-mapping"></a>创建 CNAME 映射
> [!NOTE]
> 若要配置已[购买的应用服务域](manage-custom-dns-buy-domain.md)，请跳过此部分并转到 "[启用自](#enable-custom-domain)定义域"。
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

虽然每个域提供程序的具体内容各不相同，但你可以将[非根自定义域名](#what-about-root-domains)（例如**www.contoso.com** *）映射**到*与你的应用集成的流量管理器域名（**contoso.trafficmanager.net**）。 

> [!NOTE]
> 如果某条记录已被使用并且你需要先将你的应用绑定到该记录，可以创建一条额外的 CNAME 记录。 例如，若要提前将**www\.contoso.com**绑定到你的应用，请创建从**awverify**到**contoso.trafficmanager.net**的 CNAME 记录。 然后，可以将 "www\.contoso.com" 添加到应用程序，而无需更改 "WWW" CNAME 记录。 有关详细信息，请参阅[将活动 DNS 名称迁移到 Azure App Service](manage-custom-dns-migrate-domain.md)。

在域提供商处添加或修改完 DNS 记录后，请保存这些更改。

### <a name="what-about-root-domains"></a>根域怎么样？

由于流量管理器仅支持与 CNAME 记录进行自定义域映射，并且 DNS 标准不支持用于映射根域的 CNAME 记录（例如， **contoso.com**），因此流量管理器不支持映射到根域。 若要解决此问题，请在应用级别使用 URL 重定向。 例如，在 ASP.NET Core 中，可以使用[URL 重写](/aspnet/core/fundamentals/url-rewriting)。 然后，使用流量管理器对子域进行负载平衡（**www.contoso.com**）。

对于高可用性方案，可以通过创建从根域指向每个应用副本的 IP 地址的多个*a 记录*，来实现容错 DNS 设置，而无需流量管理器。 然后，将[同一根域映射到所有应用副本](app-service-web-tutorial-custom-domain.md#map-an-a-record)。 由于不能将相同的域名映射到同一区域中的两个不同的应用，因此仅当你的应用副本位于不同区域时，此设置才有效。

## <a name="enable-custom-domain"></a>启用自定义域
在域名称的记录传播完成后，使用浏览器验证自定义域名是否解析为应用服务应用。

> [!NOTE]
> CNAME 通过 DNS 系统向外传播可能需要一段时间。 可以使用等服务<a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a>来验证 CNAME 是否可用。
> 
> 

1. 域解析成功后，返回到[Azure 门户](https://portal.azure.com)中的应用页面
2. 从左侧导航栏中，选择 "**自定义域** > " "**添加主机名**"。
4. 键入之前映射的自定义域名，然后选择 "**验证**"。
5. 确保“主机名记录类型”  设置为“CNAME (www\.example.com 或任何子域)”  。

6. 由于应用服务应用现已与流量管理器终结点集成，因此你应在 " **CNAME 配置**" 下看到流量管理器域名。 选择它，然后单击 "**添加自定义域**"。

    ![将 DNS 名称添加到应用](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Azure 应用服务中使用 SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)
