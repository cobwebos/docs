---
title: 快速入门：适用于 Go 的 QnA Maker 客户端库
description: 本快速入门介绍如何开始使用适用于 Go 的 QnA Maker 客户端库。
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: 621a0bbc6449c6deec9497d9f908edece8dba7f1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982789"
---
可以使用适用于 Go 的 QnA Maker 客户端库执行以下操作：

* 创建知识库
* 更新知识库
* 发布知识库
* 获取预测运行时终结点密钥
* 等待长时间运行的任务
* 下载知识库
* 获取答案
* 删除知识库

[参考（创作）](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker) | [参考（运行时）](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime) | [库源代码（创作）](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v4.0/qnamaker) | [库源代码（运行时）](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v4.0/qnamakerruntime) | [示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/QnAMaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [Go](https://golang.org/)
* 有了 Azure 订阅后，在 Azure 门户中创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以获取创作密钥和终结点。 部署后，选择”转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到 QnA Maker API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="create-a-new-go-application"></a>创建新的 Go 应用程序

创建一个名为 `kb_sample.go` 的新文件，并导入以下库。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="dependencies":::

为资源的 Azure 终结点和密钥创建变量。

> [!IMPORTANT]
> 访问 Azure 门户，并找到之前在先决条件部分中创建的 QnA Maker 资源的密钥和终结点。 它们将位于资源的“密钥和终结点”页的“资源管理”下。
> 需要整个密钥才能创建知识库。 仅需要终结点中的资源名称。 格式为 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`。
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-overview)可提供安全的密钥存储。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="environment":::

## <a name="object-models"></a>对象模型

QnA Maker 使用两种不同的对象模型：
* **[QnAMakerClient](#qnamakerclient-object-model)** 对象可创建、管理、发布和下载知识库。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 对象可通过 GenerateAnswer API 查询知识库，并使用训练 API 发送新的建议问题（作为[主动学习](../concepts/active-learning-suggestions.md)的一部分）。

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 对象模型

使用 [KnowledgebaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient) 创建、更新、发布、下载和删除知识库。 以下部分介绍了这些操作。

使用 [OperationsClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#OperationsClient) 检查长时间运行的操作（例如创建和更新知识库）的状态。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 对象模型

在发布知识库后，可使用 [RuntimeClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient) 查询知识库。 [查询知识库](#query-the-knowledge-base)中介绍了此操作。

## <a name="create-a-knowledge-base"></a>创建知识库

知识库为来自三个源的 [CreateKbDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#CreateKbDTO) 对象存储问答对：

* 对于**编辑内容**，请使用 [QnADTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#QnADTO) 对象。
    * 若要使用元数据和跟进提示，请使用编辑上下文，因为此数据是在单独的 QnA 对级别添加的。
* 对于**文件**，请使用 [FileDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#FileDTO) 对象。 FileDTO 包括文件名以及用于访问该文件的公共 URL。
* 对于 URL，请使用一列字符串来表示公开可用的 URL。

调用 [create](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Create) 方法，然后将返回的操作的 `operationId` 属性传递给 [getDetails](#get-status-of-an-operation) 方法以轮询状态。

以下代码的最后一行返回知识库 ID。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="create_kb":::

## <a name="update-a-knowledge-base"></a>更新知识库

可以更新知识库，方法是调用 [update](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Update) 并传入知识库 ID 和 [UpdateKbOperationDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTO) 对象。 而该对象可以包含：
- [add](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTOAdd)
- [update](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTOUpdate)
- [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#UpdateKbOperationDTODelete)

将返回的操作的 `operationId` 属性传递给 [getDetails](#get-status-of-an-operation) 方法以轮询状态。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="update_kb":::

## <a name="download-a-knowledge-base"></a>下载知识库

使用 [download](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Download) 方法，将知识库作为 [QnADocumentsDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#QnADocumentsDTO) 列表下载。 这不等同于“设置”页中 QnA Maker 门户的导出，因为此方法的结果不是 TSV 文件。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="download_kb":::

## <a name="publish-a-knowledge-base"></a>发布知识库

使用 [publish](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Publish) 方法发布知识库。 这样会通过知识库 ID 获取当前保存的已训练模型，并在某个终结点上将其发布。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="publish_kb":::

## <a name="query-the-knowledge-base"></a>查询知识库

发布知识库后，需要使用运行时终结点密钥来查询知识。 这与用于创建创作客户端的订阅密钥不同。

创建 [EndpointKeysClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysClient) 并使用 [getKeys](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysClient.GetKeys) 方法获取 [EndpointKeysDTO](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#EndpointKeysDTO) 对象。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="get_runtime_endpoint_key":::

创建 [RuntimeClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient) 并调用 [GenerateAnswer](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamakerruntime#RuntimeClient.GenerateAnswer) 方法来查询知识库。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="query_kb":::

这是查询知识库的简单示例。 若要了解高级查询方案，请参阅[其他查询示例](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)。

## <a name="delete-a-knowledge-base"></a>删除知识库

使用 [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#KnowledgebaseClient.Delete) 方法与知识库 ID 参数删除知识库。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="delete_kb":::

## <a name="get-status-of-an-operation"></a>获取操作的状态

某些方法（例如 create 和 update）可能需要很长的时间，系统不会等待此过程完成，而是返回一个 [operation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v4.0/qnamaker#Operation)。 使用操作中的操作 ID 进行轮询（使用重试逻辑），确定原始方法的状态。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="wait_for_operation":::

## <a name="handle-errors"></a>处理错误

以下代码演示如何处理 SDK 的函数可能返回的错误。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="error_handling":::

## <a name="run-the-application"></a>运行此应用程序

下面是应用程序的 main 方法。

:::code language="go" source="~/cognitive-services-quickstart-code/go/qnamaker/sdk/kb_sample.go" id="main":::

在快速入门文件中使用 go 命令运行应用程序。

```console
go run kb_sample.go
```

可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/qnamaker/sdk/kb_sample.go) 上找到此示例的源代码
