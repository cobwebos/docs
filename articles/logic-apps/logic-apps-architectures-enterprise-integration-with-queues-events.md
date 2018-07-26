---
title: Azure Integration Services 企业集成参考体系结构
description: 介绍了演示如何使用逻辑应用、API 管理、服务总线和事件网格实现企业集成模式的参考体系结构。
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: a86c4c4227795a712dd51ace1fbefe9d2b96518a
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116106"
---
# <a name="reference-architecture-enterprise-integration-with-queues-and-events"></a>参考体系结构：企业集成与队列和事件

以下参考体系结构演示使用 Azure Integration Services 的集成应用程序适用的一套成熟做法。 该体系结构可充当需要 HTTP API、工作流和业务流程的许多不同应用程序模式的基础。

![体系结构图 - 企业集成与队列和事件](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

*集成技术可能涉及多种应用程序。其范围从简单的点对点应用程序到完整的企业 Azure 服务总线应用程序不等。体系结构系列介绍了可用于生成通用集成应用程序的可重用组件部分。架构师应该考虑需要为自己的应用程序和基础结构实现哪些组件。*

## <a name="architecture"></a>体系结构

此体系结构*基于*[简单企业集成](logic-apps-architectures-simple-enterprise-integration.md)体系结构。 [简单企业体系结构的相关建议](logic-apps-architectures-simple-enterprise-integration.md#recommendations)适用于此处。 为简洁起见，本文中的[建议](#recommendations)省略了它们。 

此体系结构具有以下组件：

- 资源组。 [资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)是 Azure 资源的逻辑容器。
- **Azure API 管理**。 [API 管理](https://docs.microsoft.com/azure/api-management/)是一个完全托管的平台，用于发布、保护和转换 HTTP API。
- **Azure API 管理开发人员门户**。 Azure API 管理的每个实例都可以访问[开发人员门户](https://docs.microsoft.com/azure/api-management/api-management-customize-styles)。 你可以通过 API 管理开发人员门户访问文档和代码示例。 可以在开发人员门户中测试 API。
- **Azure 逻辑应用**。 [逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)是一个无服务器平台，用于生成企业工作流和集成。
- **连接器**。 逻辑应用使用[连接器](https://docs.microsoft.com/azure/connectors/apis-list)连接到常用的服务。 逻辑应用已有数百个不同的连接器，但你也可以创建自定义连接器。
- **Azure 服务总线**。 [服务总线](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview)提供安全可靠的消息传递。 可以使用消息传递来解耦应用程序，以及与其他基于消息的系统相集成。
- **Azure 事件网格**。 [事件网格](https://docs.microsoft.com/azure/event-grid/overview)是用于发布和传送应用程序事件的无服务器平台。
- **IP 地址**。 Azure API 管理服务具有固定的公共 [IP 地址](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)和域名。 域名是 azure-api.net 的子域，例如 contoso..azure-api.net。 逻辑应用和服务总线也具有公共 IP 地址。 但是，在此体系结构中，仅限 API 管理的 IP 地址调用逻辑应用终结点（出于安全考虑）。 对服务总线的调用受共享访问签名 (SAS) 的保护。
- **Azure DNS**。 [Azure DNS](https://docs.microsoft.com/azure/dns/) 是 DNS 域的托管服务。 Azure DNS 使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。 若要使用自定义域名（例如 contoso.com），请创建可将自定义域名映射到 IP 地址的 DNS 记录。 有关详细信息，请参阅[在 API 管理中配置自定义域名](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain)。
- **Azure Active Directory (Azure AD)**。 使用 [Azure AD](https://docs.microsoft.com/azure/active-directory/) 或其他标识提供者进行身份验证。 Azure AD 为 API 终结点访问提供身份验证（通过传递 [API 管理的 JSON Web 令牌](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims)进行验证）。 Azure AD 可以保护对 API 管理开发人员门户的访问（仅限标准层和高级层）。

该体系结构具有一些基本的运行模式：

* 现有的后端 HTTP API 通过 API 管理开发人员门户发布。 在该门户中，开发人员（组织内部和/或外部）可以将对这些 API 的调用集成到应用程序中。
* 通过以下方式生成复合 API：使用逻辑应用，并协调对服务型软件 (SaaS) 系统、Azure 服务和发布到 API 管理的任何 API 的调用。 [逻辑应用也](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api)通过 API 管理开发人员门户发布。
- 应用程序使用 Azure AD 获取访问 API 所需的 [OAuth 2.0 安全令牌](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)。
- API 管理[验证安全令牌](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)，然后将请求传递给后端 API 或逻辑应用。
- 服务总线队列用于[解耦](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview)应用程序活动，以及[将负载高峰平稳化](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling)。 使用逻辑应用或第三方应用程序将消息添加到队列中，或者（图中未显示）通过使用 API 管理将队列发布为 HTTP API 来执行此操作。
- 将消息添加到服务总线队列后，将触发事件。 此事件触发逻辑应用。 然后，逻辑应用处理消息。
- 其他 Azure 服务（例如 Azure Blob 存储和 Azure 事件中心）也会将事件发布到事件网格。 这些服务触发逻辑应用来接收事件并执行后续操作。

## <a name="recommendations"></a>建议

你的具体要求可能与本文所述的通用体系结构不同。 请使用本部分中的建议作为入手点。

### <a name="service-bus-tier"></a>服务总线层

使用服务总线高级层。 高级层支持事件网格通知。 有关详细信息，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。

### <a name="event-grid-pricing"></a>事件网格定价

事件网格使用无服务器模型。 根据操作（事件执行）数目计算费用。 有关详细信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。 目前，事件网格没有层方面的注意事项。

### <a name="use-peeklock-to-consume-service-bus-messages"></a>使用服务总线消息时使用 PeekLock

创建逻辑应用来使用服务总线消息时，请使用逻辑应用中的 [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) 来访问一组消息。 使用 PeekLock 时，逻辑应用可以执行步骤来验证每条消息，然后完成或放弃该消息。 此方法可以防范意外的消息丢失。

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>事件网格触发器触发时检查多个对象

事件网格触发器的触发仅仅意味着“发生了其中的至少 1 个事件”。 例如，当事件网格针对服务总线队列中显示的消息触发某个逻辑应用时，该逻辑应用应始终假设存在一条或多条可处理的消息。

### <a name="region"></a>区域

在同一区域中预配 API 管理、逻辑应用和服务总线可以最大程度地降低网络延迟。 一般情况下，应选择离用户最近的区域。

资源组也有一个区域。 该区域指定了部署元数据的存储位置，以及执行部署模板的位置。 请将资源组及其资源放入同一个区域，以提高部署期间的可用性。

## <a name="scalability"></a>可伸缩性

服务总线高级层可以横向扩展消息传递单位数，以提高可伸缩性。 高级层配置可以包含 1 个、2 个或 4 个消息传递单位。 有关缩放服务总线的详细信息，请参阅[使用服务总线消息传递改进性能的最佳实践](../service-bus-messaging/service-bus-performance-improvements.md)。

## <a name="availability"></a>可用性

目前，Azure API 管理基本层、标准层和高级层的服务级别协议 (SLA) 为 99.9%。 在两个或更多个区域中至少部署了一个单位的高级层配置的 SLA 为 99.95%。

目前，Azure 逻辑应用的 SLA 为 99.9%。

### <a name="disaster-recovery"></a>灾难恢复

考虑在服务总线高级层中实施异地灾难恢复，以便在发生严重的服务中断时实现故障转移。 有关详细信息，请参阅 [Azure 服务总线异地灾难恢复](../service-bus-messaging/service-bus-geo-dr.md)。

## <a name="manageability"></a>可管理性

为生产、开发和测试环境创建单独的资源组。 通过使用单独的资源组，可以更方便地管理部署、删除测试部署，以及分配访问权限。

将资源分配到资源组时，请考虑以下因素：

- **生命周期**。 一般情况下，应将具有相同生命周期的资源放入同一个资源组。
- **访问**。 可以使用[基于角色的访问控制](../role-based-access-control/overview.md) (RBAC) 对组中的资源应用访问策略。
- **计费**。 可以查看资源组的累计成本。
- **API 管理的定价层**。 建议对开发和测试环境使用开发人员层。 对于预生产环境，建议部署生产环境的副本、运行测试，然后关闭该副本以尽量降低成本。

有关详细信息，请参阅 [Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md)。

### <a name="deployment"></a>部署

建议使用 [Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)部署 API 管理、逻辑应用、事件网格和服务总线。 使用模板可以通过 PowerShell 或 Azure CLI 更轻松地自动完成部署。

建议将 API 管理、任何单个逻辑应用、事件网格主题和服务总线命名空间放在其自身独立的资源管理器模板中。 使用独立的模板时，可以将资源存储在源代码管理系统中。 然后，可以在持续集成/持续部署 (CI/CD) 过程中统一或者逐个部署这些模板。

### <a name="diagnostics-and-monitoring"></a>诊断和监控

与 API 管理和逻辑应用一样，可以使用 Azure Monitor 监视服务总线。 Azure Monitor 根据为每个服务配置的指标提供信息。 Azure Monitor 默认处于启用状态。

## <a name="security"></a>“安全”

使用 SAS 保护服务总线。 可以使用 [SAS 身份验证](../service-bus-messaging/service-bus-sas.md)向具有特定权限的用户授予对服务总线资源的访问权限。 有关详细信息，请参阅[服务总线身份验证和授权](../service-bus-messaging/service-bus-authentication-and-authorization.md)。

如果需要将服务总线队列公开为 HTTP 终结点（以便发布新消息），应通过 API 管理将此终结点置前，以对其提供保护。 然后，可以相应地使用证书或 OAuth 来保护此终结点。 保护终结点最简单的方法是使用包含请求/响应 HTTP 触发器的逻辑应用作为中介。

事件网格通过验证代码保护事件传送。 如果通过逻辑应用使用事件，则会自动执行验证。 有关详细信息，请参阅[事件网格安全性和身份验证](../event-grid/security-authentication.md)。

## <a name="next-steps"></a>后续步骤

- 了解[简单的企业集成](logic-apps-architectures-simple-enterprise-integration.md)。