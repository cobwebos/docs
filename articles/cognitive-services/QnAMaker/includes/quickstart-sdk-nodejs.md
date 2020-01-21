---
title: 快速入门：适用于 Node.js 的 QnA Maker 客户端库
description: 本快速入门介绍如何开始使用适用于 Node.js 的 QnA Maker 客户端库。
ms.topic: quickstart
ms.date: 01/13/2020
ms.openlocfilehash: 05e6d2c77a351c22c73f0bdb54daeaf0e03f4a9d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021091"
---
可以使用适用于 Node.js 的 QnA Maker 客户端库执行以下操作：

* 创建知识库
* 更新知识库
* 发布知识库
* 获取已发布的终结点密钥
* 等待长时间运行的任务
* 删除知识库

[参考文档](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/?view=azure-node-latest) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-qnamaker) | [包 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js 示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>必备条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Node.js](https://nodejs.org)。

## <a name="setting-up"></a>设置

### <a name="create-a-qna-maker-azure-resource"></a>创建 QnA Maker Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本地计算机上创建用于 QnA Maker 的资源。

从资源获取密钥和终结点后，请在 Azure 门户的“快速入门”页上获取新资源的值。

[创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) `QNAMAKER_AUTHORING_KEY` 和 `QNAMAKER_ENDPOINT`。 可以将 [.env.sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/.env.sample) 文件复制到 `.env`，并使用该文件中的环境变量。

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。

```console
mkdir qnamaker_quickstart && cd qnamaker_quickstart
```

运行 `npm init -y` 命令以使用 `package.json` 文件创建一个 node 应用程序。

```console
npm init -y
```

### <a name="install-the-client-library"></a>安装客户端库

安装必需的和可选的 NPM 包：

```console
npm install @azure/cognitiveservices-qnamaker @azure/ms-rest-js dotenv
```

应用的 `package.json` 文件将使用依赖项进行更新。 `dotenv` 为可选，用于设置文本文件中的环境变量。 不要将 `.env` 签入到源代码管理中。


## <a name="object-model"></a>对象模型

QnA Maker 客户端是 [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) 对象，使用包含密钥的 ServiceClientCredentials 向 Azure 进行身份验证。

创建客户端以后，使用[知识库](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase)属性创建、管理和发布知识库。

通过发送 JSON 对象来管理知识库。 对于即时操作，方法通常返回一个指示状态的 JSON 对象。 对于长时间运行的操作，响应是操作 ID。 使用操作 ID 调用 [client.Operations.getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) 方法，确定[请求状态](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest)。


## <a name="code-examples"></a>代码示例

这些代码片段展示如何使用适用于 Node.js 的 QnA Maker 客户端库执行以下操作：

* [创建知识库](#create-a-knowledge-base)
* [更新知识库](#update-a-knowledge-base)
* [发布知识库](#publish-a-knowledge-base)
* [删除知识库](#delete-a-knowledge-base)
* [获取发布的终结点](#get-published-endpoint)
* [获取操作的状态](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>添加依赖项

创建名为 `index.js` 的文件。 将 QnA Maker 库和依赖项添加到文件。

[!code-javascript[Require statements](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=dependencies)]

为资源的 Azure 终结点和密钥创建变量。 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

|环境变量|Node.js 变量|示例|
|--|--|--|
|`QNAMAKER_AUTHORING_KEY`|`authoring_key`|密钥是一个 32 字符的字符串，可以在 Azure 门户的 QnA Maker 资源的“快速入门”页上找到。 这与预测终结点密钥不同。|
|`QNAMAKER_ENDPOINT`|`endpoint`| 创作终结点的格式为 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`，其中包括 **资源名称**。 这与用于查询预测终结点的 URL 不同。|
||||

[!code-javascript[Azure resource variables](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=resourceSettings)]

## <a name="authenticate-the-client"></a>验证客户端

接下来，使用密钥创建 ApiKeyCredentials 对象，并将其与终结点一起使用以创建 [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) 对象。 使用客户端对象获取[知识库客户端](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest)对象。


[!code-javascript[Authorization to resource key](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>创建知识库

知识库为来自三个源的 [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) 对象存储问答对：

* 对于**编辑内容**，请使用 [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) 对象。
* 对于**文件**，请使用 [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) 对象。
* 对于 **URL**，请使用字符串列表。

使用知识库信息调用 [create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) 方法。 知识库信息基本上是一个 JSON 对象。

当 create 方法返回时，请将返回的操作 ID 传递到 [wait_for_operation](#get-status-of-an-operation) 方法，以轮询状态。 操作完成时，wait_for_operation 方法会返回。 分析返回的操作的 `resourceLocation` 标头值，以获取新的知识库 ID。

[!code-javascript[Create a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=createKnowledgeBase&highlight=15,30)]

请确保包括上述代码中引用的 [`wait_for_operation`](#get-status-of-an-operation) 函数，以便成功创建知识库。

## <a name="update-a-knowledge-base"></a>更新知识库

可以更新知识库，方法是：将知识库 ID 和包含 [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add)、[update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) 和 [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO 对象的 [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) 传递给 [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) 方法。 DTO 也基本上是 JSON 对象。 使用 [wait_for_operation](#get-status-of-an-operation) 方法来确定更新是否成功。

[!code-javascript[Update a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=updateKnowledgeBase&highlight=28)]

请确保包括上述代码中引用的 [`wait_for_operation`](#get-status-of-an-operation) 函数，以便成功更新知识库。

## <a name="publish-a-knowledge-base"></a>发布知识库

使用 [publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) 方法发布知识库。 这样会通过知识库 ID 获取当前保存的已训练模型，并在某个终结点上将其发布。 检查 HTTP 响应代码，验证发布是否成功。

[!code-javascript[Publish a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=publishKnowledgeBase&highlight=3)]


## <a name="get-published-endpoint"></a>获取发布的终结点

发布知识库后，通过查询预测运行时的 generateAnswer API 访问已发布的知识库。 若要执行此操作，需要运行时的终结点密钥。 这与创作密钥不同。

[!code-javascript[Get endpoint keys](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=getEndpointKeys&highlight=3)]

调用返回了两个终结点密钥。 仅有一个密钥是访问运行时终结点所必需的。

## <a name="delete-a-knowledge-base"></a>删除知识库

使用 [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) 方法与知识库 ID 参数删除知识库。

[!code-javascript[Delete a knowledge base](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=deleteKnowledgeBase&highlight=3)]

## <a name="get-status-of-an-operation"></a>获取操作的状态

某些方法（例如 create 和 update）可能需要很长的时间，系统不会等待此过程完成，而是返回一个 [operation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest)。 使用操作中的[操作 ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) 进行轮询（使用重试逻辑），确定原始方法的状态。

以下代码块中的 _delayTimer_ 调用用于模拟重试逻辑。 将此项替换为你自己的重试逻辑。

[!code-javascript[Monitor an operation](~/cognitive-services-quickstart-code/javascript/QnAMaker/sdk/qnamaker_quickstart.js?name=monitorOperation&highlight=8,13)]

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `node index.js` 命令运行应用程序。

本文中的所有代码片段均[可用](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/QnAMaker/sdk/qnamaker_quickstart.js)，并且可以作为单个文件运行。

```console
node index.js
```

程序将状态输出到控制台：

```console
qnamaker_quickstart@1.0.0 start C:\samples\cognitive-services-quickstart-code\javascript\QnAMaker\sdk> node index.js

Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Create operation 200, KB ID 99df758d-f23f-4931-ab83-e738fe978e69
Operation state - Running
Operation state - Running
Operation state - Running
Operation state - Succeeded
Update operation state 200 - HTTP status 200
Publish request succeeded - HTTP status 204
GetEndpointKeys request succeeded - HTTP status 200 - primary key 8482830b-681e-400e-b8a3-4016278aba64
QnA Maker FAQ stored in English language with 1 sources, last updated 2020-01-12T16:54:40Z
New KB name stored in English language with 1 sources, last updated 2020-01-12T17:32:16Z
New KB name stored in English language with 1 sources, last updated 2020-01-13T00:27:46Z
Delete operation state succeeded - HTTP status 204
done
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)