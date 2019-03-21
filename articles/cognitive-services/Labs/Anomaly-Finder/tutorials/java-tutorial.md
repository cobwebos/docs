---
title: 教程：异常检测，Java
titlesuffix: Azure Cognitive Services
description: 探索使用异常情况检测 API 的 Java 应用。 将原始数据点发送到 API 并获得预期值和异常点。
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: ba66c96f9129b253fc5897c3a2eaaefb53056cea
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548722"
---
# <a name="tutorial-anomaly-detection-with-java-application"></a>教程：使用 Java 应用程序进行异常情况检测

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

本文演示如何使用简单的 Java 应用程序来调用异常情况检测 API。  
该示例使用订阅密钥将时序数据提交到异常情况检测 API，然后从 API 获取每个数据点的所有异常点和预期值。

## <a name="prerequisites"></a>先决条件

### <a name="platform-requirements"></a>平台要求

本教程是采用 [IntelliJ IDEA](https://www.jetbrains.com/idea) 开发的。 此外，还需安装 1.8 版以上的 [Java 开发工具包 (JDK)](https://aka.ms/azure-jdks) 以及最新的 [Apache Maven](https://maven.apache.org/) 生成工具。

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>订阅异常情况检测并获取订阅密钥 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>下载教程项目

1. 转到 MicrosoftAnomalyDetection [Java 存储库](https://github.com/MicrosoftAnomalyDetection/java-sample)。
2. 单击“克隆”或“下载”按钮。
3. 单击“下载 ZIP”，下载教程项目的 .zip 文件。

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>打开教程项目

1. 将教程项目的 .zip 文件解压缩。
2. 在 IntelliJ IDEA 中，单击“文件”>“打开”，随即会出现“打开文件或项目”对话框。
3. 选择解压缩项目的根路径，然后单击“确定”。
4. 在“项目”面板中，依次展开“src”>“main”>“java”。
5. 双击 com.microsoft.cognitiveservice.anomalydetection.Main.java 将文件加载到编辑器中。

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>替换 subscriptionKey 和 URI 区域

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>生成并运行教程项目

1. 右键单击 com.microsoft.cognitiveservice.anomalydetection.Main.java 源代码选项卡中的任意位置，打开菜单。 
2. 选择“运行 Main.main()”
3. 随后会返回示例请求的结果并显示在终端中。

### <a name="result-of-the-tutorial-project"></a>教程项目的结果

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
