---
title: 简单的企业集成体系结构模式 - Azure Integration Services
description: 本体系结构参考演示如何使用 Azure 逻辑应用和 Azure API 管理来实现一个简单的企业集成模式
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5ed6fa9f514bae3ea651edba6702714e2680091f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955939"
---
# <a name="simple-enterprise-integration-architecture"></a>简单的企业集成体系结构

本文介绍一种使用成熟做法的企业集成体系结构，在使用 Azure Integration Services 时，可将它应用到集成应用程序。 可将此体系结构用作需要 HTTP API、工作流和业务流程的许多不同应用程序模式的基础。

![体系结构图 - 简单的企业集成](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

本教程系列介绍可用于生成通用集成应用程序的可重用组件部分。 由于集成技术涉及到多种可能的应用，其范围从简单的点对点应用到完整的企业 Azure 服务总线应用不等，因此，请考虑需要为自己的应用和基础结构实施哪些组件。

## <a name="architecture-components"></a>体系结构组件

本企业集成体系结构包括以下组件：

- **资源组**：[资源组](../azure-resource-manager/resource-group-overview.md)是 Azure 资源的逻辑容器。

- **Azure API 管理**：[API 管理](https://docs.microsoft.com/azure/api-management/)服务是一个完全托管的平台，用于发布、保护和转换 HTTP API。

- **Azure API 管理开发人员门户**：在 Azure API 管理的每个实例中都可以访问[开发人员门户](../api-management/api-management-customize-styles.md)。 可在此门户中访问文档和代码示例。 还可以在开发人员门户中测试 API。

- **Azure 逻辑应用**：[逻辑应用](../logic-apps/logic-apps-overview.md)是用于生成企业工作流和集成的无服务器平台。

- **连接器**：逻辑应用使用[连接器](../connectors/apis-list.md)连接到常用的服务。 逻辑应用提供数百个连接器，但你也可以创建自定义的连接器。

- **IP 地址**：Azure API 管理服务具有固定的公共 [IP 地址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)和域名。 默认域名是 azure-api.net 的子域（例如 contoso.azure-api.net），但你也可以配置[自定义域](../api-management/configure-custom-domain.md)。 逻辑应用和服务总线也具有公共 IP 地址。 但是，出于安全考虑，在此体系结构中，仅限 API 管理的 IP 地址调用逻辑应用终结点。 对服务总线的调用受共享访问签名 (SAS) 的保护。

- **Azure DNS**：[Azure DNS](https://docs.microsoft.com/azure/dns/) 是 DNS 域的托管服务。 Azure DNS 使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。 若要使用自定义域名（例如 contoso.com），请创建可将自定义域名映射到 IP 地址的 DNS 记录。 有关详细信息，请参阅[在 API 管理中配置自定义域名](../api-management/configure-custom-domain.md)。

- **Azure Active Directory (Azure AD)**：可以使用 [Azure AD](https://docs.microsoft.com/azure/active-directory/) 或其他标识提供者进行身份验证。 Azure AD 为 API 终结点访问提供身份验证（通过传递 [API 管理的 JSON Web 令牌](../api-management/policies/authorize-request-based-on-jwt-claims.md)进行验证）。 对于标准层和高级层，Azure AD 可以保护对 API 管理开发人员门户的访问。

## <a name="patterns"></a>模式 

本体系结构使用一些基本的运行模式：

* 复合 API 是使用逻辑应用生成的，可协调对软件即服务 (SaaS) 系统、Azure 服务和发布到 API 管理的任何 API 的调用。 还可以[通过 API 管理开发人员门户发布](../api-management/import-logic-app-as-api.md)逻辑应用。

* 应用程序使用 Azure AD 获取访问 API 所需的 [OAuth 2.0 安全令牌](../api-management/api-management-howto-protect-backend-with-aad.md)。

* Azure API 管理[验证安全令牌](../api-management/api-management-howto-protect-backend-with-aad.md)，然后将请求传递给后端 API 或逻辑应用。

## <a name="recommendations"></a>建议

你的具体要求可能与本文所述的通用体系结构不同。 请使用本部分中的建议作为入手点。

### <a name="azure-api-management-tier"></a>Azure API 管理层

使用 API 管理基本层、标准层或高级层。 这些层提供生产服务级别协议 (SLA)，并支持 Azure 区域中的横向扩展。 单位数因层而异。 高级层还支持跨多个 Azure 区域横向扩展。 请根据功能集和所需的吞吐量级别选择层。 有关详细信息，请参阅 [API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)。

需要对所有运行的 API 管理实例付费。 如果已纵向扩展，并且始终不需要该性能级别，请考虑利用 API 管理的按小时计费和缩减。

### <a name="logic-apps-pricing"></a>逻辑应用定价

逻辑应用使用[无服务器](../logic-apps/logic-apps-serverless-overview.md)模型。 根据操作和连接器执行计算费用。 有关详细信息，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 目前，逻辑应用没有层方面的注意事项。

### <a name="logic-apps-for-asynchronous-api-calls"></a>将逻辑应用用于异步 API 调用

逻辑应用最适合不需要低延迟的场景。 例如，逻辑应用最适合异步或运行时间适中的 API 调用。 如果需要保证低延迟（例如，某个调用会导致用户界面停滞），请使用其他技术来实现 API 或操作。 例如，使用 Azure Functions 或通过 Azure 应用服务部署的 Web API。 对于 API 使用者，请优先 API 管理而不是 API。

### <a name="region"></a>区域

为了最大程度地降低网络延迟，请在同一区域中预配 API 管理、逻辑应用和服务总线。 一般情况下，应选择离用户最近的区域。

资源组也有一个区域。 此区域指定了部署元数据的存储位置，以及执行部署模板的位置。 为了提高部署期间的可用性，请将资源组和资源放在同一区域。

## <a name="scalability"></a>可伸缩性

为了在管理 API 管理服务时提高可伸缩性，请添加[缓存策略](../api-management/api-management-howto-cache.md)（如果适用）。 缓存还有助于减少后端服务的负载。

若要提高容量，可在 Azure 区域中横向扩展 Azure API 管理基本层、标准层和高级层。 若要分析服务的使用情况，请在“指标”菜单中选择“容量指标”选项，然后相应地纵向扩展或缩减。

有关缩放 API 管理服务的建议：

- 缩放时请考虑流量模式。 采用较高易失性流量模式的客户需要更多的容量。

- 如果容量一贯超过 66%，则可能表示需要纵向扩展。

- 如果容量一贯低于 20%，则可能表示能够缩减。

- 在生产环境中启用负载之前，请始终使用有代表性的负载对 API 管理服务进行负载测试。

可跨多个 Azure 区域横向扩展高级层服务。 如果通过跨多个 Azure 区域缩放服务进行部署，则可以获得更高的 SLA（99.95%，而不是 99.9%），并可以在多个区域中靠近用户的位置预配服务。

逻辑应用的无服务器模型意味着管理员无需规划服务可伸缩性。 服务会根据需求自动缩放。

## <a name="availability"></a>可用性

* 目前，Azure API 管理的基本层、标准层和高级层的服务级别协议 (SLA) 为 99.9%。 对于在两个或更多个区域中至少部署了一个单位的高级层配置，SLA 为 99.95%。

* Azure 逻辑应用的 SLA 目前为 99.9%。

### <a name="backups"></a>备份

请根据更改频率，[定期备份](../api-management/api-management-howto-disaster-recovery-backup-restore.md) Azure API 管理配置。 将备份文件存储在不包含你的服务的某个位置或 Azure 区域。 然后，可以选择以下任一选项作为灾难恢复策略：

* 在灾难恢复事件中，预配新的 API 管理实例，将备份还原到新实例，并重新指向 DNS 记录。

* 在另一个 Azure 区域中保留服务的被动副本（会产生额外的费用）。 定期将备份还原到该副本中。 若要在发生灾难恢复事件期间还原服务，只需重新指向 DNS 记录。

由于可以快速重新创建无服务器逻辑应用，因此，保存关联 Azure 资源管理器模板的副本即可备份逻辑应用。 可将模板保存在源代码管理中，并将模板与持续集成/持续部署 (CI/CD) 过程相集成。

如果通过 Azure API 管理发布了一个逻辑应用，而该逻辑应用转移到了不同的数据中心，请更新该应用的位置。 可以使用一个基本的 PowerShell 脚本来更新 API 的**后端**属性。

## <a name="manageability"></a>可管理性

为生产、开发和测试环境创建单独的资源组。 使用单独的资源组可以更方便地管理部署、删除测试部署，以及分配访问权限。

将资源分配到资源组时，请考虑以下因素：

* **生命周期**：一般情况下，应将具有相同生命周期的资源放入同一个资源组。

* **访问**：可以使用[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 对组中的资源应用访问策略。

* **计费**：可以查看资源组的累计成本。

* **API 管理的定价层**：对开发和测试环境使用开发人员层。 为了尽量降低预生产期间的成本，请部署生产环境的副本、运行测试，然后关闭。

有关详细信息，请参阅 [Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md)。

## <a name="deployment"></a>部署

* 若要部署 API 管理和逻辑应用，请使用 [Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)。 使用模板可以通过 PowerShell 或 Azure CLI 更轻松地自动完成部署。

* 将 API 管理和任何单个逻辑应用放在其自身独立的资源管理器模板中。 使用独立的模板可将资源存储在源代码管理系统中。 然后，可以在持续集成/持续部署 (CI/CD) 过程中统一或者逐个部署这些模板。

### <a name="versions"></a>版本

每当更改逻辑应用的配置或者通过资源管理器模板部署更新时，Azure 都会保留该版本的副本以提供方便，并保留具有运行历史记录的所有版本。 可以使用这些版本来跟踪历史更改，或者将某个版本提升为逻辑应用的当前配置。 例如，你可以有效地回滚逻辑应用。

Azure API 管理有两个不同但互补的[版本概念](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/)：

* 可让 API 使用者根据需要选择 API 版本（例如 v1、v2、beta 或 production）的版本。

* 可让 API 管理员安全地对 API 进行更改，然后结合可选的备注将这些更改部署给用户的修订版。

对于部署，请将 API 管理修订版视为一种安全进行更改、保留更改历史记录并让 API 使用者知道这些更改的方法。 可在开发环境中创建修订版，并使用资源管理器模板在其他环境中部署此项更改。

尽管在将这些更改指定为“当前”版本并将其提供给用户访问之前可以使用修订版来测试 API，但我们不建议使用此方法进行负载测试或集成测试。 应改用独立的测试环境或预生产环境。

### <a name="configuration-and-sensitive-information"></a>配置和敏感信息

切勿将密码、访问密钥或连接字符串签入源代码管理。 如果需要这些值，请使用适当的技术保护和部署这些值。 

在逻辑应用中，如果逻辑应用所需的任何敏感值不能在连接中创建，请将这些值存储在 Azure Key Vault 中，并从资源管理器模板引用它们。 请为每个环境使用部署模板参数和参数文件。 有关详细信息，请参阅[保护工作流中的参数和输入](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow)。

API 管理使用称作“命名值”或“属性”的对象管理机密。 这些对象安全地存储可通过 API 管理策略访问的值。 有关详细信息，请参阅[管理 API 管理中的密码](../api-management/api-management-howto-properties.md)。

## <a name="diagnostics-and-monitoring"></a>诊断和监控

可以在 [API 管理](../api-management/api-management-howto-use-azure-monitor.md)和[逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)中使用 [Azure Monitor](../azure-monitor/overview.md) 进行操作监视。 Azure Monitor 根据为每个服务配置的指标提供信息，默认已启用。

每个服务还提供以下选项：

* 若要进行更深入的分析和仪表板显示，可将逻辑应用日志发送到 [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md)。

* 若要进行 DevOps 监视，可为 API 管理配置 Azure Application Insights。

* API 管理支持[使用 Power BI 解决方案模板进行自定义 API 分析](http://aka.ms/apimpbi)。 可以使用此解决方案模板来创建自己的分析解决方案。 业务用户可在 Power BI 中查看报表。

## <a name="security"></a>安全

尽管此列表并未完整描述所有的安全最佳做法，但其中的某些安全注意事项与本文所述的体系结构中部署的 Azure 服务专门相关：

* 为确保用户拥有适当的访问级别，请使用基于角色的访问控制 (RBAC)。

* 使用 OAuth 或 OpenID Connect 保护 API 管理中的公共 API 终结点。 若要保护公共 API 终结点，请配置标识提供者并添加 JSON Web 令牌 (JWT) 验证策略。

* 使用相互身份验证证书从 API 管理连接到后端服务。

* 创建指向 API 管理 IP 地址的 IP 地址白名单，对基于 HTTP 触发器的逻辑应用进行保护。 IP 地址白名单可以防止在未事先通过 API 管理的情况下，从公共 Internet 调用逻辑应用。

## <a name="next-steps"></a>后续步骤

* 了解[企业集成与队列和事件](../logic-apps/logic-apps-architectures-enterprise-integration-with-queues-events.md)
