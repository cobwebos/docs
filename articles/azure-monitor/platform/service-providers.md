---
title: 服务提供商的 Azure 监视器日志 |微软文档
description: Azure 监视器日志可帮助托管服务提供商 （MSP）、大型企业、独立软件供应商 （ISV） 和托管服务提供商管理和监视客户本地或云基础结构中的服务器。
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658874"
---
# <a name="azure-monitor-logs-for-service-providers"></a>服务提供商的 Azure 监视器日志

Azure Monitor 中的 Log Analytics 工作区可以帮助托管服务提供商 (MSP)、大型企业、独立软件供应商 (ISV) 和主机托管服务提供商管理和监视客户本地或云基础结构中的服务器。

大型企业与服务提供商有许多相似之处，特别是当有一个集中式 IT 团队负责管理许多不同业务部门的 IT 时。 为了简单起见，本文档使用术语*服务提供商*，但同样的功能也可用于企业和其他客户。

对于属于[云解决方案提供商 （CSP）](https://partner.microsoft.com/Solutions/cloud-reseller-overview)计划的合作伙伴和服务提供商，Azure 监视器中的日志分析是 Azure CSP 订阅中可用的 Azure 服务之一。

Azure 监视器中的日志分析也可以由管理客户资源的服务提供商使用，这些服务提供程序可以通过[Azure 灯塔](https://docs.microsoft.com/azure/lighthouse/overview)中的 Azure 委派资源管理功能。

## <a name="architectures-for-service-providers"></a>适用于服务提供商的体系结构

借助 Log Analytics 工作区，管理员可控制[日志](data-platform-logs.md)数据的流动和隔离，并创建满足其特定业务需求的体系结构。 [此文](design-logs-deployment.md)介绍了工作区的设计、部署和迁移注意事项，[管理访问权限](manage-access.md)一文讨论了如何应用和管理日志数据的权限。 服务提供商有其他注意事项。

对于 Log Analytics 工作区，有三种可能的体系结构适用于服务提供商：

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. 分布式 - 日志存储在位于客户租户中的工作区中

在此体系结构中，工作区部署在客户的租户中，此租户用于该客户的所有日志。

服务提供商管理员可通过两种方式访问客户租户中的日志分析工作区：

- 客户可以将服务提供商中的单个用户添加为 Azure[活动目录来宾用户 （B2B）。](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) 服务提供商管理员必须登录到 Azure 门户中的每个客户的目录才能访问这些工作区。 这还需要客户管理每个服务提供商管理员的单独访问。
- 为了增强可扩展性和灵活性，服务提供商可以使用[Azure 灯塔](https://docs.microsoft.com/azure/lighthouse/overview)的[Azure 委派资源管理](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)功能访问客户的租户。 使用此方法，服务提供者管理员包含在服务提供商的租户中的 Azure AD 用户组中，并且在每个客户的入职过程中授予此组访问权限。 然后，这些管理员可以从自己的服务提供商租户中访问每个客户的工作区，而不必单独登录到每个客户的租户。 以这种方式访问客户的日志分析工作区资源可减少客户方面所需的工作，并可通过[Azure 监视器工作簿](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview)等工具更轻松地跨同一服务提供商管理的多个客户收集和分析数据。 有关详细信息，请参阅[大规模监视客户资源](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale)。

分布式体系结构的优点是：

* 客户可以通过[Azure 委派的资源管理](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)确认特定级别的权限，也可以使用他们自己的[基于角色的访问](https://docs.microsoft.com/azure/role-based-access-control/overview)来管理对日志的访问。
* 可以从所有类型的资源（而不仅仅是基于代理的 VM 数据）收集日志。 例如，Azure 审核日志。
* 每个客户都可以为其工作区设置不同的设置，例如保留期和数据上限。
* 在客户之间进行隔离以遵守监管和合规要求。
* 每个工作区的费用将计入客户的订阅中。

分布式体系结构的缺点是：

* 使用 Azure 监视器工作簿 等工具对客户租户进行集中可视化和分析数据可能会导致体验变慢，尤其是在分析 50 多个工作区中的数据时。
* 如果客户未加入 Azure 委派资源管理，则必须在客户目录中预配服务提供商管理员，并且服务提供商很难同时管理大量客户租户。

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. 中央 - 日志存储在位于服务提供商租户的工作区中

在此体系结构中，日志不会存储在客户的租户中，而是仅存储在服务提供商的某个订阅内的中心位置。 客户 VM 上安装的代理配置为使用工作区 ID 和密钥将其日志发送到此工作区。

集中式体系结构的优点是：

* 可轻松管理大量客户并将其集成到各种后端系统。
* 服务提供商对日志和各种项目（例如函数和保存的查询）拥有完全所有权。
* 服务提供商可以对其所有客户执行分析。

集中式体系结构的缺点是：

* 此体系结构仅适用于基于代理的 VM 数据，不涵盖 PaaS、SaaS 和 Azure 结构数据源。
* 将客户合并到单个工作区时，可能很难分隔客户之间的数据。 唯一效果不错的方法是使用计算机的完全限定域名 (FQDN) 或通过 Azure 订阅 ID 进行分隔。 
* 来自所有客户的全部数据都将存储在具有单独帐单和相同保留期及配置设置的同一区域中。
* Azure 结构及 Azure 诊断和 Azure 审核日志等 PaaS 服务要求工作区与资源位于同一租户中，因此它们无法将日志发送到中心工作区。
* 来自所有客户的全部 VM 代理都将使用相同的工作区 ID 和密钥对中心工作区进行身份验证。 无法在不干扰其他客户的情况下阻止来自特定客户的日志。

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. 混合日志存储在位于客户租户的工作区中，其中一些日志被拉到中心位置。

第三种体系结构混合使用两个选项。 它以第一种分布式体系结构为基础，其中日志对于每个客户而言位于本地，但通过某种机制创建了日志的中心存储库。 一部分日志被提取到中心位置用于报告和分析。 此部分可以是少量数据类型，也可以是每日统计数据等活动摘要。

在中心位置实现日志有两种选择：

1. 中心工作区：服务提供商可以在其租户中创建工作区，并使用利用[查询 API](https://dev.loganalytics.io/) 和[数据收集 API](../../azure-monitor/platform/data-collector-api.md) 的脚本将来自各个工作区的数据提取到此中心位置。 脚本之外的另一种选择是使用 [Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)。

2. Power BI 作为中心位置：当各种工作区使用日志分析工作区和[Power BI](../../azure-monitor/platform/powerbi.md)之间的集成将数据导出到它时，Power BI 可以充当中心位置。 

## <a name="next-steps"></a>后续步骤

* 使用[Resource Manager 模板](template-workspace-configuration.md)自动执行创建和配置工作区

* 使用 [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 自动创建工作区 

* 使用[警报](../../azure-monitor/platform/alerts-overview.md)以便与现有系统集成

* 使用 [Power BI](../../azure-monitor/platform/powerbi.md) 生成摘要报告

* 板载客户到[Azure 委派的资源管理](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)。
