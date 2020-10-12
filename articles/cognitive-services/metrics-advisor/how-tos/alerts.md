---
title: 配置指标顾问警报
titleSuffix: Azure Cognitive Services
description: 如何使用适用于电子邮件、web 和 Azure DevOps 的挂钩配置指标顾问警报。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 870b412a1fa5d362ac7b5be1df6c92d95c2936ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90977789"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>操作说明：配置警报并使用挂钩获取通知

度量值顾问检测到异常情况后，将根据警报设置（使用挂钩）触发警报通知。 警报设置可用于多个检测配置，可使用各种参数自定义警报规则。

## <a name="create-a-hook"></a>创建挂钩

指标顾问支持三种不同类型的挂钩：电子邮件挂钩、web 挂钩和 Azure DevOps。 您可以选择适用于您的特定方案的应用程序。       

### <a name="email-hook"></a>电子邮件挂钩

> [!Note]
> 指标顾问资源管理员需要配置电子邮件设置，并在指标顾问中输入 SMTP 相关信息，然后才能发送异常警报。 资源组管理员或订阅管理员需要在指标顾问资源的 "访问控制" 选项卡中分配至少一个 *认知服务指标顾问管理员* 角色。 

若要创建电子邮件挂钩，可使用以下参数： 

电子邮件挂钩是将异常警报发送到 "发送 **到** " 部分中指定的电子邮件地址的通道。 将发送两种类型的警报电子邮件： *数据馈送不可用* 警报，以及包含一个或多个异常的 *事件报告* 。 

|参数 |说明  |
|---------|---------|
| 名称 | 电子邮件挂钩的名称 |
| 发送电子邮件至| 要向其发送警报的电子邮件地址|
| 外部链接 | 可选字段，用于启用自定义重定向，例如用于疑难解答说明。 |
| 自定义异常警报标题 | 标题模板支持 `${severity}` 、 `${alertSettingName}` 、 `${datafeedName}` 、 `${metricName}` 、 `${detectConfigName}` 、 `${timestamp}` 、 `${topDimension}` 、 `${incidentCount}` 、 `${anomalyCount}`

单击 **"确定"** 后，将创建电子邮件挂钩。 可以在任何警报设置中使用它来接收异常警报。 

### <a name="web-hook"></a>Web 挂钩

> [!Note]
> * 使用 **POST** 请求方法。
> * 请求正文类似于：  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * 创建或修改 web 挂钩时，API 将作为带有空请求正文的测试调用。 API 需要返回 200 HTTP 代码。

Web 挂钩是指标 Advisor 服务中可用的所有信息的入口点，并在触发警报时调用用户提供的 api。 可以通过 web 挂钩发送所有警报。

若要创建 web 挂钩，你将需要添加以下信息：

|参数 |说明  |
|---------|---------|
|端点     | 触发警报时要调用的 API 地址。        |
|用户名/密码 | 用于对 API 地址进行身份验证。 如果不需要身份验证，则将其保留为黑色。         |
|Header     | API 调用中的自定义标头。        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="web 挂钩创建窗口。":::

当通过 web 挂钩推送通知时，可以使用以下 Api 来获取警报的详细信息。 在将推送到的 API 服务中设置 *时间戳* 和 *alertSettingGuid* ，然后使用以下查询： 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

指标顾问还支持在 Azure DevOps 中自动创建工作项，以跟踪在检测到任何异常时的问题/bug。 可以通过 Azure DevOps 挂钩发送所有警报。

若要创建 Azure DevOps 挂钩，你将需要添加以下信息

|参数 |说明  |
|---------|---------|
| 名称 | 挂钩的名称 |
| 组织 | 你的 DevOps 所属的组织 |
| 项目 | DevOps 中的特定项目。 |
| 访问令牌 |  用于对 DevOps 进行身份验证的令牌。 | 

> [!Note]
> 如果希望指标顾问基于异常警报创建工作项，则需要授予写入权限。 创建挂钩后，可以在任何警报设置中使用它们。 在 " **挂钩设置** " 页中管理挂钩。

## <a name="add-or-edit-alert-settings"></a>添加或编辑警报设置

请参阅度量值详细信息页，找到 "度量值详细信息" 页左下角的 " **警报设置** " 部分。 它列出了适用于选定检测配置的所有警报设置。 当创建新的检测配置时，不会设置警报，也不会发送任何警报。  
您可以使用 " **添加**"、" **编辑** " 和 " **删除** " 图标来修改警报设置。

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="web 挂钩创建窗口。" 按钮以获取用于添加或编辑警报设置的窗口。

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="web 挂钩创建窗口。":::

**警报设置名称**：此警报设置的名称。 它将显示在警报电子邮件标题中。

**挂接**：要向其发送警报的挂钩列表。

上面的屏幕截图中标记的部分是一个检测配置的设置。 可以为不同的检测配置设置不同的警报设置。 使用此窗口中的第三个下拉列表选择目标配置。 

### <a name="filter-settings"></a>筛选器设置 

下面是一个检测配置的筛选器设置。

**用于** 筛选异常的的警报具有4个选项：

* **所有系列中的异常**：警报中将包括所有异常。         
* **序列组中的异常**：按维度值筛选序列。 为某些维度设置特定值。 如果序列与指定的值匹配，则异常将仅包括在警报中。       
* **收藏系列中的异常**：警报中只包含标记为收藏的序列。        |
* **所有序列的前 N 个异常**：此筛选器适用于你只关心值位于前 n 个的系列的情况。我们将回顾一些时间戳，并检查这些时间戳处的序列值是否在前 N 个。如果 "in top n" 计数大于指定的数量，则会将异常包含在警报中。        |

**筛选异常选项** 是包含以下选项的附加筛选器：

- **严重性** ：仅当异常严重性在指定范围内时才会包含异常。
- **暂停** ：对于下一个点中的异常，请暂时停止警报 (period) ，触发警报时。
    - **暂停类型** ：设置为 **系列**时，触发的异常只会推迟其序列。 对于 **指标**，一个触发的异常将使此指标中的所有序列都推迟。
    - **暂停编号** ：) 暂停的点数 (期。
    - **重置为不连续** ：选中此项时，触发的异常将仅暂停接下来的 n 个连续异常。 如果以下任一数据点不是异常情况，则将从该点重置暂停时间;如果未选择此项，则触发的异常会将下一个 n 点 (period) ，即使连续的数据点不是异常。
- **值** (可选) ：按值筛选。 只有满足条件的点值才能包含异常。 如果使用另一个度量值的相应值，则这两个度量值的维度名称应一致。

未筛选出的异常将在警报中发送。

### <a name="add-cross-metric-settings"></a>添加交叉指标设置

单击 "警报设置" 页中的 " **+ 添加交叉指标设置** " 以添加其他部分。

**运算符**选择器是每个部分的逻辑关系，以确定它们是否发送警报。


|操作员  |说明  |
|---------|---------|
|AND     | 仅当系列与每个警报部分匹配时才发送警报，所有数据点都是异常。 如果度量值具有不同的维度名称，则永远不会触发警报。         |
|OR     | 如果至少有一个部分包含异常，则发送警报。         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="web 挂钩创建窗口。":::

## <a name="next-steps"></a>后续步骤

- [使用反馈调整异常检测](anomaly-feedback.md)
- [诊断事件](diagnose-incident.md)。
- [配置指标并微调检测配置](configure-metrics.md)