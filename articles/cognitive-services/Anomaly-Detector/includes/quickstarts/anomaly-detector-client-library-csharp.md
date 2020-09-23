---
title: 异常检测器 .NET 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/04/2020
ms.author: aahi
ms.openlocfilehash: 6f1c016efb300dea2cdef91c84bb901cffd09fa0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025008"
---
开始使用适用于 .NET 的异常检测器客户端库。 请按照以下步骤安装程序包并试用基本任务的示例代码。 通过异常检测器服务，可以对时序数据自动使用最佳适配模型，从而查找器其中的异常，不限行业、场景或数据量。

使用适用于 .NET 的异常检测器客户端库，可实现以下操作：

* 以批请求的形式检测整个时序数据集中的异常
* 在时序中检测最新数据点的异常状态
* 检测数据集中的趋势更改点。

[库参考文档](https://aka.ms/anomaly-detector-dotnet-ref) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [包 (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.2) | [在 GitHub 上查找代码](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 的当前版本
* 拥有 Azure 订阅后，可在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="创建异常检测器资源"  target="_blank">创建异常检测器资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>来获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到异常检测器 API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-net-core-application"></a>创建新的 .NET Core 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `anomaly-detector-quickstart` 的新控制台应用。 此命令将创建包含单个 C# 源文件的简单“Hello World”项目：*Program.cs*。

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```dotnetcli
dotnet build
```

生成输出不应包含警告或错误。

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>安装客户端库

在应用程序目录中，使用以下命令安装适用于 .NET 的异常检测器客户端库：

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.2
```

从项目目录中，打开 *Program.cs* 文件，并添加以下 using `directives`：

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

在应用程序的 `main()` 方法中，为资源的 Azure 位置创建变量，并将密钥创建为环境变量。 如果在启动应用程序后创建了环境变量，则需要关闭并重新加载运行它的编辑器、IDE 或 shell 以访问该变量。

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>对象模型

异常检测器客户端是 [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) 对象，使用包含密钥的 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials) 对 Azure 进行身份验证。 客户端可以使用 [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync) 对整个数据集进行异常情况检测，或使用 [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync) 对最新的数据点进行异常情况检测。 [ChangePointDetectAsync](https://aka.ms/anomaly-detector-dotnet-ref) 方法可检测在趋势中标记更改的点。

时序数据作为 [Request](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) 对象中的一系列 [Point](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) 进行发送。 `Request` 对象包含描述数据的属性（例如[Granularity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity)）以及异常检测的参数。

异常检测器响应是 [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse)、[LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) 或 [changePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref) 对象，具体取决于所使用的方法。

## <a name="code-examples"></a>代码示例

这些代码片段展示如何使用适用于 .NET 的异常检测器客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [从文件加载时序数据集](#load-time-series-data-from-a-file)
* [在整个数据集中检测异常](#detect-anomalies-in-the-entire-data-set)
* [检测最新数据点的异常状态](#detect-the-anomaly-status-of-the-latest-data-point)
* [检测数据集中的更改点](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>验证客户端

在新方法中，使用终结点和密钥实例化客户端。 使用密钥创建 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) 对象，并将其与终结点一起创建 [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) 对象。

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]

## <a name="load-time-series-data-from-a-file"></a>从文件加载时序数据

从 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) 下载此快速入门中的示例数据：
1. 在浏览器中，右键单击“原始”。
2. 单击“将链接另存为”。
3. 将文件另存为 .csv 文件，保存到你的应用程序目录。

此时序数据的格式为 .csv 文件，它将被发送到异常检测器 API。

创建新方法来读取时序数据，并将其添加到 [Request](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) 对象。 使用文件路径调用 `File.ReadAllLines()`，并创建一系列 [Point](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) 对象，并去除任何新行字符。 提取值并将日期戳与其数值分开，并将它们添加到新 `Point` 对象。

创建包含一系列点的 `Request` 对象，并创建 `Granularity.Daily` 来表示数据点的 [Granularity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview)（或周期）。

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>在整个数据集中检测异常

创建一个方法使用 `Request` 对象调用客户端的 [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) 方法，并等待作为 [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) 对象的响应。 如果时序包含任何异常，循环访问响应的 [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) 值，并打印显示为 `true` 的值。 如果找到任何此类值，这些值对应于异常数据点的索引。

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测最新数据点的异常状态

创建一个方法使用 `Request` 对象调用客户端的 [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) 方法，并等待作为 [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) 对象的响应。 检查响应的 [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) 属性以确定发送的最新数据点是否异常。

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="detect-change-points-in-the-data-set"></a>检测数据集中的更改点

创建一个方法，使用 `Request` 对象调用客户端的 [DetectChangePointAsync](https://aka.ms/anomaly-detector-dotnet-ref) 方法，并等待作为 [ChangePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref) 对象的响应。 检查响应的 IsChangePoint 值，并打印任何为 `true` 的值。 如果找到任何此类值，这些值对应于趋势更改点。

[!code-csharp[DetectChangePoint() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=changePointExample)]

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `dotnet run` 命令运行应用程序。

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
