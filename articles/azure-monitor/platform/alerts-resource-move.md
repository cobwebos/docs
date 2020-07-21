---
title: 如何在其目标资源移到不同的 Azure 区域时更新警报规则或操作规则
description: 有关在其目标资源移到不同的 Azure 区域时，如何更新警报规则或操作规则的背景和说明。
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.subservice: alerts
ms.openlocfilehash: 4ea5c8552d35db67a1d2caf20c0143c74cdd642e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505476"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>如何在其目标资源移到不同的 Azure 区域时更新警报规则或操作规则

本文介绍当你在区域之间移动其他 Azure 资源时，可能会影响现有[警报规则](./alerts-overview.md)和[操作规则](./alerts-action-rules.md)，以及如何识别和解决这些问题。 请查看主[资源移动文档](../../azure-resource-manager/management/move-region.md)，以了解有关何时在区域之间移动资源的详细信息以及设计移动过程的清单的详细信息。

## <a name="why-the-problem-exists"></a>问题的原因

警报规则和操作规则引用其他 Azure 资源。 示例包括[Azure vm](../../site-recovery/azure-to-azure-tutorial-migrate.md)、 [Azure SQL](../../azure-sql/database/move-resources-across-regions.md)和[azure 存储](../../storage/common/storage-account-move.md)。 当你移动这些规则所引用的资源时，这些规则可能会因为无法找到其引用的资源而停止正常工作。

在移动目标资源后，规则可能会停止工作的两个主要原因：

- 规则范围显式引用旧资源。
- 预警规则基于指标。

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>规则作用域显式引用旧资源

移动资源时，在大多数情况下，其资源 ID 会发生更改。 在后台，系统将资源从旧区域中删除之前，将资源复制到新区域。 此过程需要两个资源，因此一小段时间内同时存在两个不同的资源 Id。 由于资源 Id 必须是唯一的，因此在处理过程中必须创建新 ID。 

**移动资源如何影响现有规则？**

警报规则和操作规则具有其适用的资源范围。 作用域可以是整个订阅、资源组或一个或多个特定资源。
例如，下面是一个具有两个资源（两个虚拟机）的作用域的规则：

![多资源警报规则](media/alerts-resource-move/multi-resource-alert-rule.png)

如果规则作用域显式提到某个资源，而该资源已移动并更改其资源 ID，则该规则将查找错误或不存在的资源，从而导致失败。

**如何解决此问题？**

更新或重新创建受影响的规则，以指向新资源。 更新范围的过程将在本文的后面部分找到。

此问题适用于这些规则类型：

- 活动日志警报规则
- 操作规则
- 经典警报
- 指标警报-有关详细信息，请参阅下一节[基于指标的警报规则](#alert-rules-based-on-metrics)。

> [!NOTE]
> 日志搜索警报规则和智能检测警报规则不受影响，因为它们的作用域是工作区或 Application Insights。 这两个作用域当前都不支持区域移动。

## <a name="alert-rules-based-on-metrics"></a>基于指标的警报规则

Azure 资源发出的指标为区域。 每次将资源移到新区域时，就会开始在新区域中发出指标。 因此，需要更新或重新创建任何基于指标的警报规则，以使其指向正确区域中的当前指标流。

此说明适用于[指标警报规则](alerts-metric-overview.md)和[可用性测试警报规则](../app/monitor-web-app-availability.md)。

如果作用域中的**所有**资源均已移动，则不需要重新创建规则。 只需更新预警规则的任何字段（如警报规则说明），然后将其保存即可。
如果**仅**移动范围中的某些资源，则需要从现有规则中删除已移动的资源，并创建仅包含已移动资源的新规则。

## <a name="procedures-to-fix-problems"></a>解决问题的过程

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>标识与 Azure 门户中的已移动资源关联的规则

- **对于警报规则**-导航到 "警报" > "管理警报规则" > 按包含订阅和移动的资源进行筛选。
> [!NOTE]
> 活动日志警报规则不支持此过程。 不能更新活动日志警报规则的作用域，并将它指向另一个订阅中的资源。 相反，你可以创建新的规则来替换旧的规则。

- **对于操作规则**-导航到 > 管理操作的警报 > 操作规则（预览版） > 按包含订阅和移动的资源进行筛选。

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>更改规则 Azure 门户范围

1. 打开你在上一步中标识的规则，方法是单击该规则。
2. 在 "**资源**" 下，单击 "**编辑**" 并根据需要调整范围。
3. 根据需要调整规则的其他属性。
4. 单击“ **保存**”。

![更改预警规则作用域](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板更改规则的范围

1. 获取规则的 Azure 资源管理器模板。  从 Azure 门户导出规则的模板：
   1. 导航到门户中的 "资源组" 部分，并打开包含该规则的资源组。
   2. 在 "概述" 部分中，选中 "**显示隐藏的类型**" 复选框，并按规则的相关类型进行筛选。
   3. 选择相关规则以查看其详细信息。
   4. 在 "**设置**" 下，选择 "**导出模板**"。
2. 修改模板。 如果需要，请拆分为两个规则（对于指标警报，如上文所述）。
3. 重新部署模板。

### <a name="change-scope-of-a-rule-using-rest-api"></a>使用 REST API 更改规则的作用域

1. 获取现有规则（[指标警报](/rest/api/monitor/metricalerts/get)、[活动日志警报](/rest/api/monitor/activitylogalerts/get)）
2. 修改作用域（[活动日志警报](/rest/api/monitor/activitylogalerts/update)）
3. 重新部署规则（[指标警报](/rest/api/monitor/metricalerts/createorupdate)、[活动日志警报](/rest/api/monitor/activitylogalerts/createorupdate)）

### <a name="change-scope-of-a-rule-using-powershell"></a>使用 PowerShell 更改规则的作用域

1. 获取现有规则（[指标警报](/powershell/module/az.monitor/get-azmetricalertrulev2)、[活动日志警报](/powershell/module/az.monitor/get-azactivitylogalert)和[操作规则](/powershell/module/az.alertsmanagement/get-azactionrule)）。
2. 修改作用域。 如果需要，请拆分为两个规则（对于指标警报，如上文所述）。
3. 重新部署规则（[指标警报](/powershell/module/az.monitor/add-azmetricalertrulev2)、[活动日志警报](/powershell/module/az.monitor/enable-azactivitylogalert)和[操作规则](/powershell/module/az.alertsmanagement/set-azactionrule)）。

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>使用 Azure CLI 更改规则的作用域

1.  获取现有规则（[指标警报](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-show)、[活动日志警报](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)）。
2.  直接更新规则作用域（[指标警报](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update)、[活动日志警报](/cli/azure/monitor/activity-log/alert/scope)）
3.  如果需要，请拆分为两个规则（对于指标警报，如上文所述）。

## <a name="next-steps"></a>后续步骤

了解如何解决[警报通知](alerts-troubleshoot.md)、[指标警报](alerts-troubleshoot-metric.md)和[日志警报](alerts-troubleshoot-log.md)的其他问题。 
