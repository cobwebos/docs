---
title: 快速入门：适用于 Ruby 的 QnA Maker 客户端库
description: 本快速入门介绍如何开始使用适用于 Ruby 的 QnA Maker 客户端库。
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: ef0db373dc6faaa470470b8169fdb6ae61aa8dde
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982734"
---
可以使用适用于 Ruby 的 QnA Maker 客户端库执行以下操作：

* 创建知识库
* 更新知识库
* 发布知识库
* 获取预测运行时终结点密钥
* 等待长时间运行的任务
* 下载知识库
* 获取答案
* 删除知识库

[库源代码（创作）](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated) | [库源代码（运行时）](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated) | [包（创作）](https://rubygems.org/gems/azure_cognitiveservices_qnamaker) | [包（运行时）](https://rubygems.org/gems/azure_cognitiveservices_qnamakerruntime) | [Ruby 示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/ruby/qnamaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [Ruby 2.x](https://www.ruby-lang.org/)
* 有了 Azure 订阅后，在 Azure 门户中创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，以获取创作密钥和终结点。 部署后，选择”转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到 QnA Maker API。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

### <a name="install-the-client-libraries"></a>安装客户端库

在安装 Ruby 后，可以通过以下命令安装客户端库：

```console
gem install azure_cognitiveservices_qnamaker
gem install azure_cognitiveservices_qnamakerruntime
```

### <a name="create-a-new-ruby-application"></a>创建新的 Ruby 应用程序

创建一个名为 `quickstart.rb` 的新文件，并导入以下库。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Dependencies":::

为资源的 Azure 终结点和密钥创建变量。

> [!IMPORTANT]
> 访问 Azure 门户，并找到之前在先决条件部分中创建的 QnA Maker 资源的密钥和终结点。 它们将位于资源的“密钥和终结点”页的“资源管理”下。
> 需要整个密钥才能创建知识库。 仅需要终结点中的资源名称。 格式为 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`。
> 完成后，请记住将密钥从代码中删除，并且永远不要公开发布该密钥。 对于生产环境，请考虑使用安全的方法来存储和访问凭据。 例如，[Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-overview)可提供安全的密钥存储。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Resourcevariables":::

## <a name="object-models"></a>对象模型

QnA Maker 使用两种不同的对象模型：
* **[QnAMakerClient](#qnamakerclient-object-model)** 对象可创建、管理、发布和下载知识库。
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 对象可通过 GenerateAnswer API 查询知识库，并使用训练 API 发送新的建议问题（作为[主动学习](../concepts/active-learning-suggestions.md)的一部分）。

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 对象模型

创作 QnA Maker 客户端是一个 [QnAMakerClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/qnamaker_client.rb) 对象，使用包含密钥的 MsRest::ServiceClientCredentials 向 Azure 进行身份验证。

创建客户端后，可使用客户端的 [Knowledgebases](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb) 属性方法创建、管理和发布知识库。

对于即时操作，方法通常返回结果（如果有）。 对于长时间运行的操作，响应是 [operation](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb) 对象。 使用 `operation.operation_id` 值调用 [operations.get_details](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/operations.rb#L33) 方法，确定[请求状态](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation_state_type.rb)。

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 对象模型

运行时 QnA Maker 客户端是一个 [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) 对象。

使用创作客户端发布知识库后，可使用运行时客户端的 [runtime.generate_answer](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/runtime.rb#L34) 方法从知识库获取答案。

调用 `generate_answer` 时，传递 `custom_headers` 可选参数的哈希值。 此哈希应包含密钥 `Authorization` 和值 `EndpointKey YOUR_ENDPOINT_KEY`。 对于 YOUR_ENDPOINT_KEY 的值，请使用创作客户端调用 [endpoint_keys.get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32)。

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>对用于创作知识库的客户端进行身份验证

使用创作终结点和订阅密钥实例化客户端。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationAuthor":::

## <a name="create-a-knowledge-base"></a>创建知识库

知识库为来自三个源的 [CreateKbDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/create_kb_dto.rb) 对象存储问答对：

* 对于**编辑内容**，请使用 [QnADTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adto.rb) 对象。
    * 若要使用元数据和跟进提示，请使用编辑上下文，因为此数据是在单独的 QnA 对级别添加的。
* 对于**文件**，请使用 [FileDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/file_dto.rb) 对象。 FileDTO 包括文件名以及用于访问该文件的公共 URL。
* 对于 URL，请使用一列字符串来表示公开可用的 URL。

调用 [create](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L554) 方法，然后将返回的操作的 `operation_id` 属性传递给 [operations.get_details](#get-status-of-an-operation) 方法以轮询状态。

以下代码的最后一行返回知识库 ID。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="CreateKBMethod":::

## <a name="update-a-knowledge-base"></a>更新知识库

可以更新知识库，方法是调用 [knowledgebase.update](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L455) 并传入知识库 ID 和 [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dto.rb) 对象。 而该对象可以包含：
- [add](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoadd.rb)
- [update](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoupdate.rb)
- [delete](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtodelete.rb)

将返回的操作的 `operation_id` 属性传递给 [operations.get_details](#get-status-of-an-operation) 方法以轮询状态。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="UpdateKBMethod":::

## <a name="download-a-knowledge-base"></a>下载知识库

使用 [knowledgebase.download](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L651) 方法，将知识库作为 [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adocuments_dto.rb) 列表下载。 这不等同于“设置”页中 QnA Maker 门户的导出，因为此方法的结果不是 TSV 文件。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DownloadKB":::

## <a name="publish-a-knowledge-base"></a>发布知识库

使用 [knowledgebase.publish](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L283) 方法发布知识库。 这样会通过知识库 ID 获取当前保存的已训练模型，并在某个终结点上将其发布。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="PublishKB":::

## <a name="get-query-runtime-key"></a>获取查询运行时密钥

发布知识库后，需要使用运行时终结点密钥来查询知识。 这与用于创建创作客户端的订阅密钥不同。

使用 [endpoint_keys.get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32) 方法获取 [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/endpoint_keys_dto.rb) 对象。

使用对象中返回的其中一个密钥属性来查询知识库。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GetQueryEndpointKey":::

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>对用于生成答案的运行时进行身份验证

创建 [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) 来查询知识库，以通过主动学习生成答案或训练。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationQuery":::

使用 QnAMakerRuntimeClient 基于知识来获取答案，或者将新的建议问题发送到知识库以进行[主动学习](../concepts/active-learning-suggestions.md)。

## <a name="generate-an-answer-from-the-knowledge-base"></a>从知识库生成答案

使用 RuntimeClient.runtime.generateAnswer 方法从已发布的知识库生成答案。 此方法接受知识库 ID 和 QueryDTO。 访问 QueryDTO 的其他属性，例如要在聊天机器人中使用的 Top 和 Context。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GenerateAnswer":::

这是查询知识库的简单示例。 若要了解高级查询方案，请参阅[其他查询示例](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)。

## <a name="delete-a-knowledge-base"></a>删除知识库

使用 [knowledgebase.delete](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L205) 方法与知识库 ID 参数可删除知识库。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DeleteKB":::

## <a name="get-status-of-an-operation"></a>获取操作的状态

某些方法（例如 create 和 update）可能需要很长的时间，系统不会等待此过程完成，而是返回一个 [operation](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb)。 使用操作中的操作 ID 进行轮询（使用重试逻辑），确定原始方法的状态。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="MonitorOperation":::

## <a name="run-the-application"></a>运行此应用程序

下面是应用程序的 main 方法。

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Main":::

在快速入门文件中使用 ruby 命令运行应用程序。

```console
ruby quickstart.rb
```

可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/ruby/qnamaker/sdk/quickstart.rb) 上找到此示例的源代码。
