---
title: 快速入门：使用异常检测器 REST API 和 Python 以批的形式检测异常 | Microsoft Docs
description: 使用异常检测器 API 能够以批或流数据的形式检测数据系列的异常。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 0ab39a7bb86671efb6258e3171e3bf7847aa241f
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341742"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>快速入门：使用异常检测器 REST API 和 Python 检测时序数据的异常

参考本快速入门可以开始使用异常检测器 API 的两种检测模式来检测时序数据的异常。 此 Python 应用程序发送两个包含 JSON 格式的时序数据的 API 请求，并获取响应。

| API 请求                                        | 应用程序输出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 以批的形式检测异常                        | JSON 响应包含时序数据中每个数据点的异常状态（和其他数据），以及检测到的任何异常所在的位置。 |
| 检测最新数据点的异常状态 | JSON 响应包含时序数据中最新数据点的异常状态（和其他数据）。                                                                                                                                         |

 虽然此应用程序是使用 Python 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

## <a name="prerequisites"></a>先决条件

- [Python 2.x 或 3.x](https://www.python.org/downloads/)

- Python 的[请求库](http://docs.python-requests.org)

- 包含时序数据点的 JSON 文件。 可在 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json) 上找到本快速入门的示例数据。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>创建新应用程序

1. 在你喜好的文本编辑器或 IDE 中创建一个新的 Python 文件。 添加以下 import 语句。

    ```python
    import requests
    import json
    ```

2. 为订阅密钥和终结点创建变量。 下面是可用于异常检测的 URI。 稍后会将这些 URI 追加到服务终结点，以创建 API 请求 URL。

    |检测方法  |URI  |
    |---------|---------|
    |批量检测    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |对最新数据点进行检测     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. 打开 JSON 数据文件，并使用 `json.load()` 读入该文件。

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>创建用于发送请求的函数

1. 创建名为 `send_request()` 的新函数，该函数使用上面创建的变量。 然后，执行以下步骤。

2. 创建请求标头的字典。 将 `Content-Type` 设置为 `application/json`，并将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头。

3. 使用 `requests.post()` 发送请求。 合并终结点和异常检测 URL 以构成完整的请求 URL，并包含标头和 JSON 请求数据。 然后返回响应。

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json',
               'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(
        endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>以批的形式检测异常

1. 创建名为 `detect_batch()` 的方法，以批的形式检测整个数据中的异常。 调用在上面使用终结点、URL、订阅密钥和 JSON 数据创建的 `send_request()` 方法。

2. 针对结果调用 `json.dumps()` 以设置其格式，然后将结果输出到控制台。

3. 如果响应包含 `code` 字段，则输出错误代码和错误消息。

4. 否则，查找异常在数据集中的位置。 响应的 `isAnomaly` 字段包含一个布尔值，该值与给定的数据点是否为异常相关。 循环访问该列表，并输出任何 `True` 值的索引。 如果找到任何此类值，这些值对应于异常数据点的索引。

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(
            result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print(x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测最新数据点的异常状态

1. 创建名为 `detect_latest()` 的方法，以确定时序中的最新数据点是否为异常。 结合终结点、URL、订阅密钥和 JSON 数据调用上述 `send_request()` 方法。 

2. 针对结果调用 `json.dumps()` 以设置其格式，然后将结果输出到控制台。

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>加载时序数据并发送请求

1. 打开文件处理程序，对 JSON 时序数据使用 `json.load()` 以加载此数据。 然后调用上面创建的异常检测方法。

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>示例响应

成功的响应以 JSON 格式返回。 单击以下链接以查看 GitHub 上的 JSON 响应：
* [批量检测响应示例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新数据点检测响应示例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
