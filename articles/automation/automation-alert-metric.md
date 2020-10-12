---
title: 使用指标警报监视 Azure 自动化 Runbook
description: 本文介绍如何根据 Runbook 完成状态设置指标警报。
services: automation
ms.date: 08/10/2020
ms.topic: article
ms.openlocfilehash: 8767687f0b72d3469bef570770ac81fa8300097f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88055916"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>使用指标警报监视 Runbook

本文介绍如何根据 Runbook 完成状态创建[指标警报](../azure-monitor/platform/alerts-metric-overview.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)

## <a name="create-alert"></a>创建警报

使用警报可以定义一个要监视的条件，以及在满足该条件时要执行的操作。

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“自动化帐户”，以启动 Azure 自动化服务 。

2. 在自动化帐户列表中，选择要为其创建警报的帐户。 

3. 在“监视”下，选择“警报”，然后选择“+ 新建警报规则”  。 目标的范围已定义，并与你的自动化帐户关联。

### <a name="configure-alert-criteria"></a>配置警报条件

1. 单击“选择条件”。 为“信号类型”选择“指标”，然后从列表中选择“作业总数”  。

2. 可在“配置信号逻辑”页中定义触发警报的逻辑。 历史图表下面显示了两个维度：“Runbook 名称”和“状态”。 维度是某个指标的、可用于筛选结果的不同属性。 对于“Runbook 名称”，请选择要对其发出警报的 Runbook，或者留空，以针对所有 Runbook 发出警报。 对于“状态”，请从下拉列表中选择要监视的状态。 下拉列表中显示的 Runbook 名称和状态值仅适用于在过去一周已运行的作业。

   如果想要针对下拉列表中未显示的状态或 Runbook 发出警报，请单击维度旁边的“添加自定义值”选项。 此操作会打开一个对话框，用于指定最近未针对该维度发出过的自定义值。 如果输入的值未在某个属性中出现过，则不会触发警报。

   > [!NOTE]
   > 如果没有为 Runbook Name 维度指定名称，如果有任何符合状态条件的 runbook（包括隐藏的系统 runbook），你将收到警报。

    例如，若要在 Runbook 返回“已失败”状态时发出警报，除了指定 Runbook 名称外，对于“状态”维度，请添加自定义维度值“已失败” 。

    :::image type="content" source="./media/automation-alert-metric/specify-dimension-custom-value.png" alt-text="指定自定义维度值" border="false":::

3. 在“警报逻辑”下，定义警报的条件和阈值。 下面会显示定义的条件预览。

4. 在“评估依据”下，选择查询的时间范围，以及查询的运行频率。 例如，如果您在过去**5 分钟**内选择了**时间段**，并且**频率**为**1 分钟**，则警报将查找满足过去5分钟的条件的 runbook 的数目。 此查询每分钟运行一次，一旦在 5 分钟时间范围内找不到定义的警报条件，则警报会自行解决。 完成后，单击“完成”。

   ![选择警报的资源](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-the-action-to-take"></a>定义要执行的操作

1. 在“操作组”下，单击“指定操作组” 。 操作组是可以对多个警报使用的一组操作。 这可能包括但不限于电子邮件通知、Runbook、Webhook 以及其他操作。 若要详细了解操作组和创建可发送电子邮件通知的操作组的步骤，请参阅[创建和管理操作组](../azure-monitor/platform/action-groups.md)。

### <a name="define-alert-details"></a>定义警报详细信息

1. 在“警报规则详细信息”下，为警报提供一个友好名称和说明。 设置与警报条件匹配的“严重性”。 有五种严重性，范围为 0 到 5。 不管严重性如何，都会以相同的方式处理警报；可以根据业务逻辑来匹配严重性。

1. 默认情况下，在创建时会启用规则，除非在“在创建后启用警报规则”选项中选择了“否” 。 对于在禁用状态下创建的警报，你可以在准备就绪后启用它们。 选择“创建警报规则”保存更改。

## <a name="receive-notification"></a>接收通知

满足警报条件时，该操作组会运行定义的操作。 本文中的示例发送了一封电子邮件。 下图是触发警报后收到的电子邮件示例：

![电子邮件警报](./media/automation-alert-activity-log/alert-email.png)

一旦指标不再超出定义的阈值，警报将会停用，并且操作组会运行定义的操作。 如果选择电子邮件操作类型，则会发送一封解决状态电子邮件，指出警报已得到解决。

## <a name="next-steps"></a>后续步骤

* 有关详细信息，请参阅[使用警报触发 Azure 自动化 runbook](automation-create-alert-triggered-runbook.md)。
