---
title: 快速入门：指标顾问 Web 门户
titleSuffix: Azure Cognitive Services
description: 了解如何开始使用指标顾问 Web 门户。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 0453ec9eac2b73b5372c143b23d6db98f65e38aa
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944085"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>快速入门：使用 Web 门户监视你的首个指标

预配指标顾问实例时，可以使用 API 和基于 Web 的工作区来处理服务。 可以使用基于 Web 的工作区充当快速开始使用该服务的简单方法。 它还提供了一种用于配置设置、自定义模型和执行根本原因分析的可视方式。 

* 载入指标数据
* 查看指标和可视化效果
* 微调检测配置
* 浏览诊断见解
* 创建并订阅异常警报

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 获得 Azure 订阅后，<a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="“创建指标顾问”资源"  target="_blank">在 Azure 门户中创建“指标顾问”资源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>，以部署“指标顾问”实例。  

    
> [!TIP]
> * 部署指标顾问资源可能需要 10 到 30 分钟。 部署成功后，单击“转到资源”。
> * 如果想要使用 REST API 与服务进行交互，需要从创建的资源获取密钥和终结点。 可以在创建的资源的“密钥和终结点”选项卡中找到它们。

本文档使用 SQL 数据库作为创建首个监视器的示例。

## <a name="onboard-time-series-data"></a>载入时序数据

指标顾问为不同的数据源（例如 SQL 数据库、Azure 数据资源管理器和 Azure 表存储）提供了连接器。 对于不同的连接器，连接数据的步骤是相似的，尽管某些配置参数可能会有所不同。 有关特定数据源的必需参数，请参阅[连接来自不同源的数据](../data-feeds-from-different-sources.md)。

本快速入门使用 SQL 数据库作为示例。 你还可以按相同的步骤引入自己的数据。

首先使用 Active Directory 帐户登录到指标顾问工作区。 在登录页上，选择刚创建的“目录”、“订阅”和“工作区”，然后单击“开始使用”   。 加载工作负载的主页后，从左侧菜单中选择“添加数据馈送”。

### <a name="data-schema-requirements-and-configuration"></a>数据架构要求和配置

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings"></a>配置连接设置

> [!TIP]
> 有关可用参数的详细信息，请参阅[如何添加数据馈送](../how-tos/onboard-your-data.md)。

通过连接到时序数据源来添加数据馈送。 首先选择以下参数：

* **源类型**：用于存储时序数据的数据源的类型。
* **粒度**：时序数据中连续数据点之间的间隔，例如每年、每月、每天。 自定义支持的最小间隔为 60 秒。
* **引入数据的时间 (UTC)** ：要引入的第一个时间戳的开始时间。 


接下来，使用数据源的凭据指定字符串，并使用自定义查询 。 此查询用于指定要引入并转换为所需架构的数据。

[!INCLUDE [query requirements](../includes/query-requirements.md)]

:::image type="content" source="../media/connection-settings.png" alt-text="连接设置" lightbox="../media/connection-settings.png":::


### <a name="verify-the-connection-and-load-the-data-schema"></a>验证连接并加载数据架构

创建连接字符串和查询字符串后，选择“验证并获取架构”验证连接并运行查询以从数据源获取数据架构。 通常，这会花费几秒钟的时间，具体取决于你的数据源连接。 如果此步骤中出现错误，请确认：

1. 你的连接字符串和查询是正确的。
2. 如果有防火墙设置，指标顾问实例能够连接到数据源。

### <a name="schema-configuration"></a>架构配置

加载数据架构并按如下显示后，选择适当的字段。


|选择  |说明  |说明  |
|---------|---------|---------|
|**Timestamp**     | 数据点的时间戳。 如果省略，则指标顾问将在数据点引入时使用时间戳。 对于每个数据馈送，最多可将一列指定为时间戳。        | 可选。 最多只能指定一列。       |
|度量      |  数据馈送中的数值。 对于每个数据馈送，可以指定多个度量值，但至少应选择一列作为度量值。        | 应至少指定一列。        |
|**维度**     | 分类值。 不同值的组合标识特定的一维时序，例如：国家/地区、语言、租户。 可以不选择列或选择任意数量的列作为维度。 注意：如果要选择非字符串列作为维度，请小心以避免维度爆炸。 | 可选。        |
|**忽略**     | 忽略所选列。        |         |


:::image type="content" source="../media/schema-configuration.png" alt-text="架构配置" lightbox="../media/schema-configuration.png":::

