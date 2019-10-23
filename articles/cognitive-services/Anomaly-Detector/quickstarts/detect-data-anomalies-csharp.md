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
ms.date: 10/14/2019
ms.author: aahi
ms.openlocfilehash: 222fb5d37065bc40e9c96a9ff3487a7ea8ad0570
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554771"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>快速入门：使用异常检测器 REST API 和 C# 检测时序数据的异常 

参考本快速入门可以开始使用异常检测器 API 的两种检测模式来检测时序数据的异常。 此 C# 应用程序发送两个包含 JSON 格式的时序数据的 API 请求，并获取响应。

| API 请求                                        | 应用程序输出                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 以批的形式检测异常                        | JSON 响应包含时序数据中每个数据点的异常状态（和其他数据），以及检测到的任何异常所在的位置。 |
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

### <a name="create-an-anomaly-detector-resource"></a>创建异常检测器资源

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>创建新应用程序

1. 在 Visual Studio 中，创建新的控制台解决方案并添加以下包。 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. 为订阅密钥和终结点创建变量。 下面是可用于异常检测的 URI。 稍后会将这些 URL 追加到服务终结点，以创建 API 请求 URL。

    |检测方法  |URI  |
    |---------|---------|
    |批量检测    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |对最新数据点进行检测     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>创建用于发送请求的函数

1. 创建名为 `Request` 的新异步函数，该函数使用上面创建的变量。

2. 使用 `HttpClient` 对象设置客户端的安全协议和标头信息。 请务必将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头中。 然后，为请求创建 `StringContent` 对象。

3. 发送包含 `PostAsync()` 的请求，然后返回响应。

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>以批的形式检测异常

1. 创建名为 `detectAnomaliesBatch()` 的新函数。 通过终结点、订阅密钥、批量异常检测的 URL 以及时序数据调用 `Request()` 函数，构造并发送请求。

2. 反序列化 JSON 对象，并将其写入控制台。

3. 如果响应包含 `code` 字段，请输出错误代码和错误消息。 

4. 否则，请查找异常在数据集中的位置。 响应的 `isAnomaly` 字段包含布尔值数组，其中每个值都指示数据点是否为异常。 使用响应对象的 `ToObject<bool[]>()` 函数将其转换为字符串数组。 循环访问该数组，并输出任何 `true` 值的索引。 如果找到任何此类值，这些值对应于异常数据点的索引。

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测最新数据点的异常状态

1. 创建名为 `detectAnomaliesLatest()` 的新函数。 通过终结点、订阅密钥、最新数据点异常检测的 URL 以及时序数据调用 `Request()` 函数，构造并发送请求。

2. 反序列化 JSON 对象，并将其写入控制台。

[!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>加载时序数据并发送请求

1. 在应用程序的 main 方法中，使用 `File.ReadAllText()` 加载 JSON 时序数据。 

2. 调用上面创建的异常检测函数。 使用 `System.Console.ReadKey()`，在运行应用程序后让控制台窗口保持打开状态。

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>示例响应

成功的响应以 JSON 格式返回。 单击以下链接在 GitHub 上查看 JSON 响应：
* [批量检测响应示例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新数据点检测响应示例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[使用 Azure Databricks 流式处理异常检测](../tutorials/anomaly-detection-streaming-databricks.md)

* 什么是[异常检测器 API？](../overview.md)
* 使用异常检测器 API 时的[最佳做法](../concepts/anomaly-detection-best-practices.md)。
* 可以在 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs) 上找到此示例的源代码。
