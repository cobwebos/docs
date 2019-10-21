---
title: 快速入门：适用于 Node.js 的个性化体验创建服务客户端库 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 通过一个学习循环开始使用适用于 Node.js 的个性化体验创建服务客户端库。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: ee647668e8b5826706e8d9bb8a82acaf53fd3d8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515204"
---
# <a name="quickstart-personalizer-client-library-for-nodejs"></a>快速入门：适用于 Node.js 的个性化体验创建服务客户端库

在本 Node.js 快速入门中使用个性化体验创建服务显示个性化内容。

适用于 Node.js 的个性化体验创建服务客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。

 * 为一系列个性化操作排名。
 * 报告奖励评分（指示最高排名操作是否成功）。

[库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [包 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [示例](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Node.js](https://nodejs.org) 和 NPM。

## <a name="using-this-quickstart"></a>使用此快速入门


使用此快速入门有几个步骤：

* 在 Azure 门户中，创建一个个性化体验创建服务资源
* 在 Azure 门户中，对于个性化体验创建服务资源，在“设置”  页上，更改模型更新频率
* 在代码编辑器中，创建一个代码文件并编辑该代码文件
* 在命令行或终端中，从命令行安装 SDK
* 在命令行或终端中，运行代码文件


## <a name="create-a-personalizer-azure-resource"></a>创建个性化体验创建服务 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本地计算机上创建用于个性化体验创建服务的资源。 还可以：

* 免费获取在 7 天内有效的[试用版密钥](https://azure.microsoft.com/try/cognitive-services)。 注册之后，它将在 [Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上提供。  
* 在 [Azure 门户](https://portal.azure.com/)上查看资源。

从试用订阅或资源获取密钥后，创建两个[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)：

* `PERSONALIZER_KEY`（代表资源键）。
* `PERSONALIZER_ENDPOINT`（代表资源终结点）。

在 Azure 门户中，可以从“快速入门”页中获取密钥和终结点值  。


## <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

运行 `npm init -y` 命令以创建 `package.json` 文件。 

```console
npm init -y
```

## <a name="install-the-nodejs-library-for-personalizer"></a>为个性化体验创建服务安装 Node.js 库

使用以下命令安装适用于 Node.js 的个性化体验创建服务客户端库：

```console
npm install @azure/cognitiveservices-personalizer --save
```

安装此快速入门的其余 NPM 包：

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

## <a name="change-the-model-update-frequency"></a>更改模型更新频率

在 Azure 门户的“设置”  页上的个性化体验创建服务资源中，将“模型更新频率”  更改为 10 秒。 这将快速训练服务，使你可以看到顶部操作如何针对每次迭代而变化。

![更改模型更新频率](./media/settings/configure-model-update-frequency-settings.png)

首次实例化个性化体验创建服务循环时，由于没有奖励 API 调用可供训练，因此没有模型。 排名调用将为每个项返回相等的概率。 你的应用程序仍应始终使用 RewardActionId 的输出对内容进行排名。

## <a name="object-model"></a>对象模型

个性化体验创建服务客户端是一个 PersonalizerClient 对象，它使用包含密钥的 Microsoft.Rest.ServiceClientCredentials 向 Azure 进行身份验证。

若要请求内容排名，请创建一个 RankRequest，然后将其传递给 client.Rank 方法。 Rank 方法返回包含排名内容的 RankResponse。 

若要向个性化体验创建服务发送奖励，请创建一个 RewardRequest，然后将其传递给 client.Reward 方法。 

在本快速入门中，可以很容易地确定奖励。 在生产系统中，确定哪些因素会影响[奖励评分](concept-rewards.md)以及影响程度可能是一个复杂的过程，你的判断可能会随时改变。 这应是个性化体验创建服务体系结构中的主要设计决策之一。 

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Node.js 的个性化体验创建服务客户端库执行以下操作：

* [创建个性化体验创建服务客户端](#create-a-personalizer-client)
* [请求排名](#request-a-rank)
* [发送奖励](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在首选编辑器或 IDE 中创建名为 `sample.js` 的新 Node.js 应用程序。 

## <a name="add-the-dependencies"></a>添加依赖项

在首选编辑器或 IDE 中打开 **sample.js** 文件。 添加以下 `requires` 以添加 NPM 包：

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>添加个性化体验创建服务资源信息

为资源的 Azure 密钥以及从环境变量拉取的终结点创建名为 `PERSONALIZER_KEY` 和 `PERSONALIZER_ENDPOINT` 的变量。 如果在启动应用程序后创建了环境变量，则需要关闭并重新加载运行它的编辑器、IDE 或 shell 以访问该变量。 稍后将在本快速入门中创建这些方法。

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>创建个性化体验创建服务客户端

接下来，创建一个用于返回个性化体验创建服务客户端的方法。 该方法的参数为 `PERSONALIZER_RESOURCE_ENDPOINT`，ApiKey 为 `PERSONALIZER_RESOURCE_KEY`。

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>获取以操作形式表示的内容选项

操作表示你希望个性化体验创建服务排名的内容选项。 将以下方法添加到 Program 类，以从命令行获取用户的日期时间输入及其当前食品偏好。

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>创建学习循环

个性化体验创建服务学习循环是一个[排名](#request-a-rank)和[奖励](#send-a-reward)调用周期。 在本快速入门中，用于个性化内容的每个排名调用都后接一个奖励调用，该奖励调用让个性化体验创建服务知道服务对内容排名的适用程度。 

以下循环代码循环调用以下循环：在命令行询问用户的首选项，将该信息发送给个性化体验创建服务以进行排名，向客户显示排名的选项以让他们从列表中进行选择，然后向个性化体验创建服务发送奖励，指出服务对所选内容排名的适用程度。

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

在接下来的部分中，让我们仔细看看排名和奖励调用。

在运行代码文件之前，添加以下方法以[获取内容选项](#get-content-choices-represented-as-actions)：

* getActionsList
* getContextFeaturesList

## <a name="request-a-rank"></a>请求排名

为了完成排名请求，程序会询问用户的首选项以创建内容选项。 该过程可以创建要从排名中排除的内容，显示为 `excludeActions`。 排名请求需要 [actions](concepts-features.md#actions-represent-a-list-of-options)、currentContext、excludeActions 以及唯一的排名事件 ID（作为 GUID）才能接收排名的响应。 

本快速入门使用简单的日期时间和用户食品偏好上下文特征。 在生产系统中，确定和[评估](concept-feature-evaluation.md)[操作与特征](concepts-features.md)可能不是一个简单的过程。  

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>发送奖励

为了完成奖励请求，程序将从命令行获取用户的选择，为每种选择分配一个数值，然后将唯一排名事件 ID 和该数值发送到奖励方法。

本快速入门分配一个简单的数字（0 或 1）作为奖励。 在生产系统中，确定何时向[奖励](concept-rewards.md)调用发送哪种内容可能不是一个简单的过程，这取决于具体的需求。 

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>运行程序

从应用程序目录使用 Node.js 运行应用程序。

```console
node sample.js
```

![快速入门程序会提出一些问题来收集用户的偏好（称为“特征”），然后提供排名最高的操作。](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[个性化体验创建服务的工作原理](how-personalizer-works.md)

* [什么是个性化体验创建服务？](what-is-personalizer.md)
* [可在哪种场合下使用个性化体验创建服务？](where-can-you-use-personalizer.md)
* [故障排除](troubleshooting.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) 上找到此示例的源代码。
