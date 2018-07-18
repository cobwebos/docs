---
title: 异常情况检测 Java 应用 - Microsoft 认知服务 | Microsoft Docs
description: 介绍使用 Microsoft 认知服务中的异常情况检测 API 的 Java 应用。 将原始数据点发送到 API 并获得预期值和异常点。
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 228d440da358eba1322e2228c54f21e925e36ecd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597942"
---
# <a name="anomaly-detection-java-application"></a>异常情况检测 Java 应用程序

本文演示如何使用简单的 Java 应用程序来调用异常情况检测 API。  
该示例使用订阅密钥将时序数据提交到异常情况检测 API，然后从 API 获取每个数据点的所有异常点和预期值。

## <a name="prerequisites"></a>先决条件

### <a name="platform-requirements"></a>平台要求

本教程是采用 [IntelliJ IDEA](https://www.jetbrains.com/idea) 开发的。 此外，还需安装 1.8 版以上的 [Java 开发工具包 (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 以及最新的 [Apache Maven](http://maven.apache.org/) 生成工具。

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
