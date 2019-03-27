---
title: 快速入门：检测异常作为批处理使用异常检测器 REST API 和 Python |Microsoft Docs
description: 使用异常检测器 API 来检测异常数据系列中，作为一个批或针对流式处理数据。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 60307d51439b4474c8be4f040792c03a6f83b0fd
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473139"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>快速入门：使用异常检测器 REST API 和 Python 时序数据中检测异常

使用本快速入门教程开始使用异常检测器 API 的两种检测模式来检测异常的时序数据。 此 Python 应用程序发送包含 JSON 格式的时间序列数据的两个 API 请求，并获取响应。

| API 请求                                        | 应用程序输出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 作为一批中检测异常                        | JSON 响应包含用于时间序列数据和任何检测到的异常的位置中的每个数据点异常状态 （和其他数据）。 |
| 检测到异常情况状态的最新的数据点 | JSON 响应包含用于时间序列数据的最新数据点的异常状态 （和其他数据）。                                                                                                                                         |

 虽然此应用程序是使用 Python 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

## <a name="prerequisites"></a>必备组件

- [Python 2.x 或 3.x](https://www.python.org/downloads/)

- [请求库](http://docs.python-requests.org)适用于 python

- JSON 文件包含时间序列数据点。 本快速入门教程的示例数据，可[GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>创建新应用程序

1. 在最喜欢的文本编辑器或 IDE 中，创建新的 python 文件。 添加以下导入。

    ```python
    import requests
    import json
    ```

2. 为你的订阅密钥和你的终结点创建变量。 下面是可用于异常情况检测的 Uri。 这些将追加到你的服务终结点更高版本才能创建 API 请求 Url。

    |检测方法  |URI  |
    |---------|---------|
    |批处理检测    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |最新的数据点上的检测     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. 打开它，并使用 JSON 数据文件中读取`json.load()`。 

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>创建一个函数来发送请求

1. 创建一个名为的新函数`send_request()`采用上面创建的变量。 然后，执行以下步骤。

2. 创建的请求标头的字典。 设置`Content-Type`到`application/json`，并添加你的订阅密钥的`Ocp-Apim-Subscription-Key`标头。

3. 发送请求使用`requests.post()`。 合并你的终结点和完整的异常情况检测 URL 请求的 URL，并包括标头和 json 请求数据。 

4. 如果请求成功，返回的响应。  
    
    ```python
    def send_request(endpoint, url, subscription_key, request_data):
        headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
        response = requests.post(endpoint+url, data=json.dumps(request_data), headers=headers)
        if response.status_code == 200:
            return json.loads(response.content.decode("utf-8"))
        else:
            print(response.status_code)
            raise Exception(response.text)
    ```

## <a name="detect-anomalies-as-a-batch"></a>作为一批中检测异常

1. 创建一个名为方法`detect_batch()`来检测异常在整个数据作为一个批。 调用`send_request()`上面创建的终结点、 url、 订阅密钥和 json 数据的方法。 

2. 调用`json.dumps()`上要设置其格式，并将其打印到控制台的结果。

3. 在数据集中找到异常的位置。 响应的`isAnomaly`字段包含与给定的数据点是否异常的布尔值。 循环访问列表中，并打印的任何索引`True`值。 如果任何发现，这些值对应于异常的数据点的索引。

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))

    # Find and display the positions of anomalies in the data set
    anomalies = result["isAnomaly"]
    print("Anomalies detected in the following data positions:")
    for x in range(len(anomalies)):
        if anomalies[x] == True:
            print (x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测到异常情况状态的最新的数据点

1. 创建一个名为方法`detect_latest()`确定时间序列中的最新数据点是否异常。 调用`send_request()`上述使用您终结点、 url、 订阅密钥和 json 数据的方法。 

2. 调用`json.dumps()`上要设置其格式，并将其打印到控制台的结果。

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>加载时间系列数据并发送请求

1. 将 JSON 时间系列数据打开文件处理程序，并使用加载`json.load()`上它。 然后，调用异常上面创建的检测方法。
    
    ```python
    file_handler = open (data_location)
    json_data = json.load(file_handler)
    
    detect_batch(json_data)
    detect_latest(json_data)
    ```

### <a name="example-response"></a>示例响应

JSON 格式返回成功的响应。 单击以下链接，查看 GitHub 上的 JSON 响应：
* [示例批处理检测响应](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [示例最新点检测响应](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)