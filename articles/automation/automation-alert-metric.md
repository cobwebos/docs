---
title: 使用指标警报监视 Azure 自动化 Runbook
description: 本文逐步讲解如何基于指标监视 Azure 自动化 Runbook
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: a8a4b24e6b2503f64cc3fd7f4fd8c7400c547d4d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658916"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>使用指标警报监视 Runbook

本文介绍如何基于 Runbook 的完成状态创建警报。

## <a name="log-in-to-azure"></a>登录 Azure

在 https://portal.azure.com 中登录 Azure

## <a name="create-alert"></a>创建警报

使用警报可以定义一个要监视的条件，以及在满足该条件时要执行的操作。

在 Azure 门户中，导航到“所有服务”并选择“监视”。 在“监视”页上，选择“警报”并单击“+ 新建警报规则”。

### <a name="define-the-alert-condition"></a>定义警报条件

1. 在“1. 定义警报条件”下，单击“+ 选择目标”。 选择自己的订阅，在“按资源类型筛选”下面选择“自动化帐户”。 选择自己的自动化帐户，并单击“完成”。

   ![选择警报的资源](./media/automation-alert-activity-log/select-resource.png)

### <a name="configure-alert-criteria"></a>配置警报条件

1. 单击“+ 添加条件”。 为“信号类型”选择“指标”，然后从表中选择“作业总数”。

1. 可在“配置信号逻辑”页中定义触发警报的逻辑。 历史图表下面显示了两个维度：“Runbook 名称”和“状态”。 维度是某个指标的、可用于筛选结果的不同属性。 对于“Runbook 名称”，请选择要对其发出警报的 Runbook，或者留空，以针对所有 Runbook 发出警报。 对于“状态”，请从下拉列表中选择要监视的状态。 下拉列表中显示的 Runbook 名称和状态值仅适用于在过去一周已运行的作业。

   如果想要针对下拉列表中未显示的状态或 Runbook 发出警报，请单击维度旁边的 **\+**。 此时会打开一个对话框，用于输入最近未针对该维度发出过的自定义值。 如果输入的值未在某个属性中出现过，则不会触发警报。

1. 在“警报逻辑”下，定义警报的条件和阈值。 下面会显示定义的条件预览。

1. 在“评估依据”下，选择查询的时间范围，以及查询的运行频率。 例如，如果为“期间”选择“过去 5 分钟”，为“频率”选择“每隔 1 分钟”，则警报会查找过去 5 分钟内满足条件的 Runbook 数。 此查询每分钟运行一次，一旦在 5 分钟时间范围内找不到定义的警报条件，则警报会自行解决。 完成后，单击“完成”。

   ![选择警报的资源](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>定义警报详细信息

1. 在“2.为警报提供一个友好名称和说明。 设置与警报条件匹配的“严重性”。 有五种严重性，范围为 0 到 5。 不管严重性如何，都会以相同的方式处理警报；可以根据业务逻辑来匹配严重性。

1. 该部分的底部有一个按钮，用于在完成设置后启用规则。 默认情况下，创建规则后会将其启用。 如果选择“否”，则可以在“已禁用”状态下创建警报。 在 Azure Monitor 的“规则”页中，可以选择该规则，并在准备就绪的情况下单击“启用”来启用警报。

### <a name="define-the-action-to-take"></a>定义要执行的操作

1. 在“3. 定义操作组”下，单击“+ 新建操作组”。 操作组是可以在多个警报中使用的一组操作。 这可能包括但不限于电子邮件通知、Runbook、Webhook 以及其他操作。 若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../monitoring-and-diagnostics/monitoring-action-groups.md)。

1. 在“操作组名称”框中，为其提供一个友好名称和短名称。 使用此组发送通知时，短名称被用来代替完整的操作组名称。

1. 在“操作”部分的“操作类型”下，选择“电子邮件/短信/推送/语音”。

1. 在“电子邮件/短信/推送/语音”页面上，为其提供一个名称。 选中“电子邮件”复选框，然后输入要使用的有效电子邮件地址。

   ![配置电子邮件操作组](./media/automation-alert-activity-log/add-action-group.png)

1. 单击“电子邮件/短信/推送/语音”页面上的“确定”以将其关闭，然后单击“确定”以关闭“添加操作组”页面。 在此页中指定的名称将保存为“操作名称”。

1. 完成后，单击“保存”。 随即会创建规则。当 Runbook 已完成并出现特定的状态时，该规则会发出警报。

> [!NOTE]
> 将电子邮件地址添加到操作组时，系统会发送一封通知电子邮件，指出已将该地址添加到操作组。

## <a name="notification"></a>通知

满足警报条件时，该操作组会运行定义的操作。 本文中的示例发送了一封电子邮件。 下图是触发警报后收到的电子邮件示例：

![电子邮件警报](./media/automation-alert-activity-log/alert-email.png)

一旦指标不再超出定义的阈值，警报将会停用，并且操作组会运行定义的操作。 如果选择电子邮件操作类型，则会发送一封解决状态电子邮件，指出警报已得到解决。

## <a name="next-steps"></a>后续步骤

请继续学习以下文章，了解将警报集成到自动化帐户的其他方法。

> [!div class="nextstepaction"]
> [使用警报触发 Azure 自动化 Runbook](automation-create-alert-triggered-runbook.md)