---
title: 快速入门：使用墨迹识别器 REST API 和 C# 识别数字墨迹
titleSuffix: Azure Cognitive Services
description: 本快速入门说明如何使用墨迹识别器 API 开始识别数字墨迹笔划。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: c5379452449188f17b75036eb09c3ca15bae0c2e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448166"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>快速入门：使用墨迹识别器 REST API 和 C# 识别数字墨迹

根据本快速入门的说明，开始将数字墨迹笔划发送到墨迹识别器 API。 本 C# 应用程序发送包含 JSON 格式墨迹笔划数据的 API 请求，并获取响应。

虽然此应用程序是使用 C# 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

通常会从数字墨迹应用调用此 API。 本快速入门为 JSON 文件中的以下手写示例发送墨迹笔划数据。

![手写文本图像](../media/handwriting-sample.jpg)

可以在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502) 上找到此快速入门的源代码。

## <a name="prerequisites"></a>必备条件

- 任何版本的 [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)。
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - 若要在 Visual Studio 中以 NuGet 包的形式安装 Newtonsoft.Json，请执行以下操作：
        1. 右键单击“解决方案管理器” 
        2. 单击“管理 NuGet 包”。 
        3. 搜索 `Newtonsoft.Json` 并安装该包
- 如果使用的是 Linux/MacOS，则可使用 [Mono](https://www.mono-project.com/) 运行此应用程序。

- 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json) 上找到此快速入门的示例墨迹笔划数据。

### <a name="create-an-ink-recognizer-resource"></a>创建“墨迹识别器”资源

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>创建新应用程序

1. 在 Visual Studio 中，创建新的控制台解决方案并添加以下包。 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. 为订阅密钥和终结点以及示例 JSON 文件创建变量。 稍后，终结点将与 `inkRecognitionUrl` 结合使用来访问 API。 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>创建用于发送请求的函数

1. 创建名为 `Request` 的新异步函数，该函数使用上面创建的变量。

2. 使用 `HttpClient` 对象设置客户端的安全协议和标头信息。 请务必将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头中。 然后，为请求创建 `StringContent` 对象。
 
3. 通过 `PutAsync()` 发送请求。 如果请求成功，则返回响应。  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>发送墨迹识别请求

1. 创建名为 `recognizeInk()` 的新函数。 通过终结点、订阅密钥、API 的 URL 以及数字墨迹笔划数据调用 `Request()` 函数，构造请求并将其发送。

2. 反序列化 JSON 对象，并将其写入控制台。 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>加载数字墨迹数据

创建一个名为 `LoadJson()` 的函数，以便加载墨迹数据 JSON 文件。 使用 `StreamReader` 和 `JsonTextReader` 创建 `JObject` 并将其返回。

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>发送 API 请求

1. 在应用程序的 main 方法中，使用上面创建的函数加载 JSON 数据。 

2. 调用上面创建的 `recognizeInk()` 函数。 使用 `System.Console.ReadKey()`，在运行应用程序后让控制台窗口保持打开状态。
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>运行应用程序并查看响应

运行应用程序。 成功的响应以 JSON 格式返回。 也可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json) 上找到 JSON 响应。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [REST API 参考](https://go.microsoft.com/fwlink/?linkid=2089907)


若要了解墨迹识别 API 在数字墨迹应用中的工作原理，请查看 GitHub 上的以下示例应用程序：
* [C# 和通用 Windows 平台 (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 和 Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript Web 浏览器应用](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 和 Android 移动应用](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 和 iOS 移动应用](https://go.microsoft.com/fwlink/?linkid=2089805)
