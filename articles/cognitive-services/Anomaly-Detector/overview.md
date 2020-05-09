---
title: 什么是异常检测器 API？
titleSuffix: Azure Cognitive Services
description: 使用异常检测器 API 的高级算法来确定时序数据中的异常。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 9237e670dd8d43c4036f996c477948944718e3aa
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80053701"
---
# <a name="what-is-the-anomaly-detector-api"></a>什么是异常检测器 API？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

可以使用异常检测器 API 来监视并检测与机器学习配合使用的时序数据中的异常。 异常检测器 API 可以自动标识最佳适配模型并将其应用到数据，不限行业、场景或数据量，通过这种方式进行适应。 使用时序数据，此 API 可以确定异常检测的边界、预期的值，以及哪些数据点异常。

![检测服务请求中的模式更改](./media/anomaly_detection2.png)

使用异常检测器不需要以前在机器学习方面有任何经验，你可以使用 RESTful API 轻松地将服务集成到应用程序和进程中。

## <a name="features"></a>功能

可以使用异常检测器自动检测时序数据中的异常以及实时出现的异常。

|Feature  |说明  |
|---------|---------|
|检测实时出现的异常。 | 检测流式传输数据中的异常，方法是：使用以前见过的数据点来确定最近的数据点是否异常。 此操作使用发送的数据点生成一个模型，然后确定目标点是否异常。 每生成一个新数据点就调用该 API，这样就可以在创建数据时监视数据。 |
|以批的形式检测整个数据集中的异常。 | 使用时序来检测数据中可能存在的任何异常。 此操作使用整个时序数据生成一个模型，每个点使用同一模型进行分析。         |
| 获取数据的其他信息。 | 获取数据的有用详细信息以及任何观察到的异常，包括预期的值、异常边界和位置。 |
| 调整异常检测边界。 | 异常检测器 API 自动创建异常检测的边界。 调整这些边界，以便提高或降低 API 对数据异常的敏感度，并更好地拟合数据。 |

## <a name="demo"></a>演示

查看此[交互式演示](https://aka.ms/adDemo)以了解异常检测器的工作原理。
若要运行演示，需要创建一个异常检测器资源，并获取 API 密钥和终结点。

## <a name="notebook"></a>笔记本

若要了解如何调用异常检测器API，请试用此 [Azure Notebook](https://aka.ms/adNotebook)。 这个 Web 托管的 Jupyter Notebook 演示如何发送 API 请求和直观显示结果。

若要运行此 Notebook，请完成以下步骤：

1. 获取一个有效的异常检测器 API 订阅密钥和一个 API 终结点。 以下部分提供注册说明。
1. 登录，然后单击右上角的“克隆”。
1. 在完成克隆操作之前，请取消选中对话框中的“公共”选项，否则你的笔记本（包括任何订阅密钥）将是公共的。
1. 单击“在免费计算上运行” 
1. 选择其中一个笔记本。
1. 向 `subscription_key` 变量添加有效的异常检测器 API 订阅密钥。
1. 将 `endpoint` 变量更改为你的终结点。 例如： `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. 单击顶部菜单栏中的“单元格”，然后单击“全部运行”  。 

## <a name="workflow"></a>工作流

异常检测器 API 是一项 RESTful Web 服务，可以轻松地通过任何编程语言调用，只要该语言能够发出 HTTP 请求和分析 JSON 即可。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

注册后：

1. 获取时序数据并将其转换为有效的 JSON 格式。 在准备数据时使用[最佳做法](concepts/anomaly-detection-best-practices.md)，以便获取最佳结果。
1. 向包含你的数据的异常检测器 API 发送请求。
1. 通过分析返回的 JSON 消息处理 API 响应。

## <a name="algorithms"></a>算法

* 有关所使用的算法的信息，请参阅以下技术博客：
    * [Azure 异常检测器 API 简介](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Azure 异常检测器中的 SR-CNN 算法概述](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

可以阅读论文 [Microsoft 的时序异常检测服务](https://arxiv.org/abs/1906.03821)（KDD 2019 接受），详细了解 Microsoft 开发的 SR-CNN 算法。


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>加入“异常检测器”社区

* 加入 [Microsoft Teams 中的异常检测器顾问组](https://aka.ms/AdAdvisorsJoin)
* 请参阅选定的[用户生成的内容](user-generated-content.md)

## <a name="next-steps"></a>后续步骤

* [快速入门：使用异常检测器 REST API 检测时序数据的异常](quickstarts/detect-data-anomalies-csharp.md)
* 异常检测器 API [在线演示](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* 异常检测器 [REST API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