### <a name="automatic-roll-up-settings"></a>自动汇总设置

> [!IMPORTANT]
> 如果想要启用根本原因分析和其他诊断功能，则需要配置“自动汇总设置”。 启用后，将无法更改自动汇总设置。

指标顾问可以在引入过程中自动对每个维度执行聚合（SUM/MAX/MIN…），然后生成一个用于根本原因分析和其他诊断功能的层次结构。 有关更多详细信息，请参阅[自动汇总设置](../how-tos/onboard-your-data.md#automatic-roll-up-settings)。

为数据馈送提供自定义名称，该名称将显示在工作区中。 单击“提交”。 

## <a name="tune-detection-configuration"></a>优化检测配置

添加数据馈送后，指标顾问将尝试从指定的开始日期引入指标数据。 完全引入数据需要一段时间，可以通过单击数据馈送页顶部的“引入进度”来查看引入状态。 如果数据已引入，指标顾问将应用检测，并继续监视源以获取新数据。

应用检测时，单击数据馈送中列出的其中一项指标，找到“指标详细信息”页并执行以下操作： 
- 查看此指标下所有时序切片的可视化效果
- 更新检测配置以满足预期的结果
- 为检测到的异常设置通知

:::image type="content" source="../media/metric-details.png" alt-text="指标详细信息" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>查看诊断见解

优化检测配置后，发现的异常应反映数据中的实际异常。 指标顾问针对多维指标执行分析，如异常群集、事件关联和根本原因分析。 使用这些功能分析和诊断数据中的事件。

要查看诊断见解，请单击时序可视化效果上的红点，这些红点代表检测到的异常。 将显示一个窗口，其中包含“事件分析”页的链接。 

:::image type="content" source="../media/incident-link.png" alt-text="事件链接" lightbox="../media/incident-link.png":::

单击此链接后，将切换到事件分析页，该页面将分析相应的异常，并提供一系列诊断见解。 顶部将显示有关事件的统计信息，如“严重性”、“涉及的异常”以及“开始时间”和“结束时间”   。 

接下来，你将看到事件的上级异常，以及自动生成的根本原因建议。 此自动根本原因建议是通过分析所有相关异常的事件树生成的，包括：偏差、分布和对父异常的贡献。 

:::image type="content" source="../media/incident-diagnostic.png" alt-text="事件诊断" lightbox="../media/incident-diagnostic.png":::

基于这些信息，你已经可以对正在发生的事情和事件的影响以及最潜在的根本原因有一个直观的看法。 这样便能够立即采取行动尽快解决事件。 

但你还可以获取和利用更多诊断见解，方法是利用其他诊断功能按维度向下钻取异常、查看相似异常并跨指标进行比较。 有关详细信息，请参阅[如何：诊断事件](../how-tos/diagnose-incident.md)。 

## <a name="get-notified-when-new-anomalies-are-found"></a>发现新的异常时获取通知

如果想要在数据中检测到异常时收到警报，可以为一个或多个指标创建订阅。 指标顾问使用挂钩发送警报。 支持三种类型的挂钩：电子邮件挂钩、Webhook 和 Azure DevOps。 我们以 Webhook 为例。 

### <a name="create-a-web-hook"></a>创建 Webhook

Webhook 是通过编程方式从指标顾问服务获取异常通知的入口点，该服务会在触发警报时调用用户提供的 API。有关如何创建挂钩的详细信息，请参阅[操作说明：使用挂钩配置警报并获取通知](../how-tos/alerts.md#create-a-hook)的“创建挂钩”部分。 

### <a name="configure-alert-settings"></a>配置警报设置

创建挂钩后，警报设置将确定应发送的警报通知和发送方式。 可以为每个指标设置多个警报设置。 有两个重要的设置：警报对象，用于指定要包含的异常；筛选异常选项，用于定义要包含在警报中的异常 。 有关更多详细信息，请参阅[：使用挂钩配置警报并获取通知](../how-tos/alerts.md#add-or-edit-alert-settings)中的“添加或编辑警报设置”部分。


## <a name="next-steps"></a>后续步骤

- [加入数据馈送](../how-tos/onboard-your-data.md)
    - [管理数据馈送](../how-tos/manage-data-feeds.md)
    - [不同数据源的配置](../data-feeds-from-different-sources.md)
- [使用 REST API 或客户端库](rest-api.md)
- [配置指标并微调检测配置](../how-tos/configure-metrics.md)