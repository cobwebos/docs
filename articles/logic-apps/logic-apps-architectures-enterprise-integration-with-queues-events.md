---
title: 企业集成体系结构模式 - Azure Integration Services
description: 本体系结构参考演示如何使用 Azure 逻辑应用、Azure API 管理、Azure 服务总线和 Azure 事件网格来实现企业集成模式
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 8fbc84b4016659b6d0d6ce9ec47c05a0b241c3d9
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855368"
---
# <a name="enterprise-integration-architecture-with-queues-and-events"></a>包含队列和事件的企业集成体系结构

本文介绍一种使用成熟做法的企业集成体系结构，在使用 Azure Integration Services 时，可将它应用到集成应用程序。 可将此体系结构用作需要 HTTP API、工作流和业务流程的许多不同应用程序模式的基础。

![体系结构图 - 企业集成与队列和事件](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

本教程系列介绍可用于生成通用集成应用程序的可重用组件部分。 由于集成技术涉及到多种可能的应用，其范围从简单的点对点应用到完整的企业 Azure 服务总线应用不等，因此，请考虑需要为自己的应用和基础结构实施哪些组件。

## <a name="architecture-components"></a>体系结构组件

本体系结构构建在[体系结构参考：简单的企业集成](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md)一文所述的体系结构基础之上。 该体系结构的[建议](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md#recommendations)在本文中同样适用，但为简单起见，本文的[建议](#recommendations)部分省略了这些建议。 本企业集成体系结构包括以下组件：

- **资源组**：[资源组](../azure-resource-manager/resource-group-overview.md)是 Azure 资源的逻辑容器。

- **Azure API 管理**：[API 管理](https://docs.microsoft.com/azure/api-management/)服务是一个完全托管的平台，用于发布、保护和转换 HTTP API。

- **Azure API 管理开发人员门户**：在 Azure API 管理的每个实例中都可以访问[开发人员门户](../api-management/api-management-customize-styles.md)。 可在此门户中访问文档和代码示例。 还可以在开发人员门户中测试 API。

- **Azure 逻辑应用**：[逻辑应用](../logic-apps/logic-apps-overview.md)是用于生成企业工作流和集成的无服务器平台。

- **连接器**：逻辑应用使用[连接器](../connectors/apis-list.md)连接到常用的服务。 逻辑应用提供数百个连接器，但你也可以创建自定义的连接器。

- **Azure 服务总线**：[服务总线](../service-bus-messaging/service-bus-messaging-overview.md)提供安全可靠的消息传送。 可以使用消息传送来解耦应用程序，以及与其他基于消息的系统相集成。

- **Azure 事件网格**：[事件网格](../event-grid/overview.md)是用于发布和传送应用程序事件的无服务器平台。

- **IP 地址**：Azure API 管理服务具有固定的公共 [IP 地址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)和域名。 默认域名是 azure-api.net 的子域（例如 contoso.azure-api.net），但你也可以配置[自定义域](../api-management/configure-custom-domain.md)。 逻辑应用和服务总线也具有公共 IP 地址。 但是，出于安全考虑，在此体系结构中，仅限 API 管理的 IP 地址调用逻辑应用终结点。 对服务总线的调用受共享访问签名 (SAS) 的保护。

- **Azure DNS**：[Azure DNS](https://docs.microsoft.com/azure/dns/) 是 DNS 域的托管服务。 Azure DNS 使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。 若要使用自定义域名（例如 contoso.com），请创建可将自定义域名映射到 IP 地址的 DNS 记录。 有关详细信息，请参阅[在 API 管理中配置自定义域名](../api-management/configure-custom-domain.md)。

- **Azure Active Directory (Azure AD)**：可以使用 [Azure AD](https://docs.microsoft.com/azure/active-directory/) 或其他标识提供者进行身份验证。 Azure AD 为 API 终结点访问提供身份验证（通过传递 [API 管理的 JSON Web 令牌](../api-management/policies/authorize-request-based-on-jwt-claims.md)进行验证）。 对于标准层和高级层，Azure AD 可以保护对 API 管理开发人员门户的访问。

## <a name="patterns"></a>模式 

本体系结构使用一些基本的运行模式：

* 现有的后端 HTTP API 通过 API 管理开发人员门户发布。 在该门户中，开发人员（组织内部和/或外部）  
可将对这些 API 的调用集成到应用程序中。

* 复合 API 是使用逻辑应用生成的，可协调对软件即服务 (SaaS) 系统、Azure 服务和发布到 API 管理的任何 API 的调用。 还可以[通过 API 管理开发人员门户发布](../api-management/import-logic-app-as-api.md)逻辑应用。

* 应用程序使用 Azure AD 获取访问 API 所需的 [OAuth 2.0 安全令牌](../api-management/api-management-howto-protect-backend-with-aad.md)。

* Azure API 管理[验证安全令牌](../api-management/api-management-howto-protect-backend-with-aad.md)，然后将请求传递给后端 API 或逻辑应用。

* Azure Service 服务总线队列用于[解耦](../service-bus-messaging/service-bus-messaging-overview.md)应用程序活动，以及[平缓负载高峰](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling)。 使用逻辑应用或第三方应用将消息添加到队列中，或者（图中未显示）通过使用 API 管理将队列发布为 HTTP API 来执行此操作。

* 将消息添加到服务总线队列后，将触发事件。 该事件触发逻辑应用，后者处理消息。

* 其他 Azure 服务（例如 Azure Blob 存储和 Azure 事件中心）也会将事件发布到事件网格。 这些服务触发逻辑应用来接收事件并执行后续操作。

## <a name="recommendations"></a>建议

你的具体要求可能与本文所述的通用体系结构不同。 请使用本部分中的建议作为入手点。

### <a name="service-bus-tier"></a>服务总线层

使用支持事件网格通知的服务总线高级层。 有关详细信息，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。

### <a name="event-grid-pricing"></a>事件网格定价

事件网格使用无服务器模型。 根据操作（事件执行）数目计算费用。 有关详细信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。 目前，事件网格没有层方面的注意事项。

### <a name="use-peeklock-to-consume-service-bus-messages"></a>使用服务总线消息时使用 PeekLock

创建逻辑应用来使用服务总线消息时，请让逻辑应用使用 [PeekLock](../service-bus-messaging/service-bus-messaging-overview.md#queues) 来访问一组消息。 使用 PeekLock 时，逻辑应用可以执行步骤来验证每条消息，然后完成或放弃该消息。 此方法可以防范意外的消息丢失。

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>事件网格触发器触发时检查多个对象

当事件网格触发器激发时，此操作仅意味着“发生了其中的至少 1 个事件”。 例如，当事件网格针对服务总线队列中显示的消息触发某个逻辑应用时，该逻辑应用应始终假设存在一条或多条可处理的消息。

### <a name="region"></a>区域

为了最大程度地降低网络延迟，请在同一区域中预配 API 管理、逻辑应用和服务总线。 一般情况下，应选择离用户最近的区域。

资源组也有一个区域。 此区域指定了部署元数据的存储位置，以及执行部署模板的位置。 为了提高部署期间的可用性，请将资源组和资源放在同一区域。

## <a name="scalability"></a>可伸缩性

若要提高可伸缩性，服务总线高级层可以扩展消息传送单元数。 高级层配置可以包含 1 个、2 个或 4 个消息传递单位。 有关缩放服务总线的详细信息，请参阅[使用服务总线消息传递改进性能的最佳实践](../service-bus-messaging/service-bus-performance-improvements.md)。

## <a name="availability"></a>可用性

* 目前，Azure API 管理的基本层、标准层和高级层的服务级别协议 (SLA) 为 99.9%。 对于在两个或更多个区域中至少部署了一个单位的高级层配置，SLA 为 99.95%。

* Azure 逻辑应用的 SLA 目前为 99.9%。

### <a name="disaster-recovery"></a>灾难恢复

为了在发生严重的服务中断时实现故障转移，请考虑在服务总线高级层中实施异地灾难恢复。 有关详细信息，请参阅 [Azure 服务总线异地灾难恢复](../service-bus-messaging/service-bus-geo-dr.md)。

## <a name="manageability"></a>可管理性

为生产、开发和测试环境创建单独的资源组。 使用单独的资源组可以更方便地管理部署、删除测试部署，以及分配访问权限。

将资源分配到资源组时，请考虑以下因素：

* **生命周期**：一般情况下，应将具有相同生命周期的资源放入同一个资源组。

* **访问**：可以使用[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 对组中的资源应用访问策略。

* **计费**：可以查看资源组的累计成本。

* **API 管理的定价层**：对开发和测试环境使用开发人员层。 为了尽量降低预生产期间的成本，请部署生产环境的副本、运行测试，然后关闭。

有关详细信息，请参阅 [Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md)。

## <a name="deployment"></a>部署

* 若要部署 API 管理、逻辑应用、事件网格和服务总线，请使用 [Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)。 使用模板可以通过 PowerShell 或 Azure CLI 更轻松地自动完成部署。

* 将 API 管理、任何单个逻辑应用、事件网格主题和服务总线命名空间放在其自身独立的资源管理器模板中。 使用独立的模板可将资源存储在源代码管理系统中。 然后，可以在持续集成/持续部署 (CI/CD) 过程中统一或者逐个部署这些模板。

## <a name="diagnostics-and-monitoring"></a>诊断和监控

与 API 管理和逻辑应用一样，可以使用 Azure Monitor（默认已启用）来监视服务总线。 Azure Monitor 根据为每个服务配置的指标提供信息。 

## <a name="security"></a>安全

若要保护服务总线，请使用共享访问签名 (SAS)。 例如，可以使用 [SAS 身份验证](../service-bus-messaging/service-bus-sas.md)向具有特定权限的用户授予对服务总线资源的访问权限。 有关详细信息，请参阅[服务总线身份验证和授权](../service-bus-messaging/service-bus-authentication-and-authorization.md)。

如果需要将服务总线队列公开为 HTTP 终结点（例如，以便发布新消息），请使用 API 管理将此终结点前置，以对其提供保护。 然后，可以适当地使用证书或 OAuth 身份验证来保护终结点。 保护终结点的最简单方法是使用包含 HTTP 请求/响应触发器的逻辑应用作为中介。

事件网格服务通过验证代码保护事件传送。 如果通过逻辑应用来使用事件，则会自动执行验证。 有关详细信息，请参阅[事件网格安全性和身份验证](../event-grid/security-authentication.md)。

## <a name="next-steps"></a>后续步骤

* 了解[简单的企业集成](logic-apps-architectures-simple-enterprise-integration.md)
