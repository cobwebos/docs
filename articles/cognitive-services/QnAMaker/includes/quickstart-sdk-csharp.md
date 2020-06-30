---
title: 快速入门：适用于 .NET 的 QnA Maker 客户端库
description: 本快速入门介绍如何开始使用适用于 .NET 的 QnA Maker 客户端库。 请按照以下步骤安装程序包并试用基本任务的示例代码。  使用 QnA Maker，可以根据常见问题解答文档、URL 和产品手册等半结构化内容打造一项问题与解答服务。
ms.topic: quickstart
ms.date: 06/11/2020
ms.openlocfilehash: e487783e506d16006231b07b9a86f93864f51903
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2020
ms.locfileid: "84765127"
---
可以使用适用于 .NET 的 QnA Maker 客户端库执行以下操作：

 * 创建知识库
 * 更新知识库
 * 发布知识库，等待发布完成
 * 获取预测运行时终结点密钥
 * 下载知识库
 * 删除知识库

[参考文档](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C# 示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或最新版本的 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 有了 Azure 订阅后，在 Azure 门户中创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以获取创作密钥和终结点。 部署后，选择”转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到 QnA Maker API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置


#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

使用 Visual Studio 创建一个 .NET Core 应用程序并安装客户端库，方法是右键单击“解决方案资源管理器”中的解决方案，然后选择“管理 NuGet 包” 。 在打开的包管理器中，选择“浏览”，选中“包括预发行版”并搜索 `(package-name)`。 选择版本 `(version)`，然后选择“安装”。

#### <a name="cli"></a>[CLI](#tab/cli)

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，使用 `dotnet new` 命令创建名为 `qna-maker-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：*program.cs*。

```console
dotnet new console -n qna-maker-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

在应用程序目录中，使用以下命令安装适用于 .NET 的 QnA Maker 客户端库：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.1.0
```


---

> [!TIP]
> 想要立即查看整个快速入门代码文件？ 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs) 上找到它，其中包含此快速入门中的代码示例。

从项目目录中，打开 Program.cs 文件，并添加以下 `using` 指令：

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

在应用程序的 `Main` 方法中，添加变量和代码（如以下部分所示），以使用本快速入门中的常见任务。

## <a name="object-models"></a>对象模型

QnA Maker 使用两种不同的对象模型：
* **[QnAMakerClient](#qnamakerclient-object-model)** 对象可创建、管理、发布和下载知识库。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 对象可通过 GenerateAnswer API 查询知识库，并使用训练 API 发送新的建议问题（作为[主动学习](../concepts/active-learning-suggestions.md)的一部分）。


### <a name="qnamakerclient-object-model"></a>QnAMakerClient 对象模型

创作 QnA Maker 客户端是 [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) 对象，使用包含密钥的 Microsoft.Rest.ServiceClientCredentials 向 Azure 进行身份验证。

创建客户端以后，使用[知识库](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase)属性创建、管理和发布知识库。

通过发送 JSON 对象来管理知识库。 对于即时操作，方法通常返回一个指示状态的 JSON 对象。 对于长时间运行的操作，响应是操作 ID。 使用操作 ID 调用 [client.Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) 方法，确定[请求状态](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 对象模型

预测 QnA Maker 客户端是一个 [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) 对象，它使用 Microsoft.Rest.ServiceClientCredentials 向 Azure 进行身份验证；Microsoft.Rest.ServiceClientCredentials 包含预测运行时密钥，发布知识库后进行创作客户端调用 `client.EndpointKeys.GetKeys` 可返回该密钥。

## <a name="code-examples"></a>代码示例

这些代码片段展示如何使用适用于 .NET 的 QnA Maker 客户端库执行以下操作：

* [对创作客户端进行身份验证](#authenticate-the-client-for-authoring-the-knowledge-base)
* [创建知识库](#create-a-knowledge-base)
* [更新知识库](#update-a-knowledge-base)
* [下载知识库](#download-a-knowledge-base)
* [发布知识库](#publish-a-knowledge-base)
* [删除知识库](#delete-a-knowledge-base)
* [获取查询运行时密钥](#get-query-runtime-key)
* [获取操作的状态](#get-status-of-an-operation)
* [对查询运行时客户端进行身份验证](#authenticate-the-runtime-for-generating-an-answer)
* [从知识库生成答案](#generate-an-answer-from-the-knowledge-base)

## <a name="using-this-example-knowledge-base"></a>使用此示例知识库

本快速入门中的知识库由两个对话 QnA 对开始（这样做一是为了简化示例，二是为了有高度可预测的 ID 在可 Update 方法中使用），将包含问题的跟进提示与新对相关联。 为本快速入门计划并按特定顺序实现此操作。

如果计划随时间推移改进知识库，扩展依赖于现有 QnA 对的跟进提示，可选择执行以下操作：
* 对于较大的知识库，请在支持自动化的文本编辑器或 TSV 工具中管理知识库，并在有更新时立即彻底替换该知识库。
* 对于较小的知识库，请仅在 QnA Maker 门户中管理跟进提示。

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>对用于创作知识库的客户端进行身份验证

在 main 方法中，为资源的 Azure 密钥和资源名称创建一个变量。 创作和预测 URL 均使用资源名称作为子域。

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


> [!IMPORTANT]
> 访问 Azure 门户，并查找在满足先决条件的过程中创建的 QnA Maker 资源的密钥和终结点。 它们将位于资源的“密钥和终结点”页的“资源管理”下。
> 需要整个密钥才能创建知识库。 仅需要来自终结点的资源名称。 其格式为 ``。
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-overview)可提供安全的密钥存储。

接下来，使用密钥创建 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) 对象，并将其与终结点一起创建 [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) 对象。

|可变|示例|
|--|--|
|`authoringKey`|密钥是一个 32 字符的字符串，可在 Azure 门户的 QnA Maker 资源的“密钥和终结点”页上找到。 这与预测运行时密钥不同。|
|`resourceName`| 以 `https://{resourceName}.cognitiveservices.azure.com` 格式使用资源名称。 这与用于查询预测运行时的 URL 不同。|
||||

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>创建知识库

知识库为来自三个源的 [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) 对象存储问答对：

* 对于**编辑内容**，请使用 [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) 对象。
    * 若要使用元数据和跟进提示，请使用编辑上下文，因为此数据是在单独的 QnA 对级别添加的。
* 对于**文件**，请使用 [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) 对象。
* 对于 **URL**，请使用字符串列表。

调用 [CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) 方法，然后将返回的操作 ID 传递给 [MonitorOperation](#get-status-of-an-operation) 方法以轮询状态。

以下代码的最后一行从来自 MonitorOperation 的响应返回知识库 ID。

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=37-38)]

