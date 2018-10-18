---
title: 快速入门：创建知识库 - REST、C# - QnA Maker
titlesuffix: Azure Cognitive Services
description: 本快速入门详细介绍如何以编程方式创建一个示例 QnA Maker 知识库，该知识库会显示在认知服务 API 帐户的 Azure 仪表板中。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: diberry
ms.openlocfilehash: e6b8c769082b688b07bac78bca5e2dca59a2d9c2
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389405"
---
# <a name="quickstart-create-a-qna-maker-knowledge-base-in-c"></a>快速入门：通过 C# 创建 QnA Maker 知识库

本快速入门将指导你完成以编程方式创建示例 QnA Maker 知识库的过程。 QnA Maker 自动从[数据源](../Concepts/data-sources-supported.md)中从半结构化内容（例如常见问题解答）中自动提取问题和解答。 用于知识库的模型是在 API 请求的正文中发送的 JSON 中定义的。 

本快速入门调用了 QnA Maker API：
* [创建知识库](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [获取操作详细信息](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>先决条件

* 最新的 [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)。
* 必须已有一个 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥，请在仪表板的“资源管理”下选择“密钥”。 

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-csharp-repo-note.md)]

## <a name="create-a-knowledge-base-project"></a>创建知识库项目

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-dependencies.md)]  

## <a name="add-the-required-constants"></a>添加必需的常量

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-constants.md)] 

## <a name="add-the-kb-definition"></a>添加知识库定义

在常量后，添加以下知识库定义：

```csharp
static string kb = @"
{
  'name': 'QnA Maker FAQ from quickstart',
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your knowledge base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my knowledge base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ],
  'urls': [
    'https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs',
    'https://docs.microsoft.com/bot-framework/resources-bot-framework-faq'
  ],
  'files': []
}
";
```

## <a name="add-supporting-functions-and-structures"></a>添加支持函数和结构

[!INCLUDE [Add supporting functions and structures](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-support-functions.md)] 

## <a name="add-a-post-request-to-create-kb"></a>添加一个 POST 请求来创建知识库

以下代码向 QnA Maker API 发出 HTTPS 请求来创建知识库并接收响应：

```csharp
async static Task<Response> PostCreateKB(string kb)
{
    // Builds the HTTP request URI.
    string uri = host + service + method;

    // Writes the HTTP request URI to the console, for display purposes.
    Console.WriteLine("Calling " + uri + ".");

    // Asynchronously invokes the Post(string, string) method, using the
    // HTTP request URI and the specified data source.
    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Content = new StringContent(kb, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

此 API 调用将返回包括操作 ID 的 JSON 响应。 使用操作 ID 来确定知识库是否已成功创建。 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>添加 GET 请求来确定创建状态

检查操作的状态。

```csharp
async static Task<Response> GetStatus(string operationID)
{
    // Builds the HTTP request URI.
    string uri = host + service + operationID;

    // Writes the HTTP request URI to the console, for display purposes.
    Console.WriteLine("Calling " + uri + ".");

    // Asynchronously invokes the Get(string) method, using the
    // HTTP request URI.
    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Get;
        request.RequestUri = new Uri(uri);
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

此 API 调用返回包含操作状态的 JSON 响应： 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

重复调用，直到成功或失败： 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>添加 CreateKB 方法

以下方法创建知识库并重复检查状态。 因为知识库创建可能要花费一些时间，所以你需要重复用来检查状态的调用，直到状态为成功或失败。

```csharp
async static void CreateKB()
{
    try
    {
        // Starts the QnA Maker operation to create the knowledge base.
        var response = await PostCreateKB(kb);

        // Retrieves the operation ID, so the operation's status can be
        // checked periodically.
        var operation = response.headers.GetValues("Location").First();

        // Displays the JSON in the HTTP response returned by the 
        // PostCreateKB(string) method.
        Console.WriteLine(PrettyPrint(response.response));

        // Iteratively gets the state of the operation creating the
        // knowledge base. Once the operation state is set to something other
        // than "Running" or "NotStarted", the loop ends.
        var done = false;
        while (true != done)
        {
            // Gets the status of the operation.
            response = await GetStatus(operation);

            // Displays the JSON in the HTTP response returned by the
            // GetStatus(string) method.
            Console.WriteLine(PrettyPrint(response.response));

            // Deserialize the JSON into key-value pairs, to retrieve the
            // state of the operation.
            var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

            // Gets and checks the state of the operation.
            String state = fields["operationState"];
            if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
            {
                // QnA Maker is still creating the knowledge base. The thread is 
                // paused for a number of seconds equal to the Retry-After header value,
                // and then the loop continues.
                var wait = response.headers.GetValues("Retry-After").First();
                Console.WriteLine("Waiting " + wait + " seconds...");
                Thread.Sleep(Int32.Parse(wait) * 1000);
            }
            else
            {
                // QnA Maker has completed creating the knowledge base. 
                done = true;
            }
        }
    }
    catch
    {
        // An error occurred while creating the knowledge base. Ensure that
        // you included your QnA Maker subscription key where directed in the sample.
        Console.WriteLine("An error occurred while creating the knowledge base.");
    }
    finally
    {
        Console.WriteLine("Press any key to continue.");
    }

}
``` 

## <a name="add-the-createkb-method-to-main"></a>将 CreateKB 方法添加到 Main

更改 Main 方法来调用 CreateKB 方法：

```csharp
static void Main(string[] args)
{
    // Call the CreateKB() method to create a knowledge base, periodically 
    // checking the status of the QnA Maker operation until the 
    // knowledge base is created.
    CreateKB();

    // The console waits for a key to be pressed before closing.
    Console.ReadLine();
}
```

## <a name="build-and-run-the-program"></a>生成并运行程序

生成并运行程序。 它将自动向 QnA Maker API 发送请求来创建知识库，然后它每隔 30 秒轮询一次结果。 每个响应都将输出到控制台窗口中。

创建知识库以后，即可在 QnA Maker 门户 - [My knowledge bases](https://www.qnamaker.ai/Home/MyServices)（我的知识库）页中查看它。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
