---
title: 快速入门：适用于 Node.js 的 QnA Maker 客户端库
titleSuffix: Azure Cognitive Services
description: 适用于 Node.js 的 QnA Maker 客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。  使用 QnA Maker，可以根据常见问题解答文档、URL 和产品手册等半结构化内容打造一项问题与解答服务。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/07/2019
ms.author: diberry
ms.openlocfilehash: dcbbaa2f4c6ebe709c879909f873b212f238ff2a
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375859"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>快速入门：适用于 Node.js 的 QnA Maker 客户端库

适用于 Node.js 的 QnA Maker 客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。  使用 QnA Maker，可以根据常见问题解答文档、URL 和产品手册等半结构化内容打造一项问题与解答服务。 

可以使用适用于 Node.js 的 QnA Maker 客户端库执行以下操作：

* 创建知识库 
* 管理知识库
* 发布知识库

[参考文档](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [包 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node.js 示例](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Node.js](https://nodejs.org)。

## <a name="setting-up"></a>设置

### <a name="create-a-qna-maker-azure-resource"></a>创建 QnA Maker Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本地计算机上创建用于 QnA Maker 的资源。 

从资源获取密钥后，为资源[创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)（名为 `QNAMAKER_SUBSCRIPTION_KEY` 和 `QNAMAKER_HOST`）。 使用在 Azure 门户中资源的“密钥”  和“概述”  页中找到的密钥和主机值。

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

运行 `npm init -y` 命令以使用 `package.json` 文件创建一个 node 应用程序。 

```console
npm init -y
```

### <a name="install-the-client-library"></a>安装客户端库

安装 `ms-rest-azure` 和 `azure-cognitiveservices-qnamaker` NPM 包:

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

应用的 `package.json` 文件将使用依赖项进行更新。


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
* [获取操作的状态](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>添加依赖项

创建名为 `index.js` 的文件。 将 QnA Maker 库和 Azure REST 库添加到文件。

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

为资源的 Azure 终结点和密钥创建变量。 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。


|环境变量|Node.js 变量|示例|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|32 字符 GUID|
|`QNAMAKER_HOST`|`endpoint`|`https://westus.api.cognitive.microsoft.com`|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>验证客户端

接下来，使用密钥创建 ServiceClientCredentials 对象，并将其与终结点一起使用以创建 [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) 对象。 使用客户端对象获取[知识库](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest)对象。


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>创建知识库

知识库为来自三个源的 [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) 对象存储问答对：

* 对于**编辑内容**，请使用 [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) 对象。
* 对于**文件**，请使用 [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) 对象。 
* 对于 **URL**，请使用字符串列表。

调用 [create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) 方法，然后将返回的操作 ID 传递给 [Operations.getDetails](#get-status-of-an-operation) 方法以轮询状态。 

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]


## <a name="update-a-knowledge-base"></a>更新知识库

可以更新知识库，方法是：将知识库 ID 和包含 [add](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add)、[update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update) 和 [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) DTO 对象的 [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) 传递给 [update](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) 方法。 使用 [Operation.getDetail](#get-status-of-an-operation) 方法来确定更新是否成功。

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

## <a name="publish-a-knowledge-base"></a>发布知识库

使用 [publish](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) 方法发布知识库。 这样会通过知识库 ID 获取当前保存的已训练模型，并在某个终结点上将其发布。 

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>删除知识库

使用 [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) 方法与知识库 ID 参数删除知识库。 

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>获取操作的状态

某些方法（例如 create 和 update）可能需要很长的时间，系统不会等待此过程完成，而是返回一个 [operation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest)。 使用操作中的[操作 ID](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) 进行轮询（使用重试逻辑），确定原始方法的状态。 

以下代码块中的 _setTimeout_ 调用用于模拟异步代码。 将其替换为重试逻辑。 

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `node index.js` 命令运行应用程序。

```console
node index.js
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[教程：创建 KB 并答复 KB](../tutorials/create-publish-query-in-portal.md)

* [什么是 QnA Maker API？](../Overview/overview.md)
* [编辑知识库](../how-to/edit-knowledge-base.md)
* [获取使用情况分析](../how-to/get-analytics-knowledge-base.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) 上找到此示例的源代码。