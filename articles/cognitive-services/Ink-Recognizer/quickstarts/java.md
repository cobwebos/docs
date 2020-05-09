---
title: 快速入门：使用墨迹识别器 REST API 和 Java 识别数字墨迹
titleSuffix: Azure Cognitive Services
description: 在本快速入门中使用墨迹识别器 API 来识别数字墨迹笔划。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: d2cd4e56477ea39587ce318538c9ddd84c51b03b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75448126"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>快速入门：使用墨迹识别器 REST API 和 Java 识别数字墨迹

根据本快速入门的说明，开始对数字墨迹笔划使用墨迹识别器 API。 本 Java 应用程序发送包含 JSON 格式墨迹笔划数据的 API 请求，并获取响应。

虽然此应用程序是使用 Java 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

通常会从数字墨迹应用调用此 API。 本快速入门为 JSON 文件中的以下手写示例发送墨迹笔划数据。

![手写文本图像](../media/handwriting-sample.jpg)

可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904) 上找到此快速入门的源代码。

## <a name="prerequisites"></a>先决条件

- [Java&trade; 开发工具包 (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 或更高版本。

- 将这些库从 Maven 存储库导入
    - [以 Java 编写的 JSON](https://mvnrepository.com/artifact/org.json/json) 包
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) 包

- 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json) 上找到此快速入门的示例墨迹笔划数据。

### <a name="create-an-ink-recognizer-resource"></a>创建“墨迹识别器”资源

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>创建新应用程序

1. 在你最喜欢的 IDE 或编辑器中新建一个 Java 项目，并导入以下库。
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. 为订阅密钥、终结点和 JSON 文件创建变量。 稍后会将终结点追加到墨迹识别器 URI。

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>创建用于发送请求的函数

1. 创建名为 `sendRequest()` 的新函数，该函数使用上面创建的变量。 然后，执行以下步骤。

2. 创建可以向 API 发送请求的 `CloseableHttpClient` 对象。 通过组合使用终结点和墨迹识别器 URL，将请求发送给 `HttpPut` 请求对象。

3. 使用请求的 `setHeader()` 函数将 `Content-Type` 标头设置为 `application/json`，然后将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头。

4. 使用请求的 `setEntity()` 函数来设置要发送的数据。   

5. 使用客户端的 `execute()` 函数来发送请求，并将其保存到 `CloseableHttpResponse` 对象。 

6. 创建 `HttpEntity` 对象，用于存储响应内容。 使用 `getEntity()` 获取内容。 如果响应不为空，请将其返回。
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>发送墨迹识别请求

创建名为 `recognizeInk()` 的方法，用于识别墨迹笔划数据。 调用在上面使用终结点、URL、订阅密钥和 JSON 数据创建的 `sendRequest()` 方法。 获取结果，并将其输出到控制台。

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>加载数字墨迹数据并发送请求

1. 在应用程序的 main 方法中读入 JSON 文件，其中包含将要添加到请求中的数据。

2. 调用上面创建的墨迹识别函数。
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>运行应用程序并查看响应

运行应用程序。 成功的响应以 JSON 格式返回。 也可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json) 上找到 JSON 响应。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://go.microsoft.com/fwlink/?linkid=2089907)


若要了解墨迹识别 API 在数字墨迹应用中的工作原理，请查看 GitHub 上的以下示例应用程序：
* [C# 和通用 Windows 平台 (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 和 Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript Web 浏览器应用](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 和 Android 移动应用](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 和 iOS 移动应用](https://go.microsoft.com/fwlink/?linkid=2089805)
