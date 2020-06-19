---
title: 作为 Azure 事件网格合作伙伴加入
description: 作为 Azure 事件网格合作伙伴主题类型加入。 了解合作伙伴主题的资源模型和发布流。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 2a1f35b86e21099c9fdd0397ae8a3b20aed3cd5d
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758820"
---
# <a name="onboard-as-an-azure-event-grid-partner"></a>作为 Azure 事件网格合作伙伴加入

本文介绍如何以专用方式使用事件网格伙伴资源以及如何成为公开提供的合作伙伴主题类型。

你不需要特殊权限即可作为事件网格伙伴开始使用与发布事件关联的事件网格资源类型。 事实上，现在可以使用它们以专用方式将事件发布到自己的 Azure 订阅，以及在考虑成为合作伙伴时测试资源模型。

## <a name="becoming-an-event-grid-partner"></a>成为事件网格合作伙伴

如果你对成为公共事件网格伙伴感兴趣，请先填写[此表单](https://aka.ms/gridpartnerform)，然后通过 [GridPartner@microsoft.com](mailto:gridpartner@microsoft.com) 联系事件网格团队。

## <a name="how-partner-topics-work"></a>合作伙伴主题的工作方式
合作伙伴主题采用现有体系结构，事件网格已使用该体系结构从 Azure 资源（如存储和 IoT 中心）发布事件并使这些工具公开提供给任何人使用。 默认情况下，使用这些工具仅专用于你的 Azure 订阅。 若要公开提供事件，请填写上面的表单，并[联系事件网格团队](mailto:gridpartner@microsoft.com)。

通过合作伙伴主题可以将事件发布到 Azure 事件网格，以用于多租户消耗。

### <a name="onboarding-and-event-publishing-overview"></a>加入和事件发布概述

#### <a name="partner-flow"></a>合作伙伴流

1. 创建 Azure 租户（如果还没有租户）。
1. 使用 CLI 创建新的事件网格 `partnerRegistration`。 此资源包含显示名称、说明、设置 URI 等信息。

    ![创建合作伙伴主题](./media/partner-onboarding-how-to/create-partner-registration.png)

1. 在要发布事件的每个区域中创建一个或多个 `partnerNamespaces`。 作为此过程的一部分，事件网格服务将预配发布终结点（例如 `https://contoso.westus-1.eventgrid.azure.net/api/events`）和访问密钥。

    ![创建合作伙伴命名空间](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. 为客户提供一种方式，用于在他们需要合作伙伴主题的系统中注册。
1. 请与事件网格团队联系，告诉我们你希望合作伙伴主题类型成为公共的。

#### <a name="customer-flow"></a>客户流

1. 你的客户将访问 Azure 门户以记下他们希望在其中创建合作伙伴主题的 Azure 订阅 ID 和资源组。
1. 客户将通过你的系统请求合作伙伴主题。 在响应中，你将为你的合作伙伴命名空间创建事件隧道。
1. 事件网格将在客户的 Azure 订阅和资源组中创建一个挂起的合作伙伴主题。

    ![创建事件通道](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. 客户通过 Azure 门户激活合作伙伴主题。 事件现在可以从你的服务流向客户的 Azure 订阅。

    ![激活合作伙伴主题](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>资源模型

下面是合作伙伴主题的资源模型。

### <a name="partner-registrations"></a>合作伙伴注册
* 资源：`partnerRegistrations`
* 使用者：合作伙伴
* 说明:捕获 SaaS 合作伙伴的全局元数据（例如名称、显示名称、说明、设置 URI）。
    
    创建/更新合作伙伴注册是适用于合作伙伴的自助服务操作。 此自助服务功能使合作伙伴能够构建并测试完整的端到端流。
    
    客户只能发现经 Microsoft 批准的 partnerRegistrations。
* 范围：在合作伙伴的 Azure 订阅中创建。 成为公共状态后对客户可见的元数据。

### <a name="partner-namespaces"></a>合作伙伴命名空间
* 资源：partnerNamespaces
* 使用者：合作伙伴
* 说明:提供用于将客户事件发布到的区域资源。 每个伙伴命名空间都有一个发布终结点和身份验证密钥。 命名空间也是合作伙伴对给定客户请求合作伙伴主题以及列出活动客户的方式。
* 范围：存在于合作伙伴的订阅中。

### <a name="event-channel"></a>事件通道
* 资源：`partnerNamespaces/eventChannels`
* 使用者：合作伙伴
* 说明:事件隧道是客户合作伙伴主题的镜像。 通过创建事件隧道并在元数据中指定客户的 Azure 订阅和资源组，你可以向事件网格发出信号，为客户创建合作伙伴主题。 事件网格将发出 ARM 调用，以便在客户的订阅中创建对应的 partnerTopic。 合作伙伴主题将创建为“挂起”状态。 每个 eventTunnel 与 partnerTopic 之间存在 1-1 链接。
* 范围：存在于合作伙伴的订阅中。

### <a name="partner-topics"></a>合作伙伴主题
* 资源：`partnerTopics`
* 使用者：客户
* 说明:合作伙伴主题类似于事件网格中的自定义主题和系统主题。 每个合作伙伴主题都与特定“源”（例如 `Contoso:myaccount`）和特定合作伙伴主题类型（例如，“Contoso”）相关联。 客户可对合作伙伴主题创建事件订阅，以将事件路由到各种事件处理程序。

    客户无法直接创建此资源。 创建合作伙伴主题的唯一方法是通过创建事件隧道的合作伙伴操作。
* 范围：存在于客户的订阅中。

### <a name="partner-topic-types"></a>合作伙伴主题类型
* 资源：`partnerTopicTypes`
* 使用者：客户
* 说明:合作伙伴主题类型是租户范围资源类型，使客户可以发现已批准的合作伙伴主题类型列表。 URL 如下所示： https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* 范围：Global

## <a name="publishing-events-to-event-grid"></a>将事件发布到事件网格
当你在 Azure 区域中创建 partnerNamespace 时，你将获得一个区域终结点和对应的身份验证密钥。 对于该命名空间中的所有客户事件隧道，将事件批次发布到此终结点。 Azure 事件网格会基于事件中的“源”字段，将每个事件映射到对应的合作伙伴主题。

### <a name="event-schema-cloudevents-v10"></a>事件架构：CloudEvents v1.0
使用 CloudEvents 1.0 架构将事件发布到 Azure 事件网格。 事件网格支持结构化模式和批处理模式。 CloudEvents 1.0 是合作伙伴命名空间唯一支持的事件架构。

### <a name="example-flow"></a>示例流

1.  发布服务对 `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` 执行 HTTP POST。
2.  在请求中包含一个名为 aeg-sas-key 的标头值，其中包含身份验证密钥。 此密钥在 partnerNamespace 创建过程中进行预配。 例如，有效的标头值为 aeg-sas-key：VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==.
3.  将 Content-type 标头设置为“application/cloudevents-batch+json; charset=UTF-8”。
4.  使用与该区域对应的事件批次，对以上发布 URL 执行 HTTP POST。 例如：

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

发布到 partnerNamespace 终结点之后，你将收到响应。 响应是标准 HTTP 响应代码。 一些常见的响应如下所示：

| 结果                             | 响应              |
|------------------------------------|-----------------------|
| Success                            | 200 正常                |
| 事件数据的格式不正确    | 400 错误请求       |
| 访问密钥无效                 | 401 未授权      |
| 终结点不正确                 | 404 未找到         |
| 数组或事件超出大小限制 | 413 有效负载太大 |

## <a name="reference"></a>参考

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM 模板](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)
  * [ARM 模板架构](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST API](https://docs.microsoft.com/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [CLI 扩展](https://docs.microsoft.com/cli/azure/ext/eventgrid/?view=azure-cli-latest)

### <a name="sdks"></a>SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>后续步骤
- [合作伙伴主题概述](partner-topics-overview.md)
- [合作伙伴主题加入表单](https://aka.ms/gridpartnerform)
- [Auth0 合作伙伴主题](auth0-overview.md)
- [如何使用 Auth0 合作伙伴主题](auth0-how-to.md)
