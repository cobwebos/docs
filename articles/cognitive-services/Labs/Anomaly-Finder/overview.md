---
title: 什么是异常查找器？ - Microsoft 认知服务 | Microsoft Docs
description: 使用异常查找器中的高级算法可帮助识别时序数据中的异常并在 Microsoft 认知服务中返回信息。
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 1080bb0ad1d901a8b9a5ace4993d4e0d46924a03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366048"
---
# <a name="what-is-anomaly-finder"></a>什么是异常查找器？

通过异常查找器可随时间推移监视数据，并使用机器学习检测异常，机器学习会自动应用正确的统计模型来适应特定数据（不受行业、方案或数据量的影响）。 异常查找器 API 将时间序列作为输入，返回指示数据点是否异常的结果，确定预期值以及可视化的上限和下限。 异常查找器是预生成的 AI 服务，除需了解如何使用 RESTful API 外，无需任何机器学习专业知识。 它可处理任何时序数据，还可构建到流数据系统中，让开发变得灵活便捷。 异常查找器涵盖了各方面的用例，例如，用于管理欺诈、盗窃、市场变化和潜在商业事件的金融工具，或以匿名方式监视 IoT 设备流量。 此解决方案还可货币化为某服务的一部分，便于最终客户了解数据、支出、投资回报或用户活动的变化。
试用异常查找器 AP，深入了解数据。 

了解使用此 API 可构建的内容：

* 学习如何根据时间序列中的历史数据预测预期值
* 判断数据点是否异于历史模式
* 生成一个区段以显示“正常”值的范围

![异常查找器](./media/anomaly_detection1.png) 

图 1：检测销售收入中的异常

![异常查找器](./media/anomaly_detection2.png)

图 2：检测服务请求中的模式更改

## <a name="requirements"></a>要求

- 输入时序的最小数据：最少 13 个无明确周期的时序数据点，最少 4 轮周期已知的时序数据点。 
- 数据完整性：时序数据点按相同时间间隔划分，且无数据点丢失。 

## <a name="identify-anomalies"></a>识别异常

异常情况检测 API 返回结果，指示给定数据点是否异常，并提供如下所示的其他信息
* Period - API 检测异常点时所采用的检测周期。
* WarningText - 可能的警告信息。
* ExpectedValue - 由基于学习的模型所预测的值
* IsAnomaly - 用于指示数据点是否异常的结果
* IsAnomaly_Neg - 用于指示负向（下降）数据点是否异常的结果
* IsAnomaly_Pos - 用于指示正向（峰值）数据点是否异常的结果
* UpperMargin - ExpectedValue 和 UpperMargin 的总和用于确定数据点正常范围的上限
* LowerMargin - (ExpectedValue - LowerMargin) 用于确定数据点正常范围的下限

> [!Note]
> UpperMargin 和 LowerMargin 可用于生成包含实际时间序列的区段，用于直观显示正常值的范围。 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>调整响应后处理中的下限和上限

异常情况检测 API 返回指示数据点是否异常的默认结果，且可通过 ExpectedValue 和 UpperMargin/LowerMargin 计算出上限和下限。 在大多情况下，这些默认值足以满足需要。 然而，某些应用情景所需限值与默认限值不同。 建议对 UpperMargin 或 LowerMargin 应用系数来调整动态界限。

### <a name="examples-with-1152-as-coefficiency"></a>系数分别为 1/1.5/2 时的相应示例

![默认敏感度](./media/sensitivity_1.png)

![敏感度 1.5](./media/sensitivity_1.5.png)

![敏感度 2](./media/sensitivity_2.png)

包含示例数据的请求

[!INCLUDE [Request](./includes/request.md)]

示例 JSON 响应

[!INCLUDE [Response](./includes/response.md)]
