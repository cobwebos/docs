---
title: 查看服务提供商活动
description: 客户可以查看记录的活动，查看服务提供商通过 Azure 委派的资源管理执行的操作。
ms.date: 12/6/2019
ms.topic: conceptual
ms.openlocfilehash: 31a9e7ff80623cc59b0a2db5951dff95d3088b05
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749171"
---
# <a name="view-service-provider-activity"></a>查看服务提供商活动

已委派 Azure 委托资源管理订阅的客户可以[查看 Azure 活动日志](../../azure-monitor/platform/platform-logs-overview.md)数据，查看所执行的所有操作。 这使客户能够全面了解服务提供商通过 Azure 委派的资源管理执行的操作，以及客户自己 Azure Active Directory （Azure AD）租户中的用户完成的操作。

## <a name="view-activity-log-data"></a>查看活动日志数据

你可以从 "**监视**" 菜单中的 "Azure 门户"[查看活动日志](../../azure-monitor/platform/activity-log-view.md)。 若要将结果限制为特定的订阅，可以使用筛选器来选择特定的订阅。 您还可以通过编程方式[查看和检索活动日志事件](../../azure-monitor/platform/activity-log-view.md)。

> [!NOTE]
> 如果服务提供商的租户中的用户已被授予 "[读取](../../role-based-access-control/built-in-roles.md#reader)者" 角色（或另一个包含读者访问权限的内置角色），则该订阅在载入用于 Azure 委派的资源管理时，可以查看该订阅的活动日志结果。

在活动日志中，你将看到操作的名称及其状态，以及执行该操作的日期和时间。 "**按启动的事件**" 列显示哪个用户执行了该操作，无论该操作是由服务提供商的租户中的用户通过 Azure 委派的资源管理还是客户自己的租户中的用户。 请注意，将显示用户的名称，而不是该用户分配给该订阅的租户或角色。

记录的活动在过去90天的 Azure 门户中可用。 若要了解如何存储超过90天的此数据，请参阅[在 Azure Monitor 中的 Log Analytics 工作区中收集和分析 Azure 活动日志](../../azure-monitor/platform/activity-log-collect.md)

## <a name="set-alerts-for-critical-operations"></a>为关键操作设置警报

要了解服务提供商（或你自己的租户中的用户）正在执行的关键操作，建议创建[活动日志警报](../../azure-monitor/platform/activity-log-alerts.md)。 例如，你可能想要跟踪订阅的所有管理操作，或在删除特定资源组中的任何虚拟机时通知你。 创建警报时，它们将包括客户自己的租户中的用户以及任何管理租户中的操作。

有关详细信息，请参阅[创建和管理活动日志警报](../../azure-monitor/platform/alerts-activity-log.md)。

## <a name="create-log-queries"></a>创建日志查询

你可以创建查询来分析你记录的活动或关注特定的项目。 例如，可能审核要求您报告对订阅执行的所有管理级别的操作。 您可以创建一个查询来仅筛选这些操作并按用户、日期或其他值对结果进行排序。

有关详细信息，请参阅[Azure Monitor 中的日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor](../../azure-monitor/index.yml)。
- 了解如何在 Azure 门户中[查看和管理服务提供商产品/服务](view-manage-service-providers.md)。