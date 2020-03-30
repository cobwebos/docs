---
title: 计划报告&监视部署 - Azure AD
description: 描述如何规划和执行报告和监视的 impl。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232108"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>规划 Azure 活动目录报告和监视部署

Azure 活动目录 （Azure AD） 报告和监视解决方案取决于法律、安全和操作要求以及现有环境和流程。 本文介绍了各种设计选项，并指导您制定正确的部署策略。

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Azure AD 报告和监视的优势

Azure AD 报告提供了环境中 Azure AD 活动的全面视图和日志，包括登录事件、审核事件和目录更改。

可以将提供的数据用于：

* 确定应用和服务的使用方式。

* 检测影响环境健康的潜在风险。

* 排除问题，阻止用户完成工作。

* 通过查看 Azure AD 目录更改的审核事件来获得见解。

> [!IMPORTANT]
> Azure AD 监视使您能够将 Azure AD 报告生成的日志路由到不同的目标系统。 然后，可以将其保存以供长期使用，或者将其与第三方安全信息和事件管理 (SIEM) 工具集成，以便获取有关环境的见解。

使用 Azure AD 监视，可以将日志路由到：

* 用于存档的 Azure 存储帐户。
* Azure 监视器日志（以前称为 Azure 日志分析工作区），您可以在其中分析数据、创建仪表板并针对特定事件发出警报。
* Azure 事件中心，您可以在其中与现有的 SIEM 工具（如 Splunk、相扑或 QRadar）集成。

> [!NOTE]
我们最近开始使用术语 Azure 监视器日志而不是日志分析。 日志数据仍然存储在 Log Analytics 工作区中，并仍然由同一 Log Analytics 服务收集并分析。 我们正在更新术语，以便更好地反映 [Azure Monitor 中的日志](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection)的角色。 有关详细信息，请参阅 [Azure Monitor 术语更改](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand)。

[了解有关报表保留策略的更多详细信息](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)。

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD 报告和监视的许可和先决条件

您将需要 Azure AD 高级许可证才能访问 Azure AD 登录日志。

