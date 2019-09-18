---
title: 快速入门：适用于 Python 的个性化体验创建服务客户端库 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 通过一个学习循环开始使用适用于 Python 的个性化体验创建服务客户端库。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/06/2019
ms.author: diberry
ms.openlocfilehash: 404ee6fd51493735b8f96e98276f71d1d2f91158
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882876"
---
# <a name="quickstart-personalize-client-library-for-python"></a>快速入门：让适用于 Python 的客户端库个性化

在本 python 快速入门中使用个性化体验创建服务显示个性化内容。

适用于 Python 的个性化体验创建服务客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。

 * 为一系列个性化操作排名。
 * 报告奖励评分（指示最高排名操作是否成功）。

[包 (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [示例](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>设置

### <a name="create-a-personalizer-azure-resource"></a>创建个性化体验创建服务 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本地计算机上创建用于个性化体验创建服务的资源。 还可以：

* 免费获取在 7 天内有效的[试用版密钥](https://azure.microsoft.com/try/cognitive-services)。 注册之后，它将在 [Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上提供。  
* 在 [Azure 门户](https://portal.azure.com/)上查看资源。

从试用订阅或资源获取密钥后，创建两个[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)：

* `PERSONALIZER_KEY`（代表资源键）。
* `PERSONALIZER_ENDPOINT`（代表资源终结点）。

在 Azure 门户中，可以从“快速入门”页中获取密钥和终结点值  。


### <a name="install-the-python-library-for-personalizer"></a>为个性化体验创建服务安装 Python 库

使用以下命令安装适用于 Python 的个性化体验创建服务客户端库：

```console
pip install azure-cognitiveservices-personalizer
```

如果你使用的是 Visual Studio IDE，客户端库可用作可下载的 NuGet 包。

### <a name="change-the-model-update-frequency"></a>更改模型更新频率

在 Azure 门户中的个性化体验创建服务中，将“模型更新频率”  更改为 10 秒。 这将快速训练服务，使你可以看到顶部操作如何针对每次迭代而变化。

![更改模型更新频率](./media/settings/configure-model-update-frequency-settings.png)

首次实例化个性化体验创建服务循环时，由于没有奖励 API 调用可供训练，因此没有模型。 排名调用将为每个项返回相等的概率。 你的应用程序仍应始终使用 RewardActionId 的输出对内容进行排名。

## <a name="object-model"></a>对象模型

个性化体验创建服务客户端是一个 PersonalizerClient 对象，它使用包含密钥的 Microsoft.Rest.ServiceClientCredentials 向 Azure 进行身份验证。

若要请求内容排名，请创建一个 RankRequest，然后将其传递给 client.Rank 方法。 Rank 方法返回包含排名内容的 RankResponse。 

若要向个性化体验创建服务发送奖励，请创建一个 RewardRequest，然后将其传递给 client.Reward 方法。 

在本快速入门中，可以很容易地确定奖励。 在生产系统中，确定哪些因素会影响[奖励评分](concept-rewards.md)以及影响程度可能是一个复杂的过程，你的判断可能会随时改变。 这应是个性化体验创建服务体系结构中的主要设计决策之一。 

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Python 的个性化体验创建服务客户端库执行以下操作：

* [创建个性化体验创建服务客户端](#create-a-personalizer-client)
* [请求排名](#request-a-rank)
* [发送奖励](#send-a-reward)

## <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

在首选编辑器或 IDE 中创建名为 `sample.py` 的新 Python 应用程序。 

## <a name="add-the-dependencies"></a>添加依赖项

在首选的编辑器或 IDE 中，从项目目录打开 **Program.cs** 文件。 将现有 `using` 代码替换为以下 `using` 指令：

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>添加个性化体验创建服务资源信息

在 **Program** 类中，为资源的 Azure 密钥以及从环境变量提取的终结点创建名为 `PERSONALIZER_RESOURCE_KEY` 和 `PERSONALIZER_RESOURCE_ENDPOINT` 的变量。 如果在启动应用程序后创建了环境变量，则需要关闭并重新加载运行它的编辑器、IDE 或 shell 以访问该变量。 稍后将在本快速入门中创建这些方法。

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>创建个性化体验创建服务客户端

接下来，创建一个用于返回个性化体验创建服务客户端的方法。 该方法的参数为 `PERSONALIZER_RESOURCE_ENDPOINT`，ApiKey 为 `PERSONALIZER_RESOURCE_KEY`。

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>获取以操作形式表示的内容选项

操作表示你希望个性化体验创建服务排名的内容选项。 将以下方法添加到 Program 类，以从命令行获取用户的日期时间输入及其当前食品偏好。

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>创建学习循环

个性化体验创建服务学习循环是一个[排名](#request-a-rank)和[奖励](#send-a-reward)调用周期。 在本快速入门中，用于个性化内容的每个排名调用都后接一个奖励调用，该奖励调用让个性化体验创建服务知道服务对内容排名的适用程度。 

程序的 `main` 方法中的以下代码循环调用某个周期，要求用户在命令行中指定其偏好，将该信息发送给个性化体验创建服务以进行排名，向客户显示排名的选项以让他们从列表中进行选择，然后向个性化体验创建服务发送奖励，指出服务对所选内容排名的适用程度。

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

在接下来的部分中，让我们仔细看看排名和奖励调用。

## <a name="request-a-rank"></a>请求排名

为了完成排名请求，程序将要求用户指定偏好，以创建内容选项的 `currentContent`。 该过程可以创建要从排名中排除的内容，显示为 `excludeActions`。 排名请求需要 actions、currentContext、excludeActions 以及唯一的排名事件 ID（GUID 形式）才能接收排名的响应。 

本快速入门使用简单的日期时间和用户食品偏好上下文特征。 在生产系统中，确定和[评估](concept-feature-evaluation.md)[操作与特征](concepts-features.md)可能不是一个简单的过程。  

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>发送奖励

为了完成奖励请求，程序将从命令行获取用户的选择，为每种选择分配一个数值，然后将唯一排名事件 ID 和该数值发送到奖励方法。

本快速入门分配一个简单的数字（0 或 1）作为奖励。 在生产系统中，确定何时向[奖励](concept-rewards.md)调用发送哪种内容可能不是一个简单的过程，这取决于具体的需求。 

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>运行程序

从应用程序目录使用 python 运行应用程序。

```console
python sample.py
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
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py) 上找到此示例的源代码。
