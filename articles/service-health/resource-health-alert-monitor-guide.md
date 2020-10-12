---
title: 使用 Azure 门户创建资源运行状况警报
description: 使用 Azure 门户创建在 Azure 资源不可用时发出通知的警报。
ms.topic: conceptual
ms.date: 6/23/2020
ms.openlocfilehash: 127aad1691f7bb6b6c64332eefde734a809ab75f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540670"
---
# <a name="configure-resource-health-alerts-using-azure-portal"></a>使用 Azure 门户配置资源运行状况警报

本文演示如何使用 Azure 门户设置活动日志警报，用于通知资源运行状况。

通过 Azure 资源运行状况可得知 Azure 资源的当前及历史运行状况。 Azure 资源运行状况警报会在这些资源的运行状况发生变化时几乎实时地发出通知。 通过以编程方式创建资源运行状况警报，用户可以批量创建警报并对其进行自定义。

资源运行状况通知存储在 [Azure 活动日志](../azure-monitor/platform/platform-logs-overview.md)中。鉴于活动日志中存储的信息量可能很大，Azure 门户提供了一个单独的用户界面，让你可以更轻松地查看和设置有关资源运行状况通知的警报。
当 Azure 资源将资源运行状况通知发送到 Azure 订阅时，你可以收到警报。 可以基于以下内容配置警报：

* 受影响的订阅。
* 受影响的资源类型。
* 受影响的资源组。
* 受影响的资源。
* 受影响资源的事件状态。
* 受影响资源的状态。
* 受影响资源的原因类型。

还可以配置向其发送警报的人员：

* 选择现有操作组。
* 创建新操作组（可以用于将来的警报）。

若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../azure-monitor/platform/action-groups.md)。

有关如何使用 Azure 资源管理器模板配置资源运行状况通知警报的信息，请参阅[资源管理器模板](./resource-health-alert-arm-template-guide.md)。
使用 Azure 门户配置资源运行状况警报

## <a name="resource-health-alert-using-azure-portal"></a>使用 Azure 门户配置资源运行状况警报

1. 在 Azure [门户](https://portal.azure.com/)中，选择“服务运行状况”。

    ![服务运行状况选择](./media/resource-health-alert-monitor-guide/service-health-selection.png)
2. 在“资源运行状况”部分中，选择“服务运行状况”。
3. 选择“添加资源运行状况警报”，并填写相应字段。
4. 在“警报目标”下，选择要针对其发出警报的订阅、资源类型、资源组和资源。

    ![“目标选择”选择](./media/resource-health-alert-monitor-guide/alert-target.png)

5. 在“警报条件”下选择：
    1. 要针对其发出警报的事件状态。 事件的严重级别：活动、已解决、正在进行、已更新
    2. 要针对其发出警报的资源状态。 事件的资源状态：可用、不可用、未知、已降级
    3. 要针对其发出警报的原因类型。 事件发生原因：平台启动、用户启动![警报条件选择 运行状况选择](./media/resource-health-alert-monitor-guide/alert-condition.png)
6. 在“定义警报详细信息”下提供以下详细信息：
    1. **警报规则名称**：新警报规则的名称。
    2. **说明**：新警报规则的说明。
    3. **将警报保存到资源组**：选择要在其中保存此新规则的资源组。
7. 在“操作组”下，从下拉菜单中指定要分配到此新警报规则的操作组。 或者，[创建新的操作组](../azure-monitor/platform/action-groups.md)并将其分配到新规则。 若要创建新组，请选择 "+ **新建组**"。
8. 若要在创建规则后启用规则，请选择“创建后启用规则”选项对应的“是”。 
9. 选择“创建警报规则”。

随即会为活动日志创建新的警报规则，并且窗口的右上角会显示一条确认消息。
可以启用、禁用、编辑或删除规则。 详细了解[如何管理活动日志规则](../azure-monitor/platform/alerts-activity-log.md#view-and-manage-in-the-azure-portal)。

## <a name="next-steps"></a>后续步骤

了解有关资源运行状况的详细信息：

* [Azure 资源运行状况概述](Resource-health-overview.md)
* [可通过 Azure 资源运行状况使用的资源类型和运行状况检查](resource-health-checks-resource-types.md)

创建服务运行状况警报：

* [配置服务运行状况的警报](./alerts-activity-log-service-notifications-portal.md) 
* [Azure 活动日志事件架构](../azure-monitor/platform/activity-log-schema.md)
* [使用资源管理器模板创建资源运行状况警报](./resource-health-alert-arm-template-guide.md)
