---
title: 使用 Azure Monitor 中的新警报（预览版）体验创建和管理活动日志警报 | Microsoft Docs
description: 本文介绍如何通过 Azure Monitor 中的“警报”（预览版）选项卡创建活动日志警报。 此文将详细介绍此功能的新用户体验。
author: JYOTHIRMAISURI
manager: vvithal
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: aabc0e57-78cd-44dd-a8d1-af5e1e567360
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: v-jysur
ms.custom: ''
ms.openlocfilehash: 740edfd158d56fcb224cf93d1720a2330779bfd6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="create-activity-log-alerts-using-the-new-alerts-preview-experience"></a>使用新的警报（预览版）体验创建活动日志警报

活动日志警报是新发生的活动日志事件与警报中指定的条件匹配时激活的警报。

这些警报针对 Azure 资源，可以使用 Azure 资源管理器模板来创建。 此外，还可以在 Azure 门户中创建、更新或删除它们。 本文介绍活动日志警报背后的概念。 然后介绍如何在 Azure 门户中使用 [Azure 警报（预览版）](monitoring-overview-unified-alerts.md)的新体验针对活动日志事件设置警报。

通常，可以创建活动日志警报，以便在 Azure 订阅中的资源发生特定更改时接收通知（通常限于特定资源组或资源）。 例如，你可能希望在删除 **myProductionResourceGroup**（示例资源组）中的任何虚拟机时接收通知，或者，可能希望在任何新角色分配到订阅中的用户时接收通知。

可以基于活动日志事件的 JSON 对象中的任何顶层属性配置活动日志警报。 但是，门户显示以下部分中详细介绍的最常用选项：

>[!NOTE]

> 当类别是“管理”时，必须在警报中至少指定上述条件之一。 不能创建每次在活动日志中创建事件时激活的警报。
>

活动日志警报激活后会使用操作组生成操作或通知。 操作组是一组可重用的通知接收方，例如电子邮件地址、Webhook URL 或短信电话号码。 可以从多个警报中引用接收方，以集中和分组通知通道。 在定义活动日志警报时，有两个选项。 可以：

* 在活动日志警报中使用现有操作组。
* 创建新的操作组。

若要了解有关操作组的详细信息，请参阅[在 Azure 门户中创建和管理操作组](monitoring-action-groups.md)。

若要了解有关服务运行状况通知的详细信息，请参阅[接收有关服务运行状况通知的活动日志警报](monitoring-activity-log-alerts-on-service-notifications.md)。


## <a name="whats-new-in-alerts-preview-for-activity-logs"></a>什么警报预览版中为活动日志提供了哪些新功能？

[Azure 警报（预览版）](monitoring-overview-unified-alerts.md)现在为活动日志警报提供增强的用户体验。 使用[警报的增强型用户体验](monitoring-overview-unified-alerts.md)，可以：

- 通过“监视器” > “警报(预览版)”边栏选项卡[创建](#create-an-alert-rule-for-an-activity-log)和[管理](#view-and-manage-activity-log-alert-rules)活动日志警报规则。 详细了解[活动日志](monitoring-overview-activity-logs.md)。

- **适用于警报目标的新选项**：创建新的活动日志警报规则时，现在可以选择目标资源、资源组或订阅。


## <a name="create-an-alert-rule-for-an-activity-log"></a>针对活动日志创建警报规则

> [!NOTE]

>  创建警报规则时，请确保：

> - 范围中的订阅不同于创建警报的订阅。
- 条件必须是配置警报所依据的级别/状态/调用方/资源组/资源 ID/资源类型/事件类别。
- 警报配置 JSON 中没有“anyOf”条件或嵌套的条件（简单而言，只允许一个 allOf，而不允许更多的 allOf/anyOf）。


请按以下过程操作：

1. 在 Azure 门户中，选择“监视” > “警报(预览版)”。
2. 单击“警报(预览版)”窗口顶部的“新建警报规则”。

     ![新建警报规则](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     此时将显示“创建规则”窗口。

      ![新建警报规则选项](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. 在“定义警报条件”下提供以下信息，然后单击“完成”。

    - **警报目标：**查看并选择新警报的目标，使用“按订阅筛选” / “按资源类型筛选”，并从显示的列表中选择资源或资源组。

    > [!NOTE]

    > 可以选择资源、资源组或整个订阅。

    **警报目标示例视图**

     ![选择目标](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - 在“目标条件”下，单击“添加条件”，并选择“活动日志”作为信号类型。

    - 从显示的列表中选择信号。

    可为此目标信号选择日志历史记录时间线和相应的警报逻辑：

    **添加条件屏幕**

    ![添加条件](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **历史记录时间**：“过去 6/12/24 小时”、“过去一周”。

    **警报逻辑**：

     - **事件级别**：事件的严重级别（详细、信息性、警告、错误或严重）。
     - **状态**：事件的状态（“已启动”、“失败”或“成功”）。
     - **事件发起者**：也称为“调用方”；执行操作的用户的电子邮件地址或 Azure Active Directory 标识符。

        **应用了警报逻辑的示例信号图**：

        ![ 已选择条件](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. 在“定义警报规则详细信息”下提供以下详细信息：

    - **警报规则名称** – 新警报规则的名称
    - **说明** – 新警报规则的说明
    - **将警报保存到资源组** – 选择要在其中保存此新规则的资源组。

5. 在“操作组”下，从下拉菜单中指定要分配到此新警报规则的操作组。 或者，[创建新的操作组](monitoring-action-groups.md)并将其分配到新规则。 若要创建新组，请单击“+ 新建组”。

6. 若要在创建规则后启用规则，请单击“创建后启用规则”选项对应的“是”。
7. 单击“创建警报规则”。

    随即为活动日志创建了新的警报规则，同时，窗口的右上角会显示一条确认消息。

    ![ 已创建警报规则](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    可以启用、禁用、编辑或删除规则。 [详细了解](#view-and-manage-activity-log-alert-rules)如何管理活动日志规则。

## <a name="view-and-manage-activity-log-alert-rules"></a>查看和管理活动日志警报规则

1. 在 Azure 门户中，单击“监视” > “警报(预览版)”，然后单击窗口左上角的“管理规则”。

    ![ 管理警报规则](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    此时将显示可用规则的列表。

2. 搜索要修改的活动日志规则。

    ![ 搜索活动日志警报规则](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    可以使用可用的筛选器（“订阅”、“资源组”或“资源”）来查找想要编辑的活动规则。

    > [!NOTE]

    > 只能编辑“说明”、“目标条件”和“操作组”。

3.  选择规则并双击以编辑规则选项。 进行所需的更改，然后单击“保存”。

    ![ 管理警报规则](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  可以禁用、启用或删除规则。 根据步骤 2 中的详述选择规则后，在窗口顶部选择相应的选项。


## <a name="next-steps"></a>后续步骤

- [存档活动日志警报](monitoring-archive-activity-log.md)
- [将活动日志流式传输到事件中心](monitoring-stream-activity-logs-event-hubs.md)
- [迁移到活动日志](monitoring-migrate-management-alerts.md)
