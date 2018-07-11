---
title: include 文件
description: include 文件
services: cognitive-services
author: scottwhi-msft
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2018
ms.author: scottwhi
ms.custom: include file
ms.openlocfilehash: 4e19c1afefdc5bcacebcb0d495193b48c7a6d724
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313704"
---
必应统计信息为必应搜索 API 提供分析。 分析包括调用量、最常用的查询字符串、地理分布，等等。 若要在必应搜索付费订阅中启用必应统计信息，请导航到 [Azure 仪表板](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7)，选择自己的付费订阅，然后单击“启用必应统计信息”。 启用必应统计信息会略微增加订阅费用（请参阅[定价](https://aka.ms/bingstatisticspricing)）。


> [!NOTE]
> 必应统计信息仅适用于付费订阅 - 不适用于免费试用订阅。 

> [!NOTE]
> 无法使用通过必应统计信息仪表板提供的任何数据来创建要分发到第三方的应用程序。

必应每隔 24 小时更新分析数据，并保留最多 13 个月的历史记录。

## <a name="accessing-your-analytics"></a>访问分析

若要访问分析仪表板，请转到 https://bingapistatistics.com。 确保使用购买付费订阅时所用的同一 Microsoft 帐户 (MSA) 登录。


## <a name="filtering-the-data"></a>筛选数据

默认情况下，图表和图形反映你有权访问的所有指标数据。 选择资源、市场、终结点和所需的报告时段，可以筛选图表和图形中显示的数据。 图表和图形会更改以反映应用的筛选器。 下面介绍了可以更改的筛选器。

- **资源 ID**：用于标识 Azure 订阅的唯一资源 ID。 如果你订阅了多个必应搜索 API 层，则该列表包含多个 ID。 默认已选中所有资源。  
  
- **市场**：结果的来源市场。 例如，en-us (美国英语) 默认已选中所有市场。 请注意，en-WW 市场是当调用未指定市场并且必应无法确定用户的市场时，必应使用的市场。  
  
- **终结点**：必应搜索 API 终结点。 该列表包含为其购买了付费订阅的所有终结点。 默认已选中所有终结点。  

- **时间范围**：报告时段。 可以指定：  
  
  - 全部 &mdash; 包含最多 13 个月的数据  
  - 过去 24 小时&mdash;包含过去 24 小时的分析  
  - 过去一周&mdash;包含过去 7 天的分析  
  - 过去一个月&mdash;包含过去 30 天的分析  
  - 自定义日期范围&mdash;包含指定日期范围的分析（如果可用）  
  
  > [!NOTE]  
  > 最长可能需要在 24 小时后，指标才会显示在仪表板上。 仪表板显示上次更新数据的日期和时间。  
  
  > [!NOTE]  
  > 从启用必应统计信息加载项时开始提供指标。 


## <a name="charts-and-graphs"></a>图表和图形

仪表板显示适用于选定终结点的指标的图表和图形。 并非所有指标都适用于所有终结点。 每个终结点的图表和图形是静态的（无法选择要显示的图表和图形）。 仪表板仅显示具有数据的图表和图形。 

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

下面是可能的指标。 每个指标注明了终结点限制。 

- **调用量**：显示在报告时段发出的调用次数。 如果报告时段为一天，则图表显示每小时发出的调用次数。 否则，图表显示报告时段内每天发出的调用次数。  
  
  > [!NOTE]
  > 调用量可能不同于计费报告，后者通常只包括成功的调用。 
  
-  **最常见的查询**：显示在报告时段最常见的查询，以及每个查询的出现次数。 可以配置显示的查询数。 例如，可以显示最常见的 25、50 或 75 个查询。 “最常见的查询”不适用于以下终结点：  
  
  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > 可以抑制一些查询字词，以删除电子邮件、电话号码、SSN 等机密信息。  

- **地理分布**：结果的来源市场。 例如，en-us (美国英语) 必应使用 `mkt` 查询参数来确定市场（如果已指定）。 否则，必应使用调用方 IP 地址等信号来确定市场。  
  
- **响应代码分布**：在报告时段发出的所有调用的 HTTP 状态代码。  
  
- **调用来源分布**：用户使用的浏览器类型。 例如 Edge、Chrome、Safari 和 FireFox。 从浏览器外部（例如机器人、Postman）发出的调用，或在控制台应用中使用 curl 发出的调用，将分组到“库”下面。 来源是使用请求的 User-Agent 标头值确定的。 如果请求不包含 User-Agent 标头，则必应会尝试从其他信号派生来源。  
  
- **安全搜索分布**：安全搜索值的分布。 例如 off、moderate 或 strict。 `safeSearch` 查询参数包含该值（如果已指定）。 否则，必应使用默认值 moderate。  
  
- **请求的回答分布**：在 `responseFilter` 查询参数中请求的 Web 搜索 API 回答。  
  
- **返回的回答分布**：Web 搜索 API 在响应中返回的回答。  
  
- **响应服务器分布**：为 API 请求提供服务的应用程序服务器。 可能的值为 Bing.com（从台式机和便携式设备为流量提供服务）和 Bing.com-mobile（从移动设备为流量提供服务）。 服务器是使用请求的 User-Agent 标头值确定的。 如果请求不包含 User-Agent 标头，则必应会尝试从其他信号派生服务器。  
  


下图显示了适用于每个终结点的分析。

![按终结点显示的分布支持矩阵](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)


