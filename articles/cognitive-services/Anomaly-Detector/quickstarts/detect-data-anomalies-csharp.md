---
title: 快速入门：使用异常检测器 REST API 和 C# 检测时序数据的异常
titleSuffix: Azure Cognitive Services
description: 使用异常检测器 API 能够以批或流数据的形式检测数据系列的异常。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 1b3ed38e7ce8738a0e92775915e894c6e0bbd52a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721558"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>快速入门：使用异常检测器 REST API 和 C# 检测时序数据的异常 

参考本快速入门可以开始使用异常检测器 API 的两种检测模式来检测时序数据的异常。 此 C# 应用程序发送两个包含 JSON 格式的时序数据的 API 请求，并获取响应。

| API 请求                                        | 应用程序输出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 批量检测异常                        | JSON 响应包含时序数据中每个数据点的异常状态（和其他数据），以及检测到的任何异常所在的位置。 |
| 检测最新数据点的异常状态 | JSON 响应包含时序数据中最新数据点的异常状态（和其他数据）。                                                                                                                                         |

 虽然此应用程序是使用 C# 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

## <a name="prerequisites"></a>先决条件

- 任何版本的 [Visual Studio 2017 或更高版本](https://visualstudio.microsoft.com/downloads/)、

- [Json.NET](https://www.newtonsoft.com/json) 框架，可以 NuGet 包的形式提供。 若要在 Visual Studio 中以 NuGet 包的形式安装 Newtonsoft.Json，请执行以下操作：
    
    1. 在**解决方案资源管理器**中右键单击你的项目。
    2. 选择“管理 NuGet 包”。 
    3. 搜索 *Newtonsoft.Json* 并安装该包。

- 如果使用的是 Linux/MacOS，则可使用 [Mono](https://www.mono-project.com/) 运行此应用程序。

- 包含时序数据点的 JSON 文件。 可在 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json) 上找到本快速入门的示例数据。

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

2. 为订阅密钥和终结点创建变量。 下面是可用于异常检测的 URI。 稍后会将这些 URL 追加到服务终结点，以创建 API 请求 URL。

    |检测方法  |URI  |
    |---------|---------|
    |批量检测    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |对最新数据点进行检测     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
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

## <a name="create-a-function-to-send-requests"></a>创建用于发送请求的函数

1. 创建名为 `Request` 的新异步函数，该函数使用上面创建的变量。

2. 使用 `HttpClient` 对象设置客户端的安全协议和标头信息。 请务必将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头中。 然后，为请求创建 `StringContent` 对象。

3. 发送包含 `PostAsync()` 的请求，然后返回响应。

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

## <a name="detect-anomalies-as-a-batch"></a>批量检测异常

1. 创建名为 `detectAnomaliesBatch()` 的新函数。 通过终结点、订阅密钥、批量异常检测的 URL 以及时序数据调用 `Request()` 函数，构造并发送请求。

2. 反序列化 JSON 对象，并将其写入控制台。

3. 如果响应包含 `code` 字段，请输出错误代码和错误消息。 

4. 否则，请查找异常在数据集中的位置。 响应的 `isAnomaly` 字段包含布尔值数组，其中每个值都指示数据点是否为异常。 使用响应对象的 `ToObject<bool[]>()` 函数将其转换为字符串数组。 循环访问该数组，并输出任何 `true` 值的索引。 如果找到任何此类值，这些值对应于异常数据点的索引。

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

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测最新数据点的异常状态

1. 创建名为 `detectAnomaliesLatest()` 的新函数。 通过终结点、订阅密钥、最新数据点异常检测的 URL 以及时序数据调用 `Request()` 函数，构造并发送请求。

2. 反序列化 JSON 对象，并将其写入控制台。

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

## <a name="load-your-time-series-data-and-send-the-request"></a>加载时序数据并发送请求

1. 在应用程序的 main 方法中，使用 `File.ReadAllText()` 加载 JSON 时序数据。 

2. 调用上面创建的异常检测函数。 使用 `System.Console.ReadKey()`，在运行应用程序后让控制台窗口保持打开状态。

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>示例响应

成功的响应以 JSON 格式返回。 单击以下链接在 GitHub 上查看 JSON 响应：
* [批量检测响应示例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新数据点检测响应示例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
