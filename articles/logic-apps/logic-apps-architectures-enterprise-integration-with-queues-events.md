---
title: Azure Integration Services 参考体系结构
description: 演示如何使用 Azure 逻辑应用、API 管理、服务总线和事件网格实现企业集成模式的参考体系结构
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
ms.openlocfilehash: db3350b3c70f6e6f35949e8423334061849b7b37
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231930"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>企业集成与队列和事件：参考体系结构

## <a name="overview"></a>概述

此参考体系结构演示使用 Azure Integration Services 的集成应用程序适用的一套成熟做法。 此体系结构可充当需要 HTTP API、工作流和业务流程的许多不同应用程序模式的基础。

集成技术有许多可能的应用方式：从简单的点到点应用程序，到完整的企业服务总线。此体系结构系列为构建任何集成应用程序制定了可重用的组成部分 – 架构师应考虑需要对其应用程序和基础结构使用哪些组成部分。

![体系结构图 - 企业集成与队列和事件](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>体系结构

此体系结构基于[简单企业集成](logic-apps-architectures-simple-enterprise-integration.md)中所示的体系结构。 它包括以下组件：

- 资源组。 资源组是 Azure 资源的逻辑容器。
- Azure API 管理。 Azure API 管理是一个完全托管的平台，用于发布、保护和转换 HTTP API。
- Azure API 管理开发人员门户。 每个 Azure API 管理实例都附带一个开发人员门户，用于访问文档、代码示例和测试 API。
- Azure 逻辑应用。 逻辑应用是一个无服务器平台，用于构建企业工作流和集成。
- 连接器。 逻辑应用使用连接器连接到常用的服务。 逻辑应用已有数百个不同的连接器，但也可以使用自定义连接器创建逻辑应用。
- Azure 服务总线。 服务总线提供安全可靠的消息传递。 可以使用消息传递来解耦不同的应用程序，以及与其他基于消息的系统相集成。
- Azure 事件网格。 事件网格是用于发布和传送应用程序事件的无服务器平台。
- IP 地址。 Azure API 管理服务具有固定的公共 IP 地址和域名。 域名是 azure-api.net 的子域，例如 contoso..azure-api.net。 逻辑应用和服务总线还有一个公共的 IP 地址；但是，在此体系结构中，仅限 API 管理的 IP 地址调用逻辑应用终结点（出于安全考虑）。 对服务总线的调用受共享访问签名的保护。
- Azure DNS。 Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。 若要使用自定义域名（例如 contoso.com），请创建可将自定义域名映射到 IP 地址的 DNS 记录。 有关详细信息，请参阅“在 Azure API 管理中配置自定义域名”。
- Azure Active Directory (Azure AD)。 使用 Azure AD 或其他标识提供者进行身份验证。 Azure AD 提供身份验证来访问 API 终结点（通过传递 API 管理的 JSON Web 令牌进行验证），并可以保护对 API 管理开发人员门户的访问（仅限标准和高级层）。

此体系结构具有一些基本的运行模式：

1. 通过 API 管理开发人员门户发布现有的后端 HTTP API，使开发人员（组织内部和/或外部）能够将这些 API 的调用集成到应用程序中。
2. 使用逻辑应用构建复合 API；协调对 SAAS 系统、Azure 服务和发布到 API 管理的任何 API 的调用。 逻辑应用也通过 API 管理开发人员门户发布。
3. 应用程序获取所需的 OAuth 2.0 安全令牌，以便能够使用 Azure Active Directory 访问 API。
4. Azure API 管理验证安全令牌，并将请求传递给后端 API/逻辑应用。
5. 服务总线队列用于解耦应用程序活动，以及将负载高峰平稳化。 逻辑应用、第三方应用程序或（图中未显示）通过 API 管理将队列发布为 HTTP API，凭此将消息添加到队列。
6. 将消息添加到服务总线队列后，将触发事件。 逻辑应用由此事件触发，并处理消息。
7. 同样，其他 Azure 服务（例如 Blob 存储、事件中心）也会将事件发布到事件网格。 这些事件触发逻辑应用来接收事件并执行后续操作。

## <a name="recommendations"></a>建议

你的要求可能不同于此处描述的体系结构。 请使用本部分中的建议作为入手点。

### <a name="service-bus-tier"></a>服务总线层

使用服务总线高级层，因为此层目前支持事件网格通知（预期将来所有层会支持此功能）。 请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。

### <a name="event-grid-pricing"></a>事件网格定价

事件网格使用无服务器模型工作 – 根据操作数目（事件执行）计算费用。 有关详细信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。 目前，事件网格没有层方面的注意事项。

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>使用服务总线消息时使用 PeekLock

创建逻辑应用来使用服务总线消息时，请使用逻辑应用中的 [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) 来访问一组消息。 然后，逻辑应用可以执行步骤来验证每条消息，然后完成或放弃。 此方法可以防范意外的消息丢失。

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>事件网格触发器触发时检查多个对象

事件网格触发器的触发仅仅意味着“发生了其中的至少 1 个事件”。 例如，当事件网格针对服务总线队列中显示的消息触发某个逻辑应用时，该逻辑应用应始终假设存在一个或多个可处理的消息。

### <a name="region"></a>区域

在同一区域中预配 API 管理、逻辑应用和服务总线可以最大程度地降低网络延迟。 一般情况下，应选择离用户最近的区域。

资源组中还有一个区域指定了部署元数据的存储位置，以及执行部署模板的位置。 请资源组及其资源放入同一个区域。 这可以在部署过程中提高可用性。

## <a name="scalability-considerations"></a>可伸缩性注意事项

Azure 服务总线高级层可以横向扩展消息传递单位数，以提高可伸缩性。 高级层可以包含 1 个、2 个或 4 个消息传递单位。 有关缩放 Azure 服务总线的更多指导，请参阅[有关使用服务总线消息传递提高性能的最佳做法](../service-bus-messaging/service-bus-performance-improvements.md)。

## <a name="availability-considerations"></a>可用性注意事项

在撰写本文时，Azure API 管理基本、标准和高级层的服务级别协议 (SLA) 为 99.9%。 在两个或更多个区域中至少部署了一个单位的高级层配置的 SLA 为 99.95%。

在撰写本文时，Azure 逻辑应用的服务级别协议 (SLA) 为 99.9%。

### <a name="disaster-recovery"></a>灾难恢复

考虑在服务总线高级层中实施异地灾难恢复，以便在发生严重的服务中断时实现故障转移。 详细了解 [Azure 服务总线异地灾难恢复](../service-bus-messaging/service-bus-geo-dr.md)。

## <a name="manageability-considerations"></a>可管理性注意事项

为生产、开发和测试环境创建单独的资源组。 这可以更方便地管理部署、删除测试部署，以及分配访问权限。
将资源分配到资源组时，请注意以下事项：

- 生命周期 一般情况下，应将具有相同生命周期的资源放入同一个资源组。
- 访问。 可以使用[基于角色的访问控制](../role-based-access-control/overview.md) (RBAC) 对组中的资源应用访问策略。
- 计费。 可以查看资源组的累计成本。
- API 管理的定价层 – 我们建议对开发和测试环境使用开发人员层。 对于预生产环境，我们建议部署生产环境的副本、运行测试，然后关闭该副本以尽量降低成本。

有关详细信息，请参阅[资源组](../azure-resource-manager/resource-group-overview.md)概述。

### <a name="deployment"></a>部署

我们建议使用 [Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)部署 Azure API 管理、逻辑应用、事件网格和服务总线。 使用模板可以通过 PowerShell 或 Azure 命令行接口 (CLI) 更轻松地自动完成部署。

我们建议将 Azure API 管理、任何单个逻辑应用、事件网格主题和服务总线命名空间放在其自身独立的资源管理器模板中。 这样，便可将其存储在源代码管理系统中。 然后，可以在执行持续集成/持续部署 (CI/CD) 过程中，统一或者逐个部署这些模板。

### <a name="diagnostics-and-monitoring"></a>诊断和监控

与 API 管理和逻辑应用一样，可以使用 Azure Monitor 监视服务总线。 Azure Monitor 默认已启用，会基于针对每个服务配置的不同指标提供信息。

## <a name="security-considerations"></a>安全注意事项

使用共享访问签名保护服务总线。 通过 [SAS 身份验证](../service-bus-messaging/service-bus-sas.md)可向具有特定权限的用户授予对服务总线资源的访问权限。 详细了解[服务总线身份验证和授权](../service-bus-messaging/service-bus-authentication-and-authorization.md)。

此外，如果需要将服务总线队列公开为 HTTP 终结点（以便发布新消息），应在终结点的前面使用 API 管理来保护服务总线。 然后，可以相应地使用证书或 OAuth 来保护服务总线。 为此，最简单的方法是使用包含请求/响应 HTTP 触发器的逻辑应用作为中介。

事件网格通过验证代码保护事件传送。 如果通过逻辑应用使用事件，则会自动执行此操作。 详细了解[事件网格安全性和身份验证](../event-grid/security-authentication.md)。

## <a name="next-steps"></a>后续步骤

* [简单企业集成](logic-apps-architectures-simple-enterprise-integration.md)
