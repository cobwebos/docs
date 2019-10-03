---
title: 使用 Azure Monitor 进行持续监视 | Microsoft Docs
description: 介绍使用 Azure Monitor 在整个工作流中启用持续监视的具体步骤。
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: bwren
ms.openlocfilehash: f101a8885d9bf67e8bd589d7cf932b0d35cdfe32
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744503"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>使用 Azure Monitor 进行持续监视

持续监视是指在 DevOps 和 IT 运营生命周期的每个阶段中整合监视功能所要采用的流程和技术。 它有助于持续确保应用程序和基础结构在从开发环境转移到生产环境时保持正常的运行状况、性能和可靠性。 持续监视构建在持续集成和持续部署 (CI/CD) 的概念基础之上。CI/CD 可帮助你更快、更可靠地开发和交付软件，为用户持续提供价值。

[Azure Monitor](overview.md) 是 Azure 中的统一监视解决方案，可跨云中和本地的应用程序与基础结构观察整个堆栈。 它可以在开发和测试过程中无缝配合 [Visual Studio 和 Visual Studio Code](https://visualstudio.microsoft.com/)，并可以在部署和运营过程中与 [Azure DevOps](/azure/devops/user-guide/index) 相集成，以提供发布管理和工作项管理。 它甚至可跨所选的 ITSM 和 SIEM 工具集成，以帮助跟踪现有 IT 流程中的问题和事件。

本文介绍使用 Azure Monitor 在整个工作流中启用持续监视的具体步骤。 其中还包含了详细介绍如何实施不同功能的其他文档的链接。


## <a name="enable-monitoring-for-all-your-applications"></a>为所有应用程序启用监视
若要观察整个环境，需在所有 Web 应用程序和服务中启用监视。 这样，便可以轻松可视化所有组件中的端到端事务和连接。

- [Azure DevOps Projects](../devops-project/overview.md) 提供一种简化的体验，在其中可以使用现有的代码和 Git 存储库，或者选择一个示例应用程序，来与 Azure 建立持续集成 (CI) 和持续交付 (CD) 管道。
- 使用 [DevOps 发布管道中的持续监视](../azure-monitor/app/continuous-monitoring.md)可以基于监视数据限制或回滚部署。
- 使用 [Status Monitor](../azure-monitor/app/monitor-performance-live-website-now.md) 可以配合 Azure Application Insights 检测 Windows 上的实时 .NET 应用，而无需修改或重新部署代码。
- 如果你有权访问应用程序的代码，则可以通过安装适用于 [.NET](../azure-monitor/learn/quick-monitor-portal.md)、[Java](../azure-monitor/app/java-get-started.md)、[Node.js](../azure-monitor/learn/nodejs-quick-start.md)或[其他任何编程语言](../azure-monitor/app/platforms.md)的 Azure Monitor Application Insights SDK，使用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 启用完全监视。 这样可以指定与应用程序和业务相关的自定义事件、指标或页面视图。



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>为整个基础结构启用监视
应用程序的可靠性只与其底层基础结构相当。 为整个基础结构启用监视有助于实现全面的观察，发生故障时，还可以更轻松地发现潜在的根本原因。 Azure Monitor 可帮助你跟踪整个混合基础结构（包括 VM、容器、存储和网络等资源）的运行状况与性能。

- 无需进行任何配置，即可自动从大多数 Azure 资源获取[平台指标、活动日志和诊断日志](platform/data-sources.md)。
- 使用[用于 VM 的 Azure Monitor](insights/vminsights-overview.md) 为 VM 启用更深入的监视。
-  使用[用于容器的 Azure Monitor](insights/container-insights-overview.md) 为 AKS 群集启用更深入的监视。
- 为环境中的不同应用程序和服务添加[监视解决方案](insights/solutions-inventory.md)。


[基础结构即代码](/azure/devops/learn/what-is-infrastructure-as-code)是描述性模型中的基础结构的管理系统，它使用版本控制方式与 DevOps 团队用于源代码管理的方式相同。 它提高了环境的可靠性和可伸缩性，并让你利用管理应用程序时所用的类似流程。

-  使用[资源管理器模板](platform/template-workspace-configuration.md)可以针对大量的资源启用监视和配置警报。
- 使用 [Azure Policy](../governance/policy/overview.md) 可对资源强制实施不同的规则。 这可以确保这些资源始终符合企业标准和服务级别协议。 


##  <a name="combine-resources-in-azure-resource-groups"></a>在 Azure 资源组中合并资源
当今 Azure 中的典型应用程序包含多个资源，例如，托管在云服务、AKS 群集中或 Service Fabric 中的 VM 和应用服务或微服务。 这些应用程序经常利用事件中心、存储、SQL 和服务总线等依赖项。

- 合并 Azure 资源组中的资源，以全面了解构成不同应用程序的所有资源。 使用[用于资源组的 Azure Monitor](../azure-monitor/insights/resource-group-insights.md) 可以方便地跟踪整个全堆栈应用程序的运行状况和性能，并可以出于任何调查或调试目的深入到相关组件。

## <a name="ensure-quality-through-continuous-deployment"></a>通过持续部署确保质量
使用持续集成/持续部署可以根据自动测试的结果，将代码更改自动集成和部署到应用程序。 它简化了部署过程，并确保任何更改在转移到生产环境之前具有可靠的质量。


- 使用 [Azure Pipelines](/azure/devops/pipelines) 可以实现持续部署，并可以根据 CI/CD 测试，自动化将代码提交到生产环境的整个流程。
- 使用[质量门限](/azure/devops/pipelines/release/approvals/gates)可将监视功能集成到部署前或部署后的流程。 这可以确保应用程序从开发环境转移到生产环境时可以符合关键的运行状况/性能指标 (KPI)，并且基础结构环境或规模的任何差异不会对 KPI 造成负面影响。
- 在开发、测试、Canary 和生产等不同的部署环境之间[维护独立的监视实例](../azure-monitor/app/separate-resources.md)。这可以确保收集的数据与关联的应用程序和基础结构相关。 如果需要跨环境关联数据，可以使用[指标资源管理器中的多资源图表](../azure-monitor/platform/metrics-charts.md)，或者[在 Azure Monitor 中创建跨资源查询](log-query/cross-workspace-query.md)。


## <a name="create-actionable-alerts-with-actions"></a>使用操作创建可操作警报
监视的一个重要方面是将任何当前问题和预测到的问题主动通知给管理员。 

- 基于日志和指标[在 Azure Monitor 中创建警报](../azure-monitor/platform/alerts-overview.md)可以识别到可预测的故障状态。 在使所有警报可操作方面应有一个目标，即，这些警报表示实际的关键状况，并且应该尽量减少误报。 使用[动态阈值](platform/alerts-dynamic-thresholds.md)可基于指标数据自动计算基线，而无需定义自己的静态阈值。 
- 为警报定义操作可以使用最有效的方式来通知管理员。 可用的[通知操作](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal)包括短信、电子邮件、推送通知或语音呼叫。
- 使用更高级的操作可以通过 [Webhook](platform/activity-log-alerts-webhook.md) [连接到 ITSM 工具](platform/itsmc-overview.md)或其他警报管理系统。
- 还可以使用 [Azure 自动化 Runbook](../automation/automation-webhooks.md) 或[逻辑应用](/connectors/custom-connectors/create-webhook-trigger)（可以通过 Webhook 从警报启动）来修正警报中识别到的问题。 
- 使用[自动缩放](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md)可以根据收集的指标动态增加和减少计算资源。

## <a name="prepare-dashboards-and-workbooks"></a>准备仪表板和工作簿
确保开发和运营部门有权访问相同的遥测功能和工具可让他们查看整个环境中的模式，并最大程度地减小平均检测时间 (MTTD) 和平均还原时间 (MTTR)。

- 根据组织中不同角色的通用指标和日志准备[自定义仪表板](../azure-monitor/learn/tutorial-app-dashboards.md)。 仪表板可以合并所有 Azure 资源的数据。
- 准备[工作簿](../azure-monitor/app/usage-workbooks.md)以确保在开发与运营部门之间分享知识。 可将这些工作簿准备为包含指标图表和日志查询的动态报表，甚至可由开发人员准备为故障排除指南，以帮助客户支持或运营人员处理基本问题。

## <a name="continuously-optimize"></a>持续优化
 监视是热门的“构建-度量-学习”理念的基本方面，该理念鼓励持续跟踪 KPI 和用户行为指标，然后努力通过规划迭代对其进行优化。 Azure Monitor 可以帮助收集业务相关的指标和日志，并在下一次部署中按需添加新的数据点。

- 使用 Application Insights 中的工具可以[跟踪最终用户行为和参与情况](../azure-monitor/learn/tutorial-users.md)。
- 使用[影响分析](../azure-monitor/app/usage-impact.md)可以帮助确定优先关注点，以提高重要 KPI。


## <a name="next-steps"></a>后续步骤

- 了解 [Azure Monitor](overview.md) 的不同组件。
- 向发布管道[添加持续监视](../azure-monitor/app/continuous-monitoring.md)。