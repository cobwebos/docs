---
title: 通过 Azure Monitor 查看指标
titleSuffix: Azure Digital Twins
description: 请参阅如何 Azure Monitor 查看 Azure 数字孪生指标。
author: baanders
ms.author: baanders
ms.date: 7/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 09e32d5baa367b76c34ebca28adfdd20385e4e18
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374523"
---
# <a name="view-and-understand-azure-digital-twins-metrics"></a>查看和了解 Azure 数字孪生指标

这些指标为你显示 Azure 订阅中 Azure 数字孪生资源的状态信息。 Azure 数字孪生指标可帮助你评估 Azure 数字孪生服务的总体运行状况以及连接到它的资源。 这些面向用户的统计信息可帮助你查看 Azure 数字孪生所发生的情况，并帮助对问题执行根本原因分析，而无需联系 Azure 支持部门。

默认启用度量值。 可以从[Azure 门户](https://portal.azure.com)查看 Azure 数字孪生指标。

## <a name="how-to-view-azure-digital-twins-metrics"></a>如何查看 Azure 数字孪生指标

1. 创建 Azure 数字孪生实例。 可以在[*如何：设置实例和身份验证*](how-to-set-up-instance-scripted.md)中找到有关如何设置 Azure 数字孪生实例的说明。

2. 在[Azure 门户](https:/portal.azure.com)中找到你的 Azure 数字孪生实例（通过在门户搜索栏中键入其名称，你可以打开该实例的页）。 

    从实例的菜单中，选择 "**指标**"。
   
    :::image type="content" source="media/how-to-view-metrics/azure-digital-twins-metrics.png" alt-text="显示 Azure 数字孪生的 "指标" 页的屏幕截图":::

    此页面显示 Azure 数字孪生实例的指标。 你还可以通过从列表中选择你想要查看的指标来创建其自定义视图。
    
3. 通过单击菜单中的 "**诊断设置**"，然后单击 "**添加诊断设置**"，可以选择将指标数据发送到事件中心终结点或 Azure 存储帐户。

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="显示 "诊断设置" 页和要添加的按钮的屏幕截图":::

    有关此过程的详细信息，请参阅[*操作方法：设置诊断*](how-to-set-up-diagnostics.md)。

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure 数字孪生指标以及如何使用它们

Azure 数字孪生提供了多个指标，可让你概括了解实例及其关联资源的运行状况。 你还可以将来自多个度量值的信息组合在一起，以便更好地了解实例的状态。 

下表描述了每个 Azure 数字孪生实例跟踪的指标，以及每个指标如何与实例的总体状态相关。

#### <a name="api-request-metrics"></a>API 请求度量值

与 API 请求有关的指标：

| 指标 | 指标显示名称 | 计价单位 | 聚合类型| 说明 | 维度 |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API 请求（预览） | 计数 | 总计 | 为数字孪生读取、写入、删除和查询操作发出的 API 请求数。 |  身份验证 </br>Operation </br>协议 </br>状态代码 </br>状态代码类 </br>状态文本 |
| ApiRequestsLatency | API 请求延迟（预览版） | 毫秒 | 平均值 | API 请求的响应时间。 这是指从 Azure 数字孪生收到请求到服务发送数字孪生读取、写入、删除和查询操作的成功/失败结果起的时间。 | 身份验证 </br>Operation </br>协议 |
| ApiRequestsFailureRate | API 请求失败率（预览） | 百分比 | 平均值 | 服务为你的实例接收的 API 请求的百分比，该百分比为数字孪生读、写、删除和查询操作提供内部错误（500）响应代码。 | 身份验证 </br>Operation </br>协议 </br>状态代码 </br>状态代码类 </br>状态文本

#### <a name="routing-metrics"></a>路由度量值

与路由有关的指标：

| 指标 | 指标显示名称 | 计价单位 | 聚合类型| 说明 | 维度 |
| --- | --- | --- | --- | --- | --- |
| 路由 | 路由（预览版） | 计数 | 总计 | 路由到终结点 Azure 服务（如事件中心、服务总线或事件网格）的消息数。 | Operation </br>结果 |
| RoutingLatency | 路由延迟（预览版） | 毫秒 | 平均值 | 在事件从 Azure 数字孪生路由到终结点（例如事件中心、服务总线或事件网格）发送到该服务之间所经过的时间。 | Operation </br>结果 |
| RoutingFailureRate | 路由失败率（预览） | 百分比 | 平均值 | 导致错误的事件百分比，因为这些事件从 Azure 数字孪生路由到终结点 Azure 服务（如事件中心、服务总线或事件网格）。 | Operation </br>结果 |

#### <a name="billing-metrics"></a>计费指标

与计费有关的指标：

>[!NOTE]
> 在预览期间，**计费为零成本**。 尽管这些指标仍显示在可选列表中，但在预览期间不适用，并且在服务超出预览状态之前将保持为零。

| 指标 | 指标显示名称 | 计价单位 | 聚合类型| 说明 | 维度 |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | 计费 API 操作（预览） | 计数 | 总计 | 针对 Azure 数字孪生服务发出的所有 API 请求计数的计费指标。 | `MeterId` |
| BillingQueryUnits | 帐单查询单位（预览版） | 计数 | 总计 | 查询单位数，即，用于执行查询的服务资源使用情况的内部计算度量值。 还有一个帮助器 API 可用于测量查询单位： [QueryChargeHelper 类](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview) | `MeterId` |
| BillingMessagesProcessed | 处理的计费消息（预览） | 计数 | 总计 | 从 Azure 数字孪生发送到外部终结点的消息数的计费指标。 | `MeterId` |

## <a name="dimensions"></a>维度

维度有助于标识有关指标的更多详细信息。 某些路由指标提供了每个终结点的信息。 下表列出了这些维度的可能值。

| 维度 | 值 |
| --- | --- |
| 身份验证 | OAuth |
| 操作（对于 API 请求） | DigitalTwins/DigitalTwins/delete</br>DigitalTwins/DigitalTwins/write</br>DigitalTwins/DigitalTwins/read </br>DigitalTwins/eventroutes/read </br>DigitalTwins/eventroutes/write </br>DigitalTwins/eventroutes/delete </br>DigitalTwins/模型/读取 </br>DigitalTwins/模型/写入 </br>DigitalTwins/模型/删除 </br>DigitalTwins/查询/操作 |
操作（用于路由） | 事件网格 </br>事件中心 </br>服务总线 |
| 协议 | HTTPS |
| 结果 | Success </br>失败 |
| 状态代码 | 200、404、500等。 |
| 状态代码类 | 2xx、4xx、5xx 等。 |
| 状态文本 | 内部服务器错误，找不到，等等。 |

## <a name="next-steps"></a>后续步骤

若要详细了解如何管理 Azure 数字孪生的记录指标，请参阅[*如何：设置诊断*](how-to-set-up-diagnostics.md)。

或者，现在你已了解 Azure 数字孪生指标的概述，请单击以下链接了解有关管理 Azure 数字孪生的详细信息：

* [*操作说明：使用 Azure 数字孪生 Api 和 Sdk*](how-to-use-apis-sdks.md)
* [*操作说明：管理自定义模型*](how-to-manage-model.md)
* [*操作说明：管理数字孪生*](how-to-manage-twin.md)
* [*如何：管理包含关系的双子图形*](how-to-manage-graph.md)
* [*操作说明：管理终结点和路由*](how-to-manage-routes.md)