有关[Azure 活动目录定价指南](https://azure.microsoft.com/pricing/details/active-directory/)中的详细信息和许可信息。

要部署 Azure AD 监视和报告，您需要一个用户，该用户是 Azure AD 租户的全局管理员或安全管理员。

根据日志数据的最终目的地，您需要以下之一：

* 你对其拥有 ListKeys 权限的 Azure 存储帐户。 建议使用常规存储帐户而非 Blob 存储帐户。 有关存储定价信息，请查看 [Azure 存储定价计算器](https://azure.microsoft.com/pricing/calculator/?service=storage)。

* 要与第三方 SIEM 解决方案集成的 Azure 事件中心命名空间。

* 用于将日志发送到 Azure Monitor 日志的 Azure Log Analytics 工作区。

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>规划 Azure 报告和监视部署项目

在此项目中，您将定义将用于监视报表的受众群体，并定义 Azure AD 监视体系结构。

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时，失败的原因往往是对影响、结果和责任的预期不符。 为了避免这些陷阱[，请确保您与合适的利益相关者接洽](https://aka.ms/deploymentplans)。 此外，通过记录利益相关者及其项目投入和问责制，确保利益相关者在项目中的角色得到充分理解。

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功至关重要。 主动与用户沟通他们的体验将如何改变、何时更改，以及如果他们遇到问题如何获得支持。

### <a name="document-your-current-infrastructure-and-policies"></a>记录当前基础架构和策略

您当前的基础架构和政策将推动您的报告和监视设计。 确保您知道

* 您使用的 SIEM 工具（如果有）是什么（

* Azure 基础结构，包括正在使用的现有存储帐户和监视。

* 日志的组织保留策略，包括所需的任何适用的合规性框架。 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>规划 Azure AD 报告和监视部署

报告和监视用于满足您的业务需求、深入了解使用模式以及提高组织的安全状况。

### <a name="business-use-cases"></a>业务用例

* 满足业务需求所需的解决方案
* 满足业务需求的好去向
* 不适用

|区域 |描述 |
|-|-|
|保留| **日志保留超过30天**。 由于法律或业务需求，需要存储审核日志并登录 Azure AD 日志的时间超过 30 天。 |
|Analytics| **日志需要可搜索**。 存储的日志需要使用分析工具进行搜索。 |
| 操作见解| **对不同团队的见解**。 需要为不同的用户提供访问权限，以获得操作见解，如应用程序使用情况、登录错误、自助服务使用、趋势等。 |
| 安全洞察| **对不同团队的见解**。 需要为不同的用户提供访问权限，以获得操作见解，如应用程序使用情况、登录错误、自助服务使用、趋势等。 |
| SIEM 系统集成      | **SIEM 集成**。 需要将 Azure AD 登录日志和审核日志集成到现有 SIEM 系统中。 |

### <a name="choose-a-monitoring-solution-architecture"></a>选择监视解决方案体系结构

借助 Azure AD 监视，可以将 Azure AD 活动日志路由到最能满足业务需求的系统。 然后，您可以保留它们进行长期报告和分析，以便深入了解您的环境，并将其与 SIEM 工具集成。

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>决策流程图![显示后续部分中描述的内容的图像](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>存储帐户中的存档日志

通过将日志路由到 Azure 存储帐户，可以保留日志的时间超过[保留策略](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)中概述的默认保留期。 如果需要存档日志，但不需要将它们与 SIEM 系统集成，并且不需要正在进行的查询和分析，请使用此方法。 您仍然可以进行按需搜索。

了解如何[将数据路由到存储帐户](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)。

#### <a name="send-logs-to-azure-monitor-logs"></a>将日志发送到 Azure Monitor 日志

[Azure 监视器日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)合并来自不同源的监视数据。 它还提供了查询语言和分析引擎，使您能够深入了解应用程序的操作和资源的使用。 通过将 Azure AD 活动日志发送到 Azure 监视器日志，可以快速检索、监视和警报收集的数据。 如果没有要将数据直接发送到但需要查询和分析的现有 SIEM 解决方案，请使用此方法。 数据在 Azure 监视器日志中后，可以将其发送到事件中心，如果需要，可以从那里将其发送到 SIEM。

了解如何[将数据发送到 Azure Monitor 日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)。

还可以为 Azure AD 活动日志安装预构建的视图，以监视涉及登录和审核事件的常见方案。

了解如何[安装和使用 Azure AD 活动日志的日志分析视图](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)。

#### <a name="stream-logs-to-your-azure-event-hub"></a>将日志流式传输到 Azure 事件中心

将日志路由到 Azure 事件中心可与第三方 SIEM 工具集成。 可以通过此集成将 Azure AD 活动日志数据与其他由 SIEM 托管的数据组合起来，获取更丰富的有关环境的见解。 

了解如何[将日志流式传输到事件中心](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)。

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>为 Azure AD 报告和监视规划操作和安全性

利益干系人需要访问 Azure AD 日志才能获得操作见解。 可能的用户包括安全团队成员、内部或外部审核员以及身份和访问管理操作团队。

Azure AD 角色使您能够委派基于角色配置和查看 Azure AD 报表的能力。 确定组织中哪些人需要权限才能读取 Azure AD 报表，以及适合他们的角色。 

以下角色可以读取 Azure AD 报表：

* 全局管理员

* 安全管理员

* 安全读取者

* 报表读者

了解有关[Azure AD 管理角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)的更多详细信息。

*始终应用最低特权的概念，以降低帐户泄露的风险*。 请考虑实施[特权身份管理](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)，以进一步保护您的组织。

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>部署 Azure AD 报告和监视

根据您之前使用上述设计指南所做的决策，本节将指导您了解有关不同部署选项的文档。

### <a name="consume-and-archive-azure-ad-logs"></a>使用和存档 Azure AD 日志

[在 Azure 门户中查找活动报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[将 Azure AD 日志存档到 Azure 存储帐户](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>实施监控和分析

[将日志发送到 Azure 监视器](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[安装和使用用于 Azure Active Directory 的日志分析视图](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[使用 Azure Monitor 日志分析 Azure AD 活动日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [解释 Azure 监视器中的审核日志架构](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [在 Azure 监视器中解释登录日志架构](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [将 Azure AD 日志流式传输到 Azure 事件中心](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [使用 Azure Monitor 将 Azure AD 日志与 Splunk 集成](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [使用 Azure Monitor 将 Azure AD 日志与 SumoLogic 集成](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>后续步骤

考虑实施[特权身份管理](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 

考虑实施[基于角色的访问控制 （RBAC）](https://docs.microsoft.com/azure/role-based-access-control/overview)

 
