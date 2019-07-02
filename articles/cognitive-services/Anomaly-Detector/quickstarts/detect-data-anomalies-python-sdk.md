---
title: 快速入门：检测数据异常使用异常情况检测程序库和 Python |Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 使用异常检测器 API 来检测异常数据系列中，作为一个批或针对流式处理数据。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: 1d89ed8f40547142d41af9c587fc8fc000fa4dd9
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503671"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>快速入门：用于 Python 的异常情况检测程序客户端库

开始使用异常情况检测程序客户端库适用于.NET。 按照以下步骤来安装包和试用基本任务的示例代码。 异常情况检测程序服务，可通过使用自动最佳配适模型，而不考虑行业，方案中或数据卷在时序数据中查找异常情况。

## <a name="key-concepts"></a>关键概念

使用适用于 Python 到异常情况检测程序客户端库：

* 检测异常在整个时间系列数据集，为批处理请求
* 检测时间序列中的最新的数据点的异常状态

[库的参考文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [包 (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [示例](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>必备组件

* Azure 订阅-[免费创建一个](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* [Pandas 数据分析库](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>设置

### <a name="create-an-anomaly-detector-resource"></a>创建异常情况检测程序资源

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="install-the-client-library"></a>安装客户端库

安装 Python 之后, 可以安装客户端库与：

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>对象模型

异常情况检测程序客户端[AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python)向 Azure 中使用你的密钥进行身份验证的对象。 客户端提供异常情况检测的两种的方法：在整个数据集使用[entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)，以及最新数据点使用[Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)。 

作为一系列的发送时间序列数据[点](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python)中[请求](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python)对象。 `Request`对象包含要描述的数据属性 ([粒度](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python)例如)，和异常情况检测的参数。 

异常情况检测程序响应[LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python)或[EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python)具体取决于所使用的方法的对象。 

## <a name="getting-started"></a>入门

在偏好的编辑器或 IDE 中创建新的 Python 应用程序。 然后将以下导入声明添加到你的文件。 

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

> [!NOTE]
> 本快速入门假定你已[创建一个环境变量](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)你异常情况检测程序的密钥，名为`ANOMALY_DETECTOR_KEY`。

为你的密钥作为环境变量，时间系列数据文件和你的订阅的 azure 位置的路径创建变量。 例如，`westus2` 。 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

## <a name="code-examples"></a>代码示例 

这些代码片段演示如何执行以下操作的异常情况检测程序客户端库适用于.NET:

* [客户端身份验证](#authenticate-the-client)
* [从文件加载时间系列数据集](#load-time-series-data-from-a-file)
* [在整个数据集中检测异常](#detect-anomalies-in-the-entire-data-set) 
* [检测到异常情况状态的最新的数据点](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="authenticate-the-client"></a>客户端身份验证

将你的 azure 位置的变量添加到终结点，并使用你的密钥进行身份验证客户端。

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>从文件加载时间序列数据

下载示例数据从本快速入门教程[GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. 在浏览器中，右键单击**Raw**
2. 单击**作为保存链接**
3. 为应用程序目录，将文件另存的.csv 文件。

此时间序列数据的格式为.csv 文件，并且将发送到异常情况检测器 API。

加载包含 Pandas 库的数据文件的`read_csv()`方法，并生成一个空列表变量来存储数据系列。 循环访问该文件，以及作为将数据追加[点](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python)对象。 此对象将包含时间戳和从数据的.csv 文件中的行的数字值。 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

创建[请求](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python)具有你时序对象和[粒度](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python)（或周期） 及其数据点。 例如，`Granularity.daily` 。

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>在整个数据集中检测异常 

调用 API，以检测通过使用客户端的整个时序数据异常[entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)方法。 存储返回[EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python)对象。 循环访问的响应`is_anomaly`列表，并打印的任何索引`true`值。 如果任何发现，这些值对应于异常的数据点的索引。

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>检测到异常情况状态的最新的数据点

调用以确定最新的数据点是否使用客户端的异常，异常情况检测器 API [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-)方法，并返回应用商店[LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python)对象。 响应的`is_anomaly`值是一个布尔值，指定该点的异常状态。  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>运行应用程序

在 IDE 中，或使用命令行中运行应用程序`python`命令和文件名称。
 
## <a name="clean-up-resources"></a>清理资源

如果你想要清理和删除认知服务订阅，则可以删除资源或资源组。 删除资源组也会删除与资源组相关联的任何其他资源。

* [门户](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

此外可以运行以下 cloud shell 命令删除资源组及其关联的资源。 这可能需要几分钟才能完成。 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[使用 Azure Databricks 的流式处理异常情况检测](../tutorials/anomaly-detection-streaming-databricks.md)

* 什么是[异常检测器 API？](../overview.md)
* [最佳做法](../concepts/anomaly-detection-best-practices.md)时使用异常检测器 API。
* 可以在 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs) 上找到此示例的源代码。
