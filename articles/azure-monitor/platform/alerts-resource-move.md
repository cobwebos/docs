---
title: 如何在警报规则或操作规则的目标资源移动到其他 Azure 区域时对这些规则进行更新
description: 如何在警报规则或操作规则的目标资源移动到其他 Azure 区域时对这些规则进行更新 - 背景和说明。
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.subservice: alerts
ms.openlocfilehash: 4ea5c8552d35db67a1d2caf20c0143c74cdd642e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86505476"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>如何在警报规则或操作规则的目标资源移动到其他 Azure 区域时对这些规则进行更新

本文介绍了当你在区域之间移动其他 Azure 资源时，为何现有的[警报规则](./alerts-overview.md)和[操作规则](./alerts-action-rules.md)会受影响，以及如何识别和解决这些问题。 请查看主要的[资源移动文档](../../azure-resource-manager/management/move-region.md)，详细了解何时需要在区域之间移动资源，以及用于设计移动过程的清单。

## <a name="why-the-problem-exists"></a>存在此问题的原因

警报规则和操作规则引用其他 Azure 资源。 示例包括 [Azure VM](../../site-recovery/azure-to-azure-tutorial-migrate.md)、[Azure SQL](../../azure-sql/database/move-resources-across-regions.md) 和 [Azure 存储](../../storage/common/storage-account-move.md)。 当你移动这些规则所引用的资源时，这些规则可能会因为找不到其引用的资源而停止正常工作。

规则可能会在移动目标资源后停止工作的两个主要原因如下所述：

- 你的规则的范围显式引用了旧资源。
- 你的警报规则基于指标。

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>规则范围显式引用了旧资源

大多数情况下，当你移动资源时，其资源 ID 会发生更改。 在后台，系统会将该资源复制到新区域中，然后将其从旧区域中删除。 此过程要求两个资源以及两个不同的资源 ID 在一小段时间内同时存在。 由于资源 ID 必须是唯一的，因此在此过程中必须创建一个新 ID。 

**移动资源对现有规则有何影响？**

警报规则和操作规则在应用到资源时有一个资源范围。 该范围可以是整个订阅、某个资源组或者一个或多个特定资源。
例如，下面是范围为两个资源（两个虚拟机）的一个规则：

![多资源警报规则](media/alerts-resource-move/multi-resource-alert-rule.png)

如果规则范围显式提到了某个资源，而该资源已移动并更改了其资源 ID，则该规则会查找一个错误的或不存在的资源，因此会失败。

**如何解决此问题？**

更新或重新创建受影响的规则，使之指向新资源。 可以在本文的后面部分找到更新此范围的过程。

此问题影响这些规则类型：

- 活动日志警报规则
- 操作规则
- 经典警报
- 指标警报-有关详细信息，请参阅下一节 [基于指标的警报规则](#alert-rules-based-on-metrics)。

> [!NOTE]
> 日志搜索警报规则和智能检测器警报规则不受影响，因为它们的范围是工作区或 Application Insights。 这两种范围当前都不支持区域移动。

## <a name="alert-rules-based-on-metrics"></a>基于指标的警报规则

Azure 资源发出的指标是区域性的。 每次将资源移到新区域时，它就会开始在该新区域中发出指标。 因此，需要更新或重新创建基于指标的任何警报规则，使其指向正确区域中的当前指标流。

此说明同时适用于[指标警报规则](alerts-metric-overview.md)和[可用性测试警报规则](../app/monitor-web-app-availability.md)。

如果移动了范围中的**所有**资源，则无需重新创建规则。 可以直接更新警报规则的任何字段（例如警报规则说明），然后将其保存。
如果仅移动了范围中的某些资源，则需要从现有规则中删除已移动的资源，并创建一个仅涵盖已移动资源的新规则。

## <a name="procedures-to-fix-problems"></a>解决问题的过程

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>从 Azure 门户中查明与已移动资源关联的规则

- **对于警报规则** - 导航到“警报”>“管理警报规则”> 按包含项目的订阅和移动的资源进行筛选。
> [!NOTE]
> 活动日志警报规则不支持此过程。 无法更新活动日志警报规则的范围，也无法将其指向另一个订阅中的资源。 你可以改为创建新规则来替换旧规则。

- **对于操作规则** - 导航到“警报”>“管理操作”>“操作规则(预览版)”> 按包含项目的订阅和移动的资源进行筛选。

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>从 Azure 门户中更改规则的范围

1. 以单击方式打开你在上一步中查明的规则。
2. 在“资源”下，单击“编辑”并根据需要调整范围。
3. 根据需要调整规则的其他属性。
4. 单击“保存” 。

![更改警报规则范围](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板更改规则的范围

1. 获取规则的 Azure 资源管理器模板。  若要从 Azure 门户导出规则的模板，请执行以下操作：
   1. 在门户中导航到“资源组”部分，打开包含该规则的资源组。
   2. 在“概览”部分中，选中“显示隐藏的类型”复选框，并按规则的相关类型进行筛选。
   3. 选择相关规则以查看其详细信息。
   4. 在“设置”下，选择“导出模板”。 
2. 修改模板。 如果需要，请将其拆分为两个规则（这适用于指标警报的某些案例，如上文所述）。
3. 重新部署模板。

### <a name="change-scope-of-a-rule-using-rest-api"></a>使用 REST API 更改规则的范围

1. 获取现有规则（[指标警报](/rest/api/monitor/metricalerts/get)、[活动日志警报](/rest/api/monitor/activitylogalerts/get)）
2. 修改范围（[活动日志警报](/rest/api/monitor/activitylogalerts/update)）
3. 重新部署规则（[指标警报](/rest/api/monitor/metricalerts/createorupdate)、[活动日志警报](/rest/api/monitor/activitylogalerts/createorupdate)）

### <a name="change-scope-of-a-rule-using-powershell"></a>使用 PowerShell 更改规则的范围

1. 获取现有规则（[指标警报](/powershell/module/az.monitor/get-azmetricalertrulev2)、[活动日志警报](/powershell/module/az.monitor/get-azactivitylogalert)、[操作规则](/powershell/module/az.alertsmanagement/get-azactionrule)）。
2. 修改范围。 如果需要，请将其拆分为两个规则（这适用于指标警报的某些案例，如上文所述）。
3. 重新部署规则（[指标警报](/powershell/module/az.monitor/add-azmetricalertrulev2)、[活动日志警报](/powershell/module/az.monitor/enable-azactivitylogalert)、[操作规则](/powershell/module/az.alertsmanagement/set-azactionrule)）。

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>使用 Azure CLI 更改规则的范围

1.  获取现有规则（[指标警报](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-show)、[活动日志警报](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)）。
2.  直接更新规则范围（[指标警报](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update)、[活动日志警报](/cli/azure/monitor/activity-log/alert/scope)）
3.  如果需要，请将其拆分为两个规则（这适用于指标警报的某些案例，如上文所述）。

## <a name="next-steps"></a>后续步骤

了解如何解决[警报通知](alerts-troubleshoot.md)、[指标警报](alerts-troubleshoot-metric.md)和[日志警报](alerts-troubleshoot-log.md)的其他问题。 
