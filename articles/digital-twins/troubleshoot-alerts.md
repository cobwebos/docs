---
title: 设置警报
titleSuffix: Azure Digital Twins
description: 请参阅如何在 Azure 数字孪生度量值上启用警报。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: ae7e85624f5da06603ddc2675787b84203bc987b
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90087196"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>排查 Azure 数字孪生：警报

Azure 数字孪生收集提供有关资源状态的信息的服务实例的 [指标](troubleshoot-metrics.md) 。 你可以使用这些度量值来评估 Azure 数字孪生服务及其连接到的资源的整体运行状况。

当发现指标数据有重要条件时，**警报**会主动通知您。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 可以在 [*Microsoft Azure 中的警报概述*](../azure-monitor/platform/alerts-overview.md)中阅读有关警报的详细信息。

## <a name="turn-on-alerts"></a>启用警报

下面介绍如何为 Azure 数字孪生实例启用警报：

1. 登录到 [Azure 门户](https://portal.azure.com) 并导航到 Azure 数字孪生实例。 可以通过在门户搜索栏中键入其名称来找到它。 

2. 从菜单中选择 " **警报** "，然后选择 " **+ 新建警报规则**"。

3. 在下面的 " *创建警报规则* " 页上，你可以按照提示定义条件、要触发的操作和警报详细信息。     
    * **作用域** 详细信息应自动填写实例的详细信息
    * 你将定义 **条件** 和 **操作组** 详细信息，以自定义警报触发器和响应
    * 在 " **警报规则详细信息** " 部分中，输入规则的名称和可选描述。 如果希望警报在创建后立即激活，可以选中 " _创建时启用警报规则_ " 复选框。
        - 你还可以在其中选择 _资源组_ 和 _严重性_ 级别。

4. 选择 " _创建警报规则_ " 按钮，创建警报规则。

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="显示 "创建警报规则" 页的屏幕截图，其中包含作用域、条件、操作组和警报规则详细信息的部分" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

有关填写这些字段的指导演练，请参阅 [*Microsoft Azure 中的警报概述*](../azure-monitor/platform/alerts-overview.md)。 下面是 Azure 数字孪生步骤的一些示例。

### <a name="select-conditions"></a>选择条件

下面是 *Select 条件* 过程的摘录，用于说明可用于 Azure 数字孪生的警报信号的类型。 在此页上，您可以筛选信号类型，并从列表中选择所需的信号。

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="显示第一个 "配置信号逻辑" 页的屏幕截图。"信号类型" 框中突出显示了 "指标" 或 "活动日志"，以及可供选择的指标列表。":::

选择信号后，系统会要求你配置警报的逻辑。 可以筛选维度，设置警报的阈值，并设置条件检查的频率。 下面是有关在平均路由失败率指标超过5% 时设置警报的示例。

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="显示第二个 "配置信号逻辑" 页的屏幕截图。该页显示了度量值历史记录，其中包含一个用于筛选维度（例如事件网格操作）的区域，以及一个用于定义警报逻辑（如 "average 大于 5"）的部分。":::

### <a name="verify-success"></a>验证是否成功

设置警报后，它们将显示在你的实例的 " *警报* " 页上。
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="显示 "警报" 页和要添加的按钮的屏幕截图。已配置一个警报" lightbox="media/troubleshoot-alerts/alerts-post.png":::

## <a name="next-steps"></a>后续步骤

* 有关 Azure Monitor 警报的详细信息，请参阅 [*Microsoft Azure 中的警报概述*](../azure-monitor/platform/alerts-overview.md)。
* 有关 Azure 数字孪生指标的信息，请参阅 [*故障排除：查看包含 Azure Monitor 的指标*](troubleshoot-metrics.md)。
* 若要了解如何启用指标的诊断日志记录，请参阅 [*故障排除：设置诊断*](troubleshoot-diagnostics.md)。
