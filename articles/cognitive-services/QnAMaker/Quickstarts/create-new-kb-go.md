---
title: 快速入门：创建知识库 - REST、Go - QnA Maker
description: 此 Go 基于 REST 的快速入门详细介绍如何以编程方式创建一个示例 QnA Maker 知识库，该知识库会显示在认知服务 API 帐户的 Azure 仪表板中。
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 221220345f4f3b7aff2a32c956d921f677ca0627
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851912"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-go"></a>快速入门：通过 Go 在 QnA Maker 中创建知识库

本快速入门将指导你完成以编程方式创建示例 QnA Maker 知识库的过程。 QnA Maker 自动从[数据源](../Concepts/knowledge-base.md)中从半结构化内容（例如常见问题解答）中自动提取问题和解答。 用于知识库的模型是在 API 请求的正文中发送的 JSON 中定义的。

本快速入门调用了 QnA Maker API：
* [创建知识库](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [获取操作详细信息](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[参考文档](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [GO 示例](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>必备条件

* [Go 1.10.1](https://golang.org/dl/)
* 必须已有一个 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥和终结点（包括资源名称），请在 Azure 门户中为资源选择“快速入门”。

## <a name="create-a-knowledge-base-go-file"></a>创建知识库 Go 文件

创建名为 `create-new-knowledge-base.go` 的文件。

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 `create-new-knowledge-base.go` 的顶部，添加以下行来向项目添加必需的依赖项：

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>添加必需的常量
在上述必需的依赖项后，添加访问 QnA Maker 所必需的常量。

设置以下值：

* `<your-qna-maker-subscription-key>` - **密钥**是一个 32 字符的字符串，可以在“快速入门”页的 QnA Maker 资源上的 Azure 门户中使用。 这与预测终结点密钥不同。
* `{your-resource-name}` - **资源名称**用于构造用于创作的创作终结点 URL，其格式为 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`。 这与用于查询预测终结点的 URL 不同。

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=13-20 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>添加知识库模型定义
在常量后，添加以下知识库模型定义。 模型将在定义后转换为字符串。

[!code-go[Add the KB model definition](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=22-44 "Add the KB model definition")]

## <a name="add-supporting-structures-and-functions"></a>添加支持结构和函数

接下来，添加以下支持函数。

1. 添加 HTTP 请求的结构：

    [!code-go[Add the structure for an HTTP request](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=46-49 "Add the structure for an HTTP request")]

2. 添加以下方法，以处理向 QnA Maker API 发出的 POST 请求。 在本快速入门中，POST 用于将知识库定义发送到 QnA Maker。

    [!code-go[Add the POST method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=51-66 "Add the POST method")]

3. 添加以下方法，以处理向 QnA Maker API 发出的 GET 请求。 在本快速入门中，GET 用于检查创建操作的状态。

    [!code-go[Add the GET method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=68-83 "Add the GET method")]

## <a name="add-function-to-create-kb"></a>添加函数以创建知识库

添加以下函数以发出用于创建知识库的 HTTP POST 请求。 _create_ **Operation ID** 在 POST 响应标头字段 **Location** 中返回，然后在 GET 请求中用作路由的一部分。 `Ocp-Apim-Subscription-Key` 是用于身份验证的 QnA Maker 服务密钥。

[!code-go[Add the create_kb method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=85-97 "Add the create_kb method")]

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

## <a name="add-function-to-get-status"></a>添加函数以获取状态

添加以下函数以发出用于检查操作状态的 HTTP GET 请求。 `Ocp-Apim-Subscription-Key` 是用于身份验证的 QnA Maker 服务密钥。

[!code-go[Add the check_status method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=99-108 "Add the check_status method")]

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
## <a name="add-main-function"></a>添加 main 函数

以下函数是主函数，用于创建知识库并重复检查状态。 因为知识库创建可能要花费一些时间，所以你需要重复用来检查状态的调用，直到状态为成功或失败。

[!code-go[Add the main method](~/samples-qnamaker-go/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.go?range=110-140 "Add the main method")]


## <a name="compile-the-program"></a>编译程序
输入以下命令以编译该文件。 对于成功的生成，命令提示符不会返回任何信息。

```bash
go build create-new-knowledge-base.go
```

## <a name="run-the-program"></a>运行程序

在命令行中输入以下命令以运行程序。 它向 QnA Maker API 发送创建知识库的请求，然后每隔 30 秒轮询一次结果。 每个响应都将输出到控制台窗口中。

```bash
go run create-new-knowledge-base
```

创建知识库以后，即可在 QnA Maker 门户 - [My knowledge bases](https://www.qnamaker.ai/Home/MyServices)（我的知识库）页中查看它。

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://go.microsoft.com/fwlink/?linkid=2092179)