---
title: 快速入门：检测异常的异常情况检测程序 REST API 使用时序数据和C#|Microsoft Docs
description: 使用异常检测器 API 来检测异常数据系列中，作为一个批或针对流式处理数据。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 04b331f3b63ad6400b4bb8efcd053d04ac88989b
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595841"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>快速入门：检测异常的异常情况检测程序 REST API 使用时序数据和C# 

使用本快速入门教程开始使用异常检测器 API 的两种检测模式来检测异常的时序数据。 此C#应用程序将发送包含 JSON 格式的时间序列数据的两个 API 请求并获取响应。

| API 请求                                        | 应用程序输出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 作为一批中检测异常                        | JSON 响应包含用于时间序列数据和任何检测到的异常的位置中的每个数据点异常状态 （和其他数据）。 |
| 检测到异常情况状态的最新的数据点 | JSON 响应包含用于时间序列数据的最新数据点的异常状态 （和其他数据）。                                                                                                                                         |

 虽然此应用程序是使用 C# 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

## <a name="prerequisites"></a>必备组件

- 任何版本的[Visual Studio 2017 或更高版本](https://visualstudio.microsoft.com/downloads/)，

- [Json.NET](https://www.newtonsoft.com/json) 框架，可以 NuGet 包的形式提供。 若要安装 Newtonsoft.Json NuGet 包在 Visual Studio 中的形式：
    
    1. 右键单击您的项目**解决方案资源管理器**。
    2. 选择**管理 NuGet 包**。
    3. 搜索*Newtonsoft.Json*和安装包。

- 如果使用的 Linux/MacOS，可以通过使用运行此应用程序[Mono](https://www.mono-project.com/)。

- JSON 文件包含时间序列数据点。 本快速入门教程的示例数据，可[GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>创建新应用程序

1. 在 Visual Studio 中，创建新的控制台解决方案并添加以下包。 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. 为你的订阅密钥和你的终结点创建变量。 下面是可用于异常情况检测的 Uri。 这些将追加到你的服务终结点更高版本才能创建 API 请求 Url。

    |检测方法  |URI  |
    |---------|---------|
    |批处理检测    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |最新的数据点上的检测     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>创建一个函数来发送请求

1. 创建一个名为的新异步函数`Request`采用上面创建的变量。

2. 设置客户端的安全协议和标头信息使用`HttpClient`对象。 请确保添加到你的订阅密钥`Ocp-Apim-Subscription-Key`标头。 然后创建`StringContent`请求对象。

3. 发送包含请求`PostAsync()`，然后返回响应。

```csharp
static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        return await res.Content.ReadAsStringAsync();
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>作为一批中检测异常

1. 创建一个名为的新函数`detectAnomaliesBatch()`。 构造请求并将其发送通过调用`Request()`与你的终结点、 订阅密钥、 批处理异常情况检测的 URL 和时间序列数据的函数。

2. 反序列化 JSON 对象，并将其写入到控制台。

3. 如果响应包含`code`字段中，打印的错误代码和错误消息。 

4. 否则，在数据集中发现的异常的位置。 响应的`isAnomaly`字段包含布尔值的数组，其中每个指示数据点是否为异常。 将其转换为响应对象的字符串数组`ToObject<bool[]>()`函数。 循环访问数组，并打印的任何索引`true`值。 如果任何发现，这些值对应于异常的数据点的索引。

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    if (jsonObj["code"] != null){
        System.Console.WriteLine($"Detection failed. ErrorCode:{jsonObj["code"]}, ErrorMessage:{jsonObj["message"]}");
    }
    else{
        bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
        System.Console.WriteLine("\nAnomalies detected in the following data positions:");
        for (var i = 0; i < anomalies.Length; i++){
            if (anomalies[i])
            {
                System.Console.Write(i + ", ");
            }
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测到异常情况状态的最新的数据点

1. 创建一个名为的新函数`detectAnomaliesLatest()`。 构造请求并将其发送通过调用`Request()`与你的终结点、 订阅密钥、 最新点异常情况检测的 URL 和时间序列数据的函数。

2. 反序列化 JSON 对象，并将其写入到控制台。

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\nDetermining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>加载时间系列数据并发送请求

1. 应用程序的 main 方法，与将 JSON 时间系列数据加载`File.ReadAllText()`。 

2. 调用上面创建的异常情况检测函数。 使用`System.Console.ReadKey()`后运行应用程序保留在控制台窗口打开。

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>示例响应

JSON 格式返回成功的响应。 单击以下链接，查看 GitHub 上的 JSON 响应：
* [示例批处理检测响应](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [示例最新点检测响应](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