请确保包括上述代码中引用的 [`MonitorOperation`](#get-status-of-an-operation) 函数，以便成功创建知识库。

## <a name="update-a-knowledge-base"></a>更新知识库

可以更新知识库，方法是：将知识库 ID 和包含 [add](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet)、[update](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) 和 [delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO 对象的 [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) 传递给 [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) 方法。 使用 [MonitorOperation](#get-status-of-an-operation) 方法来确定更新是否成功。

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

请确保包括上述代码中引用的 [`MonitorOperation`](#get-status-of-an-operation) 函数，以便成功更新知识库。

## <a name="download-a-knowledge-base"></a>下载知识库

使用 [DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) 方法，将知识库作为 [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet) 列表下载。 这不等同于“设置”页中 QnA Maker 门户的导出，因为此方法的结果不是 TSV 文件。

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3,4)]

## <a name="publish-a-knowledge-base"></a>发布知识库

使用 [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) 方法发布知识库。 这样会通过知识库 ID 获取当前保存的已训练模型，并在某个终结点上将其发布。

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]

## <a name="get-query-runtime-key"></a>获取查询运行时密钥

发布知识库后，需要查询运行时密钥来查询运行时。 此密钥并非用于创建原始客户端对象的密钥。

使用 [EndpointKeys](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) 方法获取 [EndpointKeysDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet) 类。

使用对象中返回的其中一个密钥属性来查询知识库。

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>对用于生成答案的运行时进行身份验证

创建 [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) 来查询知识库，以通过主动学习生成答案或训练。

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

使用 QnAMakerRuntimeClient 从知识中获取答案，或者将新的建议问题发送到知识库以实现[主动学习](../concepts/active-learning-suggestions.md)。

## <a name="generate-an-answer-from-the-knowledge-base"></a>从知识库生成答案

使用 [RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) 方法从已发布的知识库生成答案。 此方法接受知识库 ID 和 [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet)。 访问 QueryDTO 的其他属性，例如要在聊天机器人中使用的 [Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) 和 [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet)。

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]

这是查询知识库的简单示例。 若要了解高级查询方案，请参阅[其他查询示例](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)。

## <a name="delete-a-knowledge-base"></a>删除知识库

使用 [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) 方法与知识库 ID 参数删除知识库。

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>获取操作的状态

某些方法（例如 create 和 update）可能需要很长的时间，系统不会等待此过程完成，而是返回一个 [operation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet)。 使用操作中的[操作 ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) 进行轮询（使用重试逻辑），确定原始方法的状态。

以下代码块中的 _loop_ 和 _Task.Delay_ 用于模拟重试逻辑。 应将其替换为你自己的重试逻辑。

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `dotnet run` 命令运行应用程序。

本文中的所有代码片段均[可用](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)，并且可以作为单个文件运行。

```dotnetcli
dotnet run
```

* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart) 上找到此示例的源代码。