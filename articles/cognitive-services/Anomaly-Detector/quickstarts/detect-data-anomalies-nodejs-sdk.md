---
title: 快速入门：使用适用于 Python 的异常探测器客户端库检测数据异常
titleSuffix: Azure Cognitive Services
description: 使用异常检测器 API 能够以批或流数据的形式检测数据系列的异常。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: 084ef1b81f7db5bd4a2d371e0c322211c0f0e142
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483497"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>快速入门：适用于 Node.js 的异常检测器客户端库

开始使用适用于 Node.js 的异常检测器客户端库。 请按照以下步骤安装程序包并试用基本任务的示例代码。 通过异常检测器服务，可以对时序数据自动使用最佳适配模型，从而查找器其中的异常，不限行业、场景或数据量。

使用适用于 Node.js 的异常检测器客户端库，可实现以下操作：

* 以批请求的形式检测整个时序数据集中的异常
* 在时序中检测最新数据点的异常状态

[参考文档](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [包 (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | [在 GitHub 上查找代码](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Node.js](https://nodejs.org/)
* 异常检测器密钥和终结点

## <a name="setting-up"></a>设置

### <a name="create-an-anomaly-detector-azure-resource"></a>创建异常检测器 Azure 资源

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

运行 `npm init` 命令以使用 `package.json` 文件创建一个 node 应用程序。 

```console
npm init
```

创建一个名为 `index.js` 的文件，并导入以下库：

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

为资源的 Azure 终结点和密钥创建变量。 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。 为将在后面的步骤中下载的示例数据文件创建另一个变量，并为数据点创建一个空列表。 然后创建一个 `ApiKeyCredentials` 对象以包含该密钥。

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>安装客户端库

安装 `ms-rest-azure` 和 `azure-cognitiveservices-anomalydetector` NPM 包。 此快速入门中也使用了 csv-parse 库：

```console
npm install  @azure/cognitiveservices-anomalydetector ms-rest-azure csv-parse
```

应用的 `package.json` 文件将使用依赖项进行更新。

## <a name="object-model"></a>对象模型

异常检测器客户端是 [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) 对象，该对象使用你的密钥向 Azure 进行身份验证。 该客户端提供两种异常检测方法：对整个数据集使用 [entireDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)，对最新数据点使用 [LastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-)。 

时序数据作为 [Request](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) 对象中的一系列 [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) 进行发送。 `Request` 对象包含描述数据的属性（例如[Granularity](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity)）以及异常检测的参数。 

异常检测器响应是 [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) 或 [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) 对象，具体取决于所使用的方法。 

## <a name="code-examples"></a>代码示例 

这些代码片段展示如何使用适用于 Node.js 的异常检测器客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [从文件加载时序数据集](#load-time-series-data-from-a-file)
* [在整个数据集中检测异常](#detect-anomalies-in-the-entire-data-set) 
* [检测最新数据点的异常状态](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>验证客户端

使用终结点和凭据实例化 [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) 对象。

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>从文件加载时序数据

从 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv) 下载此快速入门中的示例数据：
1. 在浏览器中，右键单击“原始”  。
2. 单击“将链接另存为”  。
3. 将文件另存为 .csv 文件，保存到你的应用程序目录。

此时序数据的格式为 .csv 文件，它将被发送到异常检测器 API。

使用 csv-parse 库的 `readFileSync()` 方法读取数据文件，并使用 `parse()` 分析文件。 对于每一行，推送包含时间戳和数值的 [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) 对象。

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>在整个数据集中检测异常 

调用 API 以使用客户端的 [entireDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) 方法批量检测整个时间序列中的异常。 存储返回的 [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) 对象。 循环访问响应的 `isAnomaly` 列表，并输出任何 `true` 值的索引。 如果找到任何此类值，这些值对应于异常数据点的索引。

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测最新数据点的异常状态

调用异常检测器 API，以便使用客户端的 [lastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) 方法确定最新数据点是否异常，并存储返回的 [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) 对象。 响应的 `isAnomaly` 值是一个布尔值，用于指定该点的异常状态。  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>运行应用程序

在快速入门文件中使用 `node` 命令运行应用程序。

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
