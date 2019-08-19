---
title: 快速入门：适用于 Node.js 的 QnA Maker REST API
titleSuffix: Azure Cognitive Services
description: 适用于 Node.js 的 QnA Maker REST API 入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。  使用 QnA Maker，可以根据常见问题解答文档、URL 和产品手册等半结构化内容打造一项问题与解答服务。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/13/2019
ms.author: diberry
ms.openlocfilehash: ad7986a0c4b0d59322ccebcaa6b1c70776164c48
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015704"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>快速入门：适用于 Node.js 的 QnA Maker REST API

适用于 Node.js 的 QnA Maker REST API 入门。 遵循以下步骤尝试学习基本任务的示例代码。  使用 QnA Maker，可以根据常见问题解答文档、URL 和产品手册等半结构化内容打造一项问题与解答服务。 

使用适用于 Node.js 的 QnA Maker REST API 可以：

* 创建知识库
* 替换知识库
* 发布知识库
* 删除知识库
* 下载知识库
* 获取操作的状态

[参考文档](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Node.js 示例](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Node.js](https://nodejs.org)。

## <a name="setting-up"></a>设置

### <a name="create-a-qna-maker-azure-resource"></a>创建 QnA Maker Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本地计算机上创建用于 QnA Maker 的资源。 

从资源获取密钥后，为资源[创建环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)（名为 `QNAMAKER_RESOURCE_KEY` 和 `QNAMAKER_AUTHORING_ENDPOINT`）。 使用在 Azure 门户中资源的“开始”页中找到的密钥和主机值  。

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

运行 `npm init -y` 命令以创建 `package.json` node 文件。 

```console
npm init -y
```

添加 `reqeuestretry` 和 `request` NPM 包：

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Node.js 的 QnA Maker REST API 执行以下操作：

* [创建知识库](#create-a-knowledge-base)
* [替换知识库](#replace-a-knowledge-base)
* [发布知识库](#publish-a-knowledge-base)
* [删除知识库](#delete-a-knowledge-base)
* [下载知识库](#download-the-knowledge-base)
* [获取操作的状态](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>添加依赖项



创建名为 `rest-apis.js` 的文件，并添加以下 _requires_ 语句发出 HTTP 请求。 

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>添加 Azure 资源信息

为资源的 Azure 终结点和密钥创建变量。 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>创建知识库

知识库将存储基于以下内容的 JSON 对象创建的问题和回答对：

* **编辑内容**。 
* **文件** - 不需要任何权限的本地文件。 
* **URL** - 公用的 URL。

[使用 REST API 创建知识库](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)。 

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>替换知识库

[使用 REST API 替换知识库](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>发布知识库

发布知识库。 此进程使得知识库可从 HTTP 查询预测终结点使用。

[使用 REST API 发布知识库](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)。


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>下载知识库 

[使用 REST API 下载知识库](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>删除知识库

用完知识库后，请将其删除。

[使用 REST API 删除知识库](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete)。

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>获取操作的状态

长时间运行的进程（例如创建进程）将返回一个操作 ID，需要使用单独的 REST API 调用来检查该 ID。 此函数采用 create 响应的正文。 重要的键是 `operationState`，它确定是否需要继续轮询。

[使用 REST API 监视对知识库执行的操作](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)。


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `node rest-apis.js` 命令运行应用程序。

```console
node rest-apis.js
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
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js) 上找到此示例的源代码。