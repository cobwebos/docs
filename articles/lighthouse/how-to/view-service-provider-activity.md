---
title: 查看服务提供商活动
description: 客户可以查看记录的活动，以查看服务提供商通过 Azure 委派的资源管理执行的操作。
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649630"
---
# <a name="view-service-provider-activity"></a>查看服务提供商活动

已委派 Azure 委派资源管理订阅的客户可以查看[Azure 活动日志](../../azure-monitor/platform/platform-logs-overview.md)数据以查看所有执行的操作。 这使客户能够全面了解服务提供商通过 Azure 委派资源管理执行的操作，以及客户自己的 Azure 活动目录 （Azure AD） 租户中用户执行的操作。

> [!TIP]
> 我们还提供了 Azure 策略内置策略定义，用于审核作用域委派给管理租户。 有关详细信息，请参阅[环境中的审核授权](view-manage-service-providers.md#audit-delegations-in-your-environment)。

## <a name="view-activity-log-data"></a>查看活动日志数据

您可以在 Azure 门户中的 **"监视器"** 菜单[中查看活动日志](../../azure-monitor/platform/activity-log-view.md)。 要将结果限制为特定订阅，请使用筛选器选择特定订阅。 您还可以以编程方式[查看和检索活动日志事件](../../azure-monitor/platform/activity-log-view.md)。

> [!NOTE]
> 服务提供商租户中的用户可以查看客户租户中委派订阅的活动日志结果，如果该订阅已注册用于 Azure 委派的资源管理，则他们被授予[Reader](../../role-based-access-control/built-in-roles.md#reader)角色（或包含 Reader 访问权限的另一个内置角色）。

在活动日志中，您将看到操作的名称及其状态，以及执行操作的日期和时间。 **由列启动的事件**显示执行该操作的用户，无论是服务提供商租户中通过 Azure 委派的资源管理执行的用户，还是客户自己的租户中的用户。 请注意，将显示用户的名称，而不是租户或已为该订阅分配的用户的角色。

记录的活动在过去 90 天内在 Azure 门户中可用。 要了解如何存储此数据超过 90 天，请参阅[在日志分析工作区中收集和分析 Azure 活动日志](../../azure-monitor/platform/activity-log-collect.md)。

> [!NOTE]
> 服务提供商的用户将显示在活动日志中，但这些用户及其角色分配未显示在**访问控制 （IAM）** 中或通过 API 检索角色分配信息时。

## <a name="set-alerts-for-critical-operations"></a>为关键操作设置警报

为了了解服务提供商（或您自己的租户中的用户）正在执行的关键操作，我们建议创建[活动日志警报](../../azure-monitor/platform/activity-log-alerts.md)。 例如，您可能希望跟踪订阅的所有管理操作，或者在删除特定资源组中的任何虚拟机时收到通知。 创建警报时，这些警报将包括客户自己的租户中的用户以及任何管理租户执行的操作。

有关详细信息，请参阅[创建和管理活动日志警报](../../azure-monitor/platform/alerts-activity-log.md)。

## <a name="create-log-queries"></a>创建日志查询

您可以创建查询来分析记录的活动或关注特定项目。 例如，审核可能要求您报告对订阅执行的所有管理级操作。 您可以创建查询以仅筛选这些操作，并按用户、日期或其他值对结果进行排序。

有关详细信息，请参阅[Azure 监视器 中的日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。

## <a name="next-steps"></a>后续步骤

- 了解有关[Azure 监视器](../../azure-monitor/index.yml)的更多。
- 了解如何在 Azure 门户中[查看和管理服务提供商产品/服务](view-manage-service-providers.md)。