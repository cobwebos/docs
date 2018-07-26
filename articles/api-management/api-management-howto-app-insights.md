---
title: 如何将 Azure API 管理与 Azure Application Insights 集成 | Microsoft Docs
description: 了解如何在 Azure Application Insights 中记录和查看来自 Azure API 管理的事件。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 8546b1228c8d8f213cb87692144e8d1d31a949d8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001797"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>如何将 Azure API 管理与 Azure Application Insights 集成

Azure API 管理可以轻松地与 Azure Application Insights 集成（一个可扩展的服务，可让 Web 开发人员在多个平台上生成和管理应用）。 本指南逐步讲解这种集成的每个步骤，并介绍可以采取哪些策略来降低对 API 管理服务实例的性能影响。

## <a name="prerequisites"></a>先决条件

若要执行本指南中的步骤，需要一个 Azure API 管理实例。 如果没有该实例，请先完成[此教程](get-started-create-service-instance.md)。

## <a name="create-an-azure-application-insights-instance"></a>创建 Azure Application Insights 实例

在使用 Azure Application Insights 之前，需要先创建一个服务实例。

1. 打开 **Azure 门户**，导航到“Application Insights”。  
    ![App Insights 创建](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. 单击“+ 添加”。  
    ![App Insights 创建](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. 填写窗体。 选择“常规”作为“应用程序类型”。
4. 单击“创建”。

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>在 Azure Application Insights 与 Azure API 管理服务实例之间创建连接

1. 在 **Azure 门户**中导航到自己的 **Azure API 管理服务实例**。
2. 在左侧菜单中选择“Application Insights”。
3. 单击“+ 添加”。  
    ![App Insights 记录器](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. 选择前面创建的 **Application Insights** 实例并提供简短说明。
5. 单击“创建”。
6. 你刚刚创建了一个具有检测密钥的 Azure Application Insights 记录器。 该记录器现在应已显示在列表中。  
    ![App Insights 记录器](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> 在后台，将在 API 管理实例中创建了一个 [Logger](https://docs.microsoft.com/en-us/rest/api/apimanagement/logger/createorupdate) 实体，其中包含 Application Insights 实例的检测密钥。

## <a name="enable-application-insights-logging-for-your-api"></a>为 API 启用 Application Insights 日志记录

1. 在 **Azure 门户**中导航到自己的 **Azure API 管理服务实例**。
2. 在左侧菜单中选择“API”。
3. 单击你的 API（在本例中为“演示会议 API”）。
4. 从顶部栏转到“设置”选项卡。
5. 向下滚动到“诊断日志”部分。  
    ![App Insights 记录器](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. 选中“启用”框。
7. 在“目标”下拉列表中选择附加的记录器。
8. 输入 **100** 作为“采样率”，并勾选“始终记录错误”复选框。
9. 在“正文的前几个字节”字段中输入 **1024**。
10. 单击“ **保存**”。

> [!NOTE]
> 在后台，将在 API 级别创建一个名为“applicationinsights”的 [Diagnostic](https://docs.microsoft.com/en-us/rest/api/apimanagement/diagnostic/createorupdate) 实体。

| 设置名称                        | 值类型                        | Description                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 启用                              | 布尔值                           | 指定是否要启用此 API 的日志记录。                                                                                                                                                                                                                                                                                                |
| 目标                         | Azure Application Insights 记录器 | 指定要使用的 Azure Application Insights 记录器                                                                                                                                                                                                                                                                                           |
| 采样率                        | decimal                           | 值为 0 到 100（百分比）。 <br/> 指定要将多少百分比的请求记录到 Azure Application Insights。 0% 采样表示不记录任何请求，100% 采样表示记录所有请求。 <br/> 此设置用于降低将请求记录到 Azure Application Insights 时所造成的性能影响（请参阅以下部分）。 |
| 始终记录错误                   | 布尔值                           | 如果选择此设置，则会将所有错误记录到 Azure Application Insights，不管“采样率”设置如何。                                                                                                                                                                                                                  |
| 基本选项：标头              | list                              | 指定要记录到 Azure Application Insights 的请求和响应标头。  默认值：不记录标头。                                                                                                                                                                                                             |
| 基本选项：正文的前几个字节  | integer                           | 指定要将请求和响应正文的最前面多少个字节记录到 Azure Application Insights。  默认值：不记录正文。                                                                                                                                                                                              |
| 高级选项：前端请求  |                                   | 指定是否以及如何将前端请求记录到 Azure Application Insights。 前端请求是传入 Azure API 管理服务的请求。                                                                                                                                                                        |
| 高级选项：前端响应 |                                   | 指定是否以及如何将前端响应记录到 Azure Application Insights。 前端响应是传出 Azure API 管理服务的响应。                                                                                                                                                                   |
| 高级选项：后端请求   |                                   | 指定是否以及如何将后端请求记录到 Azure Application Insights。 后端请求是传出 Azure API 管理服务的请求。                                                                                                                                                                        |
| 高级选项：后端响应  |                                   | 指定是否以及如何将后端响应记录到 Azure Application Insights。 后端响应是传入 Azure API 管理服务的响应。                                                                                                                                                                       |

> [!NOTE]
> 可以在不同的级别指定记录器 - 指定单个 API 记录器，或者对所有 API 使用一个记录器。
>  
> 如果同时指定这两个选项：
> + 如果它们是不同的记录器，则会同时使用两者（多路复用日志）；
> + 如果它们是相同的记录器但采用不同的设置，则单个 API 的记录器（粒度级更高）将替代所有 API 的记录器。

## <a name="what-data-is-added-to-azure-application-insights"></a>将哪些数据添加到 Azure Application Insights

Azure Application Insights 接收：

+ 每个传入请求（前端请求、前端响应）的请求遥测项；
+ 转发到后端服务的每个请求（后端请求、后端响应）的依赖项遥测项；
+ 每个失败的请求的异常遥测项。

失败的请求是具有以下特征的请求：

+ 由于客户端连接关闭而失败，或
+ 触发了 API 策略的 *on-error* 节，或
+ 具有与 4xx 或 5xx 匹配的响应 HTTP 状态代码。

## <a name="performance-implications-and-log-sampling"></a>性能影响和日志采样

> [!WARNING]
> 记录所有事件可能会产生严重的性能影响，具体取决于传入请求的速率。

根据内部负载测试，当请求速率超过每秒 1,000 个请求时，启用此功能会导致吞吐量下降 40%-50%。 Azure Application Insights 设计为使用统计分析来评估应用程序的性能。 它并非旨在用作审核系统，并且不适合用于记录高流量 API 的每个请求。

可以通过调整“采样率”设置来操控所记录的请求数（参阅前面的步骤）。 值 100% 表示记录所有请求，0% 表示不记录任何请求。 “采样率”有助于减少遥测量，可有效防止性能出现明显下降，同时仍可保持日志记录的优势。

跳过请求和响应的标头与正文的日志记录也有利于缓解性能问题。

## <a name="next-steps"></a>后续步骤

+ 详细了解 [Azure Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/)。
+ 考虑[使用 Azure 事件中心进行日志记录](api-management-howto-log-event-hubs.md)。
