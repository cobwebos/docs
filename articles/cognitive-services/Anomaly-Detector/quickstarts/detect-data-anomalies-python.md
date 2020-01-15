---
title: 快速入门：使用异常检测器 REST API 和 Python 以批的形式检测异常
titleSuffix: Azure Cognitive Services
description: 参考本快速入门使用异常检测器 API 以批处理形式或在流式处理数据时检测数据系列中的异常。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 7cef1c8c1b05c859f9435b06c3096bb983965656
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448871"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>快速入门：使用异常检测器 REST API 和 Python 检测时序数据的异常

参考本快速入门可以开始使用异常检测器 API 的两种检测模式来检测时序数据的异常。 此 Python 应用程序发送两个包含 JSON 格式的时序数据的 API 请求，并获取响应。

| API 请求                                        | 应用程序输出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 以批的形式检测异常                        | JSON 响应包含时序数据中每个数据点的异常状态（和其他数据），以及检测到的任何异常所在的位置。 |
| 检测最新数据点的异常状态 | JSON 响应包含时序数据中最新数据点的异常状态（和其他数据）。                                                                                                                                         |

 虽然此应用程序是使用 Python 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。 可在 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py) 上找到本快速入门的源代码。

## <a name="prerequisites"></a>必备条件

- [Python 2.x 或 3.x](https://www.python.org/downloads/)
- 异常检测器密钥和终结点
- Python 的[请求库](https://pypi.org/project/requests/)

- 包含时序数据点的 JSON 文件。 可在 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json) 上找到本快速入门的示例数据。

### <a name="create-an-anomaly-detector-resource"></a>创建异常检测器资源

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]


## <a name="create-a-new-application"></a>创建新应用程序

1. 创建新的 python 文件并添加以下导入。

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. 为订阅密钥和终结点创建变量。 下面是可用于异常检测的 URI。 稍后会将这些 URL 追加到服务终结点，以创建 API 请求 URL。

    |检测方法  |URI  |
    |---------|---------|
    |批量检测    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |对最新数据点进行检测     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. 打开 JSON 数据文件，并使用 `json.load()` 读入该文件。

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>创建用于发送请求的函数

1. 创建名为 `send_request()` 的新函数，该函数使用上面创建的变量。 然后，执行以下步骤。

2. 创建请求标头的字典。 将 `Content-Type` 设置为 `application/json`，并将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头。

3. 使用 `requests.post()` 发送请求。 合并终结点和异常检测 URL 以构成完整的请求 URL，并包含标头和 JSON 请求数据。 然后返回响应。

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>以批的形式检测异常

1. 创建名为 `detect_batch()` 的方法，以批的形式检测整个数据中的异常。 调用在上面使用终结点、URL、订阅密钥和 JSON 数据创建的 `send_request()` 方法。

2. 针对结果调用 `json.dumps()` 以设置其格式，然后将结果输出到控制台。

3. 如果响应包含 `code` 字段，请输出错误代码和错误消息。

4. 否则，请查找异常在数据集中的位置。 响应的 `isAnomaly` 字段包含一个布尔值，该值与给定的数据点是否为异常相关。 循环访问该列表，并输出任何 `True` 值的索引。 如果找到任何此类值，这些值对应于异常数据点的索引。

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测最新数据点的异常状态

1. 创建名为 `detect_latest()` 的方法，以确定时序中的最新数据点是否为异常。 结合终结点、URL、订阅密钥和 JSON 数据调用上述 `send_request()` 方法。 

2. 针对结果调用 `json.dumps()` 以设置其格式，然后将结果输出到控制台。

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>发送请求

调用上面创建的异常检测方法。

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>示例响应

成功的响应以 JSON 格式返回。 单击以下链接在 GitHub 上查看 JSON 响应：
* [批量检测响应示例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新数据点检测响应示例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
