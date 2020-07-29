---
title: 设置诊断
titleSuffix: Azure Digital Twins
description: 请参阅如何使用诊断设置启用日志记录。
author: baanders
ms.author: baanders
ms.date: 7/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 60ee2102a2b725c68e29afec9a44de03e52e9467
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374504"
---
# <a name="enable-logging-with-diagnostics-settings"></a>通过诊断设置启用日志记录

可以通过对实例启用诊断设置，选择将指标数据发送到[Log Analytics](../azure-monitor/log-query/get-started-portal.md)、[事件中心](../event-hubs/event-hubs-about.md)终结点或[Azure 存储](../storage/blobs/storage-blobs-overview.md)。

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>启用 Azure 门户的诊断设置

1. 登录到[Azure 门户](https://portal.azure.com)并导航到 Azure 数字孪生实例。 可以通过在门户搜索栏中键入其名称来找到它。 

2. 从菜单中选择 "**诊断设置**"，然后单击 "**添加诊断设置**"。

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="显示 "诊断设置" 页和要添加的按钮的屏幕截图":::

3. 在下面的页面上，填写以下值：
 * **诊断设置名称**：为诊断设置指定一个名称。
 * **类别详细信息**：选择要监视的操作，并选中相应的复选框以对这些操作启用诊断。 诊断设置可以报告的操作如下：
    - DigitalTwinsOperation
    - EventRoutesOperation
    - ModelsOperation
    - QueryOperation
    - AllMetrics
 * **目标详细信息**：选择要将日志发送到的位置。 可选择以下三个选项的任意组合：
    - 发送到 Log Analytics
    - 存档到存储帐户
    - 流式传输到事件中心

    如果目标选择需要，系统可能会要求你填写其他详细信息。  
    
4. 保存新设置。 

    :::image type="content" source="media/how-to-set-up-diagnostics/diagnostic-settings-details.png" alt-text="显示 "诊断设置" 页和要添加的按钮的屏幕截图":::

新设置在大约 10 分钟后生效。 之后，日志会在实例的 "**诊断设置**" 页上显示在配置的目标中。 

## <a name="next-steps"></a>后续步骤

* 有关配置诊断的详细信息，请参阅[*从 Azure 资源收集和使用日志数据*](../azure-monitor/platform/platform-logs-overview.md)。
* 有关 Azure 数字孪生指标的信息，请参阅 how [ *To： View 公制 with Azure Monitor*](how-to-view-metrics.md)