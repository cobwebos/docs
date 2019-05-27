---
title: 什么是异常检测器 API？ | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 使用异常检测器 API 的高级的算法来标识时序数据中的异常。
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 48dffb8853d3def590e434c1683ecf038e2a194e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "64415825"
---
# <a name="what-is-the-anomaly-detector-api"></a>什么是异常检测器 API？

通过异常检测器 API，可使用机器学习监视和检测时序数据中的异常。 通过自动发现并将最佳配适模型应用于你的数据，而不考虑行业，方案中或数据量调整异常检测程序 API。 使用时序数据，API 确定用于异常情况检测，预期值的边界和哪些数据点都是异常。

![检测服务请求中的模式更改](./media/anomaly_detection2.png)

使用异常情况检测程序无需在机器学习，任何经验和 RESTful API，你可以轻松地将服务集成到应用程序和进程。

## <a name="features"></a>功能

使用异常情况检测程序，您可以自动检测到异常整个时序数据，或实时发生的方式。 

|Feature  |描述  |
|---------|---------|
|在实时发生，检测到异常。 | 通过使用先前已见到的数据点以确定最新项目是否异常流式处理数据中检测异常。 此操作将生成使用数据点将发送，并确定目标在于异常的模型。 通过调用生成的每个新数据点的 API，可以监视你的数据，因为这会创建。 |
|作为一个批，检测到整个数据集的异常。 | 使用时间序列检测整个数据可能存在的任何异常。 此操作将生成整个时间系列数据，使用与分析时相同的模型的每个点的模型。         |
| 获取有关你的数据的其他信息。 | 获取有关你的数据和任何观察到的异常，包括预期值、 异常边界和位置的有用详细信息。 |
| 调整异常情况检测边界。 | 异常情况检测器 API 将自动创建边界用于异常情况检测。 调整这些边界，以增大或减小数据异常到 API 的敏感度和更好地适应你的数据。 |

## <a name="demo"></a>演示

若要快速开始使用异常检测器 API，请尝试[在线演示](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)，可以在浏览器中运行。 此演示在 web 承载的 Jupyter 笔记本中运行，并演示如何发送 API 请求，并直观显示结果。

若要运行此演示，请完成以下步骤：

1. 获取有效的异常情况检测器 API 订阅密钥和 API 终结点。 下面的部分包含有关注册的说明。 
2. 登录，并单击右上角的克隆。
3. 单击**免费的计算机上运行**
4. 选择其中一个为本示例的笔记本。
5. 添加到有效的异常情况检测器 API 订阅密钥`subscription_key`变量。 更改`endpoint`变量与你的终结点。 例如： `https://westus2.api.cognitive.microsoft.com`
1. 在顶部菜单栏上，单击**单元格**，然后**全部运行**。

## <a name="workflow"></a>工作流

异常情况检测程序 API 是 RESTful web 服务，轻松地从任何可以发出 HTTP 请求和分析 JSON 的编程语言调用。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

注册后：

1. 执行时间系列数据并将其转换为有效的 JSON 格式。 使用[最佳做法](concepts/anomaly-detection-best-practices.md)准备数据以获得最佳结果时。
1. 将请求发送到与你的数据，异常情况检测器 API。
1. 通过分析返回的 JSON 消息处理 API 响应。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用异常情况检测程序 REST API 将时序数据中检测异常](quickstarts/detect-data-anomalies-csharp.md)
* 异常情况检测器 API[联机演示](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* 异常情况检测器[REST API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)