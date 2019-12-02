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
ms.openlocfilehash: f58f4f9c89e2c0bfb7d9eca6d39dab3f25b21990
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483338"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>快速入门：适用于 Python 的异常检测器客户端库

适用于 Python 的异常检测器客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。 通过异常检测器服务，可以对时序数据自动使用最佳适配模型，从而查找器其中的异常，不限行业、场景或数据量。

使用适用于 Python 的异常检测器客户端库，可实现以下操作：

* 以批请求的形式检测整个时序数据集中的异常
* 在时序中检测最新数据点的异常状态

[库参考文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [包 (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [在 GitHub 上查找示例代码](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 异常检测器密钥和终结点
* [Python 3.x](https://www.python.org/)
* [Pandas 数据分析库](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>设置

### <a name="create-an-anomaly-detector-resource"></a>创建异常检测器资源

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

 创建新的 Python 文件并导入以下库。

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

将密钥的变量创建为环境变量，同时创建时序数据文件的路径，以及订阅的 Azure 位置。 例如，`westus2` 。 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>安装客户端库

在安装 Python 后，可以通过以下命令安装客户端库：

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>对象模型

异常检测器客户端是 [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) 对象，该对象使用你的密钥向 Azure 进行身份验证。 该客户端提供两种异常检测方法：对整个数据集使用 [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)，对最新数据点使用 [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)。 

时序数据作为 [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) 对象中的一系列 [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) 进行发送。 `Request` 对象包含描述数据的属性（例如[Granularity](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python)）以及异常检测的参数。 

异常检测器响应是 [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) 或 [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) 对象，具体取决于所使用的方法。 

## <a name="code-examples"></a>代码示例 

这些代码片段展示如何使用适用于 Python 的异常检测器客户端库执行以下操作：

* [对客户端进行身份验证](#authenticate-the-client)
* [从文件加载时序数据集](#load-time-series-data-from-a-file)
* [在整个数据集中检测异常](#detect-anomalies-in-the-entire-data-set) 
* [检测最新数据点的异常状态](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>验证客户端

将 Azure 位置变量添加到终结点，并使用密钥对客户端进行身份验证。

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>从文件加载时序数据

从 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) 下载此快速入门中的示例数据：
1. 在浏览器中，右键单击“原始”  。
2. 单击“将链接另存为”  。
3. 将文件另存为 .csv 文件，保存到你的应用程序目录。

此时序数据的格式为 .csv 文件，它将被发送到异常检测器 API。

使用 Pandas 库的 `read_csv()` 方法加载数据文件，并构建一个空的列表变量来存储数据系列。 循环访问该文件，并将数据作为 [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) 对象追加。 此对象将包含 .csv 数据文件的行中的时间戳和数字值。 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

使用时序创建一个 [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) 对象，以及其数据点的[粒度](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python)（或周期）。 例如，`Granularity.daily` 。

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>在整个数据集中检测异常 

调用 API，以便使用客户端的 [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) 方法检测整个时序数据中的异常。 存储返回的 [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) 对象。 循环访问响应的 `is_anomaly` 列表，并输出任何 `true` 值的索引。 如果找到任何此类值，这些值对应于异常数据点的索引。

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测最新数据点的异常状态

调用异常检测器 API，以便使用客户端的 [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) 方法确定最新数据点是否异常，并存储返回的 [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) 对象。 响应的 `is_anomaly` 值是一个布尔值，用于指定该点的异常状态。  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>运行应用程序

使用 `python` 命令和文件名运行应用程序。
 
[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
