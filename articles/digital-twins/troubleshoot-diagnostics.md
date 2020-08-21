---
title: 设置诊断
titleSuffix: Azure Digital Twins
description: 请参阅如何使用诊断设置启用日志记录。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 5091edbf9138cb8ff03df193dcbeed692aaf13e3
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612395"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure 数字孪生故障排除：诊断日志记录

Azure 数字孪生收集提供有关资源状态的信息的服务实例的 [指标](troubleshoot-metrics.md) 。 你可以使用这些度量值来评估 Azure 数字孪生服务及其连接到的资源的整体运行状况。 这些面向用户的统计信息可帮助你查看 Azure 数字孪生所发生的情况，并帮助对问题执行根本原因分析，而无需联系 Azure 支持部门。

本文介绍如何从 Azure 数字孪生实例为指标数据启用 **诊断日志记录** 。 您可以使用这些日志来帮助您解决服务问题，并将诊断设置配置为将 Azure 数字孪生指标发送到不同的目标。 有关这些设置的详细信息，请参阅 [*创建诊断设置以将平台日志和指标发送到不同的目标*](../azure-monitor/platform/diagnostic-settings.md)。

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>启用 Azure 门户的诊断设置

下面介绍如何为 Azure 数字孪生实例启用诊断设置：

1. 登录到 [Azure 门户](https://portal.azure.com) 并导航到 Azure 数字孪生实例。 可以通过在门户搜索栏中键入其名称来找到它。 

2. 从菜单中选择 " **诊断设置** "，然后单击 " **添加诊断设置**"。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="显示 诊断设置 页和要添加的按钮的屏幕截图":::

3. 在下面的页面上，填写以下值：
     * **诊断设置名称**：为诊断设置指定一个名称。
     * **类别详细信息**：选择要监视的操作，并选中相应的复选框以对这些操作启用诊断。 诊断设置可以报告的操作如下：
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        有关这些选项的更多详细信息，请参阅下面的 [*类别详细信息*](#category-details) 部分。
     * **目标详细信息**：选择要将日志发送到的位置。 可选择以下三个选项的任意组合：
        - 发送到 Log Analytics
        - 存档到存储帐户
        - 流式传输到事件中心

        如果目标选择需要，系统可能会要求你填写其他详细信息。  
    
4. 保存新设置。 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="显示 诊断设置 页和要添加的按钮的屏幕截图":::

新设置在大约 10 分钟后生效。 之后，日志会在实例的 " **诊断设置** " 页上显示在配置的目标中。 

## <a name="category-details"></a>类别详细信息

下面更详细地介绍了在设置诊断设置时，可在 " **类别详细信息** " 下选择的日志类别。

| 日志类别 | 说明 |
| --- | --- |
| ADTModelsOperation | 记录与模型有关的所有 API 调用 |
| ADTQueryOperation | 记录与查询相关的所有 API 调用 |
| ADTEventRoutesOperation | 记录与事件路由相关的所有 API 调用，以及从 Azure 数字孪生传出事件到终结点服务（如事件网格、事件中心和服务总线）的事件 |
| ADTDigitalTwinsOperation | 记录与 Azure 数字孪生有关的所有 API 调用 |

每个日志类别包含写入、读取、删除和操作操作。  它们映射到 REST API 调用，如下所示：

| 事件类型 | REST API 操作 |
| --- | --- |
| 写入 | PUT 和 PATCH |
| 读取 | GET |
| 删除 | DELETE |
| 操作 | POST |

下面是在每个类别中记录的操作和相应的 [Azure 数字孪生 REST API 调用](https://docs.microsoft.com/rest/api/azure-digitaltwins/) 的完整列表。 

>[!NOTE]
> 每个日志类别包含若干个操作/REST API 调用。 在下表中，每个日志类别映射到其下的所有操作/REST API 调用，直到列出下一个日志类别。 

| 日志类别 | Operation | REST API 调用和其他事件 |
| --- | --- | --- |
| ADTModelsOperation | DigitalTwins/模型/写入 | 数字克隆模型更新 API |
|  | DigitalTwins/模型/读取 | 数字克隆模型按 Id 和列表 Api 获取 |
|  | DigitalTwins/模型/删除 | 数字克隆模型删除 API |
|  | DigitalTwins/模型/操作 | 数字克隆模型添加 API |
| ADTQueryOperation | DigitalTwins/查询/操作 | 查询孪生 API |
| ADTEventRoutesOperation | DigitalTwins/eventroutes/write | 事件路由添加 API |
|  | DigitalTwins/eventroutes/read | 事件路由由 Id 和列表 Api 获取 |
|  | DigitalTwins/eventroutes/delete | 事件路由删除 API |
|  | DigitalTwins/eventroutes/action | 尝试将事件发布到终结点服务时失败， (不是 API 调用)  |
| ADTDigitalTwinsOperation | DigitalTwins/DigitalTwins/write | 数字孪生添加、添加关系、更新、更新组件 |
|  | DigitalTwins/DigitalTwins/read | 数字孪生按 Id、获取组件、按 Id 获取关系、列出传入关系、列表关系 |
|  | DigitalTwins/DigitalTwins/delete | 数字孪生删除，删除关系 |
|  | DigitalTwins/DigitalTwins/action | 数字孪生发送组件遥测数据，发送遥测数据 |

## <a name="next-steps"></a>后续步骤

* 有关配置诊断的详细信息，请参阅 [*从 Azure 资源收集和使用日志数据*](../azure-monitor/platform/platform-logs-overview.md)。
* 有关 Azure 数字孪生指标的信息，请参阅 [*故障排除：查看包含 Azure Monitor 的指标*](troubleshoot-metrics.md)。
* 若要了解如何启用指标警报，请参阅 [*故障排除：设置警报*](troubleshoot-alerts.md)。
