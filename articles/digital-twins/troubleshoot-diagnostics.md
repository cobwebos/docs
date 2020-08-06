---
title: 设置诊断
titleSuffix: Azure Digital Twins
description: 请参阅如何使用诊断设置启用日志记录。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 402eda92d5cc8e5c2566345d31c9ae625b82695c
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809386"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure 数字孪生故障排除：诊断日志记录

Azure 数字孪生收集提供有关资源状态的信息的服务实例的[指标](troubleshoot-metrics.md)。 你可以使用这些度量值来评估 Azure 数字孪生服务及其连接到的资源的整体运行状况。 这些面向用户的统计信息可帮助你查看 Azure 数字孪生所发生的情况，并帮助对问题执行根本原因分析，而无需联系 Azure 支持部门。

本文介绍如何从 Azure 数字孪生实例为指标数据启用**诊断日志记录**。 您可以使用这些日志来帮助您解决服务问题。

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>启用 Azure 门户的诊断设置

1. 登录到[Azure 门户](https://portal.azure.com)并导航到 Azure 数字孪生实例。 可以通过在门户搜索栏中键入其名称来找到它。 

2. 从菜单中选择 "**诊断设置**"，然后单击 "**添加诊断设置**"。

    :::image type="content" source="media/troubleshoot-metrics/diagnostic-settings.png" alt-text="显示 "诊断设置" 页和要添加的按钮的屏幕截图":::

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

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="显示 "诊断设置" 页和要添加的按钮的屏幕截图":::

新设置在大约 10 分钟后生效。 之后，日志会在实例的 "**诊断设置**" 页上显示在配置的目标中。 

## <a name="next-steps"></a>后续步骤

* 有关配置诊断的详细信息，请参阅[*从 Azure 资源收集和使用日志数据*](../azure-monitor/platform/platform-logs-overview.md)。
* 有关 Azure 数字孪生指标的信息，请参阅[*故障排除：查看包含 Azure Monitor 的指标*](troubleshoot-metrics.md)。
