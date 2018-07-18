---
title: Azure Integration Services - 简单的企业集成
description: 演示如何使用 Azure 逻辑应用和 Azure API 管理实现简单企业集成模式的参考体系结构
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
ms.openlocfilehash: 17f591a470bf65d3c9365bca9c5ae2d5a6c9574f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231859"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>简单的企业集成 - 参考体系结构

## <a name="overview"></a>概述

此参考体系结构演示使用 Azure Integration Services 的集成应用程序适用的一套成熟做法。 此体系结构可充当需要 HTTP API、工作流和业务流程的许多不同应用程序模式的基础。

集成技术有许多可能的应用方式：从简单的点到点应用程序，到完整的企业服务总线。此体系结构系列为构建任何集成应用程序制定了可重用的组成部分 – 架构师应考虑需要对其应用程序和基础结构使用哪些组成部分。

   ![体系结构图 - 简单的企业集成](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>体系结构

此体系结构具有以下组件：

- 资源组。 资源组是 Azure 资源的逻辑容器。
- Azure API 管理。 Azure API 管理是一个完全托管的平台，用于发布、保护和转换 HTTP API。
- Azure API 管理开发人员门户。 每个 Azure API 管理实例都附带一个开发人员门户，用于访问文档、代码示例和测试 API。
- Azure 逻辑应用。 逻辑应用是一个无服务器平台，用于构建企业工作流和集成。
- 连接器。 逻辑应用使用连接器连接到常用的服务。 逻辑应用已有数百个不同的连接器，但也可以使用自定义连接器创建逻辑应用。
- IP 地址。 Azure API 管理服务具有固定的公共 IP 地址和域名。 域名是 azure-api.net 的子域，例如 contoso. azure-api.net。 逻辑应用还有一个公共的 IP 地址；但是，在此体系结构中，仅限 API 管理的 IP 地址调用逻辑应用终结点（出于安全考虑）。
- Azure DNS。 Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。 若要使用自定义域名（例如 contoso.com），请创建可将自定义域名映射到 IP 地址的 DNS 记录。 有关详细信息，请参阅“在 Azure API 管理中配置自定义域名”。
- Azure Active Directory (Azure AD)。 使用 Azure AD 或其他标识提供者进行身份验证。 Azure AD 提供身份验证来访问 API 终结点（通过传递 API 管理的 JSON Web 令牌进行验证），并可以保护对 API 管理开发人员门户的访问（仅限标准和高级层）。

此体系结构具有一些基本的运行模式：

1. 通过 API 管理开发人员门户发布现有的后端 HTTP API，使开发人员（组织内部和/或外部）能够将这些 API 的调用集成到应用程序中。
2. 使用逻辑应用构建复合 API；协调对 SAAS 系统、Azure 服务和发布到 API 管理的任何 API 的调用。 逻辑应用也通过 API 管理开发人员门户发布。
3. 应用程序获取所需的 OAuth 2.0 安全令牌，以便能够使用 Azure Active Directory 访问 API。
4. Azure API 管理验证安全令牌，并将请求传递给后端 API/逻辑应用。

## <a name="recommendations"></a>建议

你的要求可能不同于此处描述的体系结构。 请使用本部分中的建议作为入手点。

### <a name="azure-api-management-tier"></a>Azure API 管理层

使用基本、标准或高级层，因为这些提供生产 SLA，并支持 Azure 区域中的横向扩展（单位数因层而异）。 高级层还支持跨多个 Azure 区域横向扩展。 请根据所需的吞吐量级别和功能集选择层。 有关详细信息，请参阅 [API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。

需要对所有运行的 API 管理实例付费。 如果已纵向扩展，并且始终不需要该性能级别，请考虑利用 API 管理的按小时计费和纵向缩减。

### <a name="logic-apps-pricing"></a>逻辑应用定价

逻辑应用以[无服务器](logic-apps-serverless-overview.md)模型的形式工作 – 根据操作和连接器执行计算费用。 有关详细信息，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 目前，逻辑应用没有层方面的注意事项。

### <a name="logic-apps-for-asynchronous-api-calls"></a>将逻辑应用用于异步 API 调用

如果不需要保证较低的延迟，则非常适合使用逻辑应用 – 例如执行异步操作，或者运行时间适中的 API 调用。 如果需要保证低延迟（例如，某个调用会导致用户界面停滞），则我们建议使用不同的技术（例如 Azure Functions，或使用应用服务部署的 Web API）来实现该 API 或操作。 对于 API 使用者，我们仍建议优先使用此 API，而不是 API 管理。

### <a name="region"></a>区域

在同一区域中预配 API 管理和逻辑应用可以最大程度地降低网络延迟。 一般情况下，应选择离用户最近的区域。

资源组中还有一个区域指定了部署元数据的存储位置，以及执行部署模板的位置。 请资源组及其资源放入同一个区域。 这可以在部署过程中提高可用性。

## <a name="scalability-considerations"></a>可伸缩性注意事项

在适用的情况下，API 管理管理员应添加[缓存策略](../api-management/api-management-howto-cache.md)来提高服务的可伸缩性，以及减少其后端服务上的负载。

可在 Azure 区域中横向扩展 Azure API 管理基本、标准和高级层，以提高容量。 管理员可以使用“指标”菜单中的“容量指标”来分析其服务的使用情况，以及相应地纵向扩展或缩减。

有关缩放 API 管理服务的建议：

- 缩放需要考虑到流量模式 – 采用较高易失性流量模式的客户更需要提高容量。
- 如果容量一贯超过 66%，则可能表示需要纵向扩展。
- 如果容量一贯低于 20%，则可能表示能够纵向缩减。
- 在生产环境中启用 API 管理服务之前，我们始终建议使用有代表性的负载对该服务进行负载测试。

可跨多个 Azure 区域横向扩展高级层服务。 以这种方式进行部署的客户将获得更高的 SLA（99.95%，而不是 99.9%），并可以在多个区域中靠近用户的位置预配服务。

逻辑应用的无服务器模型意味着管理员不需要额外考虑服务的可伸缩性；服务会根据需求自动缩放。

## <a name="availability-considerations"></a>可用性注意事项

在撰写本文时，Azure API 管理基本、标准和高级层的服务级别协议 (SLA) 为 99.9%。 在两个或更多个区域中至少部署了一个单位的高级层配置的 SLA 为 99.95%。

在撰写本文时，Azure 逻辑应用的服务级别协议 (SLA) 为 99.9%。

### <a name="backups"></a>备份

应[定期备份](../api-management/api-management-howto-disaster-recovery-backup-restore.md) Azure API 管理的配置（根据更改频率适当备份），并将备份文件存储在该服务不在的某个位置或 Azure 区域。 然后，客户可为灾难恢复策略选择以下两个选项之一：

1. 在灾难恢复事件中，预配新的 API 管理实例、将备份还原到其中，并重新指向 DNS 记录。
2. 客户在另一个 Azure 区域中保留服务的被动副本（会产生额外的费用），并定期将备份还原到该副本。 在灾难恢复事件中，只需重新指向 DNS 记录即可还原服务。

由于逻辑应用可以快速重新创建并且是无服务器的，因此，保存关联 Azure 资源管理器模板的副本即可备份逻辑应用。 可将这些逻辑应用保存到源代码管理/集成到客户的持续集成/持续部署 (CI/CD) 过程。

如果通过 API 管理发布的逻辑应用转移到不同的数据中心，则需要更新其位置。 为此，可以通过一个简单的 PowerShell 脚本来更新 API 的后端属性。

## <a name="manageability-considerations"></a>可管理性注意事项

为生产、开发和测试环境创建单独的资源组。 这可以更方便地管理部署、删除测试部署，以及分配访问权限。
将资源分配到资源组时，请注意以下事项：

- 生命周期 一般情况下，应将具有相同生命周期的资源放入同一个资源组。
- 访问。 可以使用[基于角色的访问控制](../role-based-access-control/overview.md) (RBAC) 对组中的资源应用访问策略。
- 计费。 可以查看资源组的累计成本。
- API 管理的定价层 – 我们建议对开发和测试环境使用开发人员层。 对于预生产环境，我们建议部署生产环境的副本、运行测试，然后关闭该副本以尽量降低成本。

有关详细信息，请参阅[资源组](../azure-resource-manager/resource-group-overview.md)概述。

### <a name="deployment"></a>部署

我们建议使用 [Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)部署 Azure API 管理和 Azure 逻辑应用。 使用模板可以通过 PowerShell 或 Azure 命令行接口 (CLI) 更轻松地自动完成部署。

我们建议将 Azure API 管理和任何单个逻辑应用放在其自身独立的资源管理器模板中。 这样，便可将其存储在源代码管理系统中。 然后，可以在执行持续集成/持续部署 (CI/CD) 过程中，统一或者逐个部署这些模板。

### <a name="versions"></a>版本

每当对逻辑应用进行配置更改（或通过资源管理器模板部署更新）时，将会保留该版本的副本以提供方便（将保留具有运行历史记录的所有版本）。 可以使用这些版本来跟踪历史更改，并将某个版本提升为逻辑应用的当前配置；举例而言，这意味着可以有效地回滚逻辑应用。

API 管理有两个不同（但互补）的[版本概念](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/)：

- 让 API 使用者根据需要，选择要使用的 API（例如 v1、v2 或 beta、production）的版本。
- 让 API 管理员安全对 API 进行更改，并结合可选的备注将其部署给用户的版本。

在部署上下文中，应将 API 管理修订版视为安全进行更改、保留更改历史记录并让 API 使用者知道这些更改的方法。 可在开发环境中创建修订版，并使用资源管理器模板在其他环境之间部署该修订版。

尽管在将某个 API 指定为“当前”版本并将其提供给用户访问之前，可以使用修订版来测试该 API，但我们不建议使用此机制进行负载测试或集成测试 -- 应改用独立的测试环境或预生产环境。

### <a name="configuration"></a>配置

切勿将密码、访问密钥或连接字符串签入源代码管理。 如果需要这些值，请使用适当的技术来部署和保护这些值。 

逻辑应用中所需的任何敏感值（不能以连接的形式创建）应存储在 Azure Key Vault 中，并从资源管理器模板引用。 我们还建议结合适用于每个环境的参数文件使用部署模板参数。 [保护工作流中的参数和输入](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow)中提供了更多指导。

在 API 管理中，机密是使用称作命名值/属性的对象管理的。 这些对象安全地存储可在 API 管理策略中访问的值。 请参阅如何[在 API 管理中管理机密](../api-management/api-management-howto-properties.md)。

### <a name="diagnostics-and-monitoring"></a>诊断和监控

[API 管理](../api-management/api-management-howto-use-azure-monitor.md)和[逻辑应用](logic-apps-monitor-your-logic-apps.md)都支持通过 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) 执行操作监视。 Azure Monitor 默认已启用，会基于针对每个服务配置的不同指标提供信息。

此外，还为每个服务提供了其他选项：

- 可将逻辑应用日志发送到 [Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) 进行更深入的分析和显示（在仪表板上）。
- API 管理支持配置 Application Insights 用于开发运营监视。
- API 管理支持[使用 Power BI 解决方案模板进行自定义 API 分析](http://aka.ms/apimpbi)。 此解决方案模板可让客户创建自己的自定义分析解决方案，业务用户可在 Power BI 中查看报表。

## <a name="security-considerations"></a>安全注意事项

本部分列出专门与本文所述的体系结构中部署的 Azure 服务相关的安全注意事项， 内容并非安全最佳做法的完整列表。

- 使用基于角色的访问控制 (RBAC) 来确保用户拥有适当的访问级别。
- 使用 OAuth/Open IDConnect 保护 API 管理中的公共 API 终结点。 配置标识提供者并添加 JWT 验证策略即可实现此目的。
- 使用相互身份验证证书从 API 管理连接到后端服务
- 创建指向 API 管理 IP 地址的 IP 地址白名单，对基于 HTTP 触发器的逻辑应用进行保护。 这可以防止在不事先通过 API 管理的情况下，从公共 Internet 调用逻辑应用。

此参考体系结构演示了如何使用 Azure Integration Services 构建简单的企业集成平台。

## <a name="next-steps"></a>后续步骤

* [企业集成与队列和事件](logic-apps-architectures-enterprise-integration-with-queues-events.md)
