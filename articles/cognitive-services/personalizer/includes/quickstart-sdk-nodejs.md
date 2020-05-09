---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 04/27/2020
ms.author: diberry
ms.openlocfilehash: 237ba5ba390b4065a67f29611fbd43375c239578
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188840"
---
[参考文档](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest) |[库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [包 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [示例](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>必备条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Node.js](https://nodejs.org) 和 NPM。

## <a name="using-this-quickstart"></a>使用此快速入门


使用此快速入门有几个步骤：

* 在 Azure 门户中，创建一个个性化体验创建服务资源
* 在 Azure 门户中，对于个性化体验创建服务资源，在“配置”页上，将模型更新频率更改为非常短的间隔 
* 在代码编辑器中，创建一个代码文件并编辑该代码文件
* 在命令行或终端中，从命令行安装 SDK
* 在命令行或终端中，运行代码文件

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

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

## <a name="object-model"></a>对象模型

个性化体验创建服务客户端是 [PersonalizerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest) 对象，使用包含密钥的 Microsoft.Rest.ServiceClientCredentials 向 Azure 进行身份验证。

若要请求内容的单一最佳项，请创建一个 [RankRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rankrequest?view=azure-node-latest)，然后将其传递给 [client.Rank](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/personalizerclient?view=azure-node-latest#rank-rankrequest--msrest-requestoptionsbase-) 方法。 Rank 方法返回 RankResponse。

若要向个性化体验创建服务发送奖励，请创建一个 [RewardRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/rewardrequest?view=azure-node-latest)，然后将其传递给 Events 类的 [Reward](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/events?view=azure-node-latest#reward-string--rewardrequest--servicecallback-void--) 方法。

在本快速入门中，可以很容易地确定奖励。 在生产系统中，确定哪些因素会影响[奖励评分](../concept-rewards.md)以及影响程度可能是一个复杂的过程，你的判断可能会随时改变。 这应是个性化体验创建服务体系结构中的主要设计决策之一。

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Node.js 的个性化体验创建服务客户端库执行以下操作：

* [创建个性化体验创建服务客户端](#create-a-personalizer-client)
* [排名 API](#request-the-best-action)
* [奖励 API](#send-a-reward)

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

操作表示你希望个性化体验创建服务从中选择最佳内容项的内容选择。 将以下方法添加到 Program 类，以表示操作及其特征的集合。

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>创建学习循环

个性化体验创建服务学习循环是一个[排名](#request-the-best-action)和[奖励](#send-a-reward)调用周期。 在本快速入门中，用于个性化内容的每个排名调用都后接一个奖励调用，该奖励调用让个性化体验创建服务知道该服务的表现如何。

以下代码循环调用以下循环：在命令行询问用户的首选项，将该信息发送给个性化体验创建服务以选择最佳操作，向客户显示所选项以让他们从列表中进行选择，然后向个性化体验创建服务发送奖励，指出服务在做选择时的表现如何。

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

在接下来的部分中，让我们仔细看看排名和奖励调用。

在运行代码文件之前，添加以下方法以[获取内容选项](#get-content-choices-represented-as-actions)：

* getActionsList
* getContextFeaturesList

## <a name="request-the-best-action"></a>请求最佳操作

为了完成排名请求，程序会询问用户的首选项以创建内容选项。 该过程可以创建要从操作中排除的内容，显示为 `excludeActions`。 排名请求需要 [actions](../concepts-features.md#actions-represent-a-list-of-options) 及其特征、currentContext 特征、excludeActions 以及唯一的排名事件 ID 才能接收排名的响应。

本快速入门使用简单的日期时间和用户食品偏好上下文特征。 在生产系统中，确定和[评估](../concept-feature-evaluation.md)[操作与特征](../concepts-features.md)可能是一件非常重要的事情。

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>发送奖励


为了获取奖励评分以在奖励请求中发送，程序将从命令行获取用户的选择，为该选择分配一个数值，然后将唯一事件 ID 和奖励评分（作为数值）发送到奖励 API。

本快速入门分配一个简单的数字（0 或 1）作为奖励评分。 在生产系统中，确定何时向[奖励](../concept-rewards.md)调用发送哪种内容可能不是一个简单的过程，这取决于具体的需求。

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>运行程序

从应用程序目录使用 Node.js 运行应用程序。

```console
node sample.js
```

![快速入门程序会提出一些问题来收集用户的偏好（称为“特征”），然后提供排名最高的操作。](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
