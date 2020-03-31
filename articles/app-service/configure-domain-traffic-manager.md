---
title: 使用流量管理器配置 DNS 名称
description: 了解如何为与流量管理器集成以实现负载均衡的 Azure 应用服务应用配置自定义域。
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944123"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>通过流量管理器集成在 Azure 应用服务中配置自定义域名

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> 对于云服务，请查看[为 Azure 云服务配置自定义域名](../cloud-services/cloud-services-custom-domain-name.md)。

当您使用[Azure 流量管理器](/azure/traffic-manager/)将平衡流量加载到[Azure 应用服务](overview.md)时，可以使用**\<流量管理器-终结点>.流量管理器.net**访问应用服务应用。 您可以使用应用服务应用分配自定义域名（如 www\.contoso.com，以便为用户提供更可识别的域名。

本文介绍如何使用与[流量管理器](../traffic-manager/traffic-manager-overview.md)集成的应用服务应用配置自定义域名。

> [!NOTE]
> 使用流量管理器终结点配置域名时，仅支持[CNAME](https://en.wikipedia.org/wiki/CNAME_record)记录。 由于不支持记录，因此也不支持根域映射（如contoso.com。
> 

## <a name="prepare-the-app"></a>准备应用

要将自定义 DNS 名称映射到与 Azure 流量管理器集成的应用，Web 应用[的应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须位于**标准**层或更高级别。 在此步骤中，需确保应用服务计划位于受支持的定价层。

### <a name="check-the-pricing-tier"></a>检查定价层

在[Azure 门户](https://portal.azure.com)中，搜索并选择**应用服务**。

在“应用服务”页上，选择 Azure 应用的名称****。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-domain/select-app.png)

在应用页面的左侧导航中，选择 **"向上缩放"（应用服务计划）。**

![扩展菜单](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

蓝色边框突出显示了应用的当前层。 检查以确保应用位于**标准**层或以上（**生产**或**隔离**类别中的任何层）。 如果是，请关闭 **"向上缩放**"页并跳过以创建[CNAME 映射](#create-the-cname-mapping)。

![检查定价层](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>增加应用服务计划

如果需要扩展应用，请选择 **"生产**"类别中的任何定价层。 有关其他选项，请单击“查看其他选项”****。

单击 **“应用”**。

## <a name="create-traffic-manager-endpoint"></a>创建流量管理器终结点

按照["添加或删除终结点](../traffic-manager/traffic-manager-endpoints.md)"中的步骤，将应用服务应用添加为流量管理器配置文件中的终结点。

应用服务应用处于受支持的定价层后，在添加终结点时，该应用将显示在可用应用服务目标列表中。 如果应用未列出，[请验证应用的定价层](#prepare-the-app)。

## <a name="create-the-cname-mapping"></a>创建 CNAME 映射
> [!NOTE]
> 要配置[您购买的应用服务域，](manage-custom-dns-buy-domain.md)请跳过此部分，然后转到[启用自定义域](#enable-custom-domain)。
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

虽然每个域提供商的具体细节各不相同，但您*从*与应用集成的流量管理器域名（如**contoso.com）***映射到*流量管理器域名 **（contoso.trafficmanager.net）。**

> [!NOTE]
> 如果某条记录已被使用并且你需要先将你的应用绑定到该记录，可以创建一条额外的 CNAME 记录。 例如，要先发制人地将**\.www contoso.com**绑定到你的应用，请创建从**awverify.www**到**contoso.trafficmanager.net**的 CNAME 记录。 然后，您可以将"www\.contoso.com"添加到应用，而无需更改"www"CNAME 记录。 有关详细信息，请参阅[将活动 DNS 名称迁移到 Azure 应用服务](manage-custom-dns-migrate-domain.md)。

在域提供商处添加或修改完 DNS 记录后，请保存这些更改。

## <a name="enable-custom-domain"></a>启用自定义域
域名记录传播后，请使用浏览器验证自定义域名是否解析为应用服务应用。

> [!NOTE]
> CNAME 通过 DNS 系统向外传播可能需要一段时间。 您可以使用服务，例如<a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a>验证 CNAME 是否可用。
> 
> 

1. 域解析成功后，返回[Azure 门户](https://portal.azure.com)中的应用页面
2. 从左侧导航中，选择 **"自定义域** > **添加主机名**"。
4. 键入您之前映射的自定义域名，然后选择 **"验证**"。
5. 确保**主机名记录类型**设置为**CNAME（www\.example.com 或任何子域）。**

6. 由于应用服务应用现在与流量管理器终结点集成，因此应在**CNAME 配置**下看到流量管理器域名。 选择它，然后单击 **"添加自定义域**"。

    ![将 DNS 名称添加到应用](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Azure 应用服务中使用 SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)
