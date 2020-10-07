---
title: 什么是指标顾问服务？
titleSuffix: Azure Cognitive Services
description: 什么是指标顾问？
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 408bdd948977218d9b39a39bf97391a4141e545c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943398"
---
# <a name="what-is-metrics-advisor-preview"></a>什么是指标顾问（预览版）？ 

指标顾问是 Azure 认知服务的一部分，使用 AI 对时序数据执行数据监视和异常情况检测。 该服务自动执行将模型应用于数据的过程，并提供了一组 API 和基于 Web 的工作区，用于数据引入、异常检测和诊断，无需了解机器学习即可使用它们。 使用指标顾问执行以下操作：

* 分析来自多个数据源的多维数据 
* 识别并关联异常
* 配置并微调用于数据的异常情况检测模型
* 诊断异常，并帮助进行根本原因分析。 

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="指标顾问概述":::

## <a name="connect-to-a-variety-of-data-sources"></a>连接到各种数据源

指标顾问可以连接到多个数据存储，并从这些数据存储中[引入多维指标](how-tos/onboard-your-data.md)数据，包括：SQL Server、Azure Blob 存储、MongoDB 等。 

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>易于使用且可自定义的异常情况检测

* 指标顾问会自动为你的数据选择最佳模型，而无需了解任何机器学习。 
* 在[多维指标](glossary.md#multi-dimensional-metric)中自动监视每个时序。
* 使用[参数优化](how-tos/configure-metrics.md)和[交互式反馈](how-tos/anomaly-feedback.md)自定义应用于数据的模型以及将来的异常情况检测结果。


## <a name="real-time-alerts-through-multiple-channels"></a>通过多个通道发送实时警报

每当检测到异常时，指标顾问可以使用挂钩（例如：电子邮件挂钩、Webhook 和 Azure DevOps 挂钩）通过多个通道[发送实时警报](how-tos/alerts.md)。 通过灵活的警报规则可自定义要发送的警报和位置。

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>通过分析异常生成智能诊断见解

分析在多维指标上检测到的异常，并生成[智能诊断见解](how-tos/diagnose-incident.md)，包括最可能的根本原因、诊断树、指标钻取等。 通过配置[指标图](how-tos/metrics-graph.md)，可以启用跨指标分析来帮助你可视化事件。


## <a name="typical-workflow"></a>典型工作流

此工作流非常简单：载入数据后，可以微调异常检测，并创建适合场景的配置。

1. 为指标顾问[创建 Azure 资源](../cognitive-services-apis-create-account.md)。 
2. 尝试使用演示网站查看具有预配置示例数据的示例指标顾问实例。 
3. 使用 Web 门户生成首个监视器。
    1. 载入数据
    2. 微调异常情况检测
    3. 订阅警报
    4. 查看诊断见解
1. 使用 REST API 自定义实例。

## <a name="next-steps"></a>后续步骤

* 尝试[演示网站](quickstarts/explore-demo.md)。
* 浏览快速入门：[监视你关于 web 的第一个指标](quickstarts/web-portal.md)。
* 浏览快速入门：[使用 REST API 自定义解决方案](quickstarts/rest-api.md)。
