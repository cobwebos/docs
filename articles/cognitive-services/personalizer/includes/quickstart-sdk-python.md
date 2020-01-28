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
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: fe800280a7a652b5d9a397a21a5b0a66b40af4dc
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122774"
---
[参考文档](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer?view=azure-python) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [包 (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [示例](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py)

## <a name="prerequisites"></a>必备条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="using-this-quickstart"></a>使用此快速入门


使用此快速入门有几个步骤：

* 在 Azure 门户中，创建一个个性化体验创建服务资源
* 在 Azure 门户中，对于个性化体验创建服务资源，在“配置”页上，将模型更新频率更改为非常短的间隔 
* 在代码编辑器中，创建一个代码文件并编辑该代码文件
* 在命令行或终端中，从命令行安装 SDK
* 在命令行或终端中，运行代码文件

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="install-the-python-library-for-personalizer"></a>为个性化体验创建服务安装 Python 库

使用以下命令安装适用于 Python 的个性化体验创建服务客户端库：

```console
pip install azure-cognitiveservices-personalizer
```

## <a name="object-model"></a>对象模型

个性化体验创建服务客户端是 [PersonalizerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient?view=azure-python) 对象，使用包含密钥的 Microsoft.Rest.ServiceClientCredentials 向 Azure 进行身份验证。

若要请求内容的单一最佳项，请创建一个 [RankRequest](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest?view=azure-python)，然后将其传递给 client.Rank 方法。 Rank 方法返回 RankResponse。

若要将奖励评分发送到个性化体验创建服务，请设置要发送到 EventOperations 类的 [Reward](https://docs.microsoft.com/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations?view=azure-python#reward-event-id--value--custom-headers-none--raw-false----operation-config-) 方法的事件 ID 和奖励评分（值）。

在本快速入门中，可以很容易地确定奖励。 在生产系统中，确定哪些因素会影响[奖励评分](../concept-rewards.md)以及影响程度可能是一个复杂的过程，你的判断可能会随时改变。 这应是个性化体验创建服务体系结构中的主要设计决策之一。

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Python 的个性化体验创建服务客户端库执行以下操作：

* [创建个性化体验创建服务客户端](#create-a-personalizer-client)
* [排名 API](#request-the-best-action)
* [奖励 API](#send-a-reward)

## <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

在首选编辑器或 IDE 中创建名为 `sample.py` 的新 Python 应用程序。

## <a name="add-the-dependencies"></a>添加依赖项

从项目目录中，在首选编辑器或 IDE 中打开 **sample.py** 文件。 添加以下内容：

[!code-python[Add module dependencies](~/samples-personalizer/quickstarts/python/sample.py?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>添加个性化体验创建服务资源信息

为资源的 Azure 密钥以及从环境变量拉取的终结点创建名为 `PERSONALIZER_RESOURCE_KEY` 和 `PERSONALIZER_RESOURCE_ENDPOINT` 的变量。 如果在启动应用程序后创建了环境变量，则需要关闭并重新加载运行它的编辑器、IDE 或 shell 以访问该变量。 稍后将在本快速入门中创建这些方法。

资源名称是终结点 URL 的一部分：`https://<your-resource-name>.api.cognitive.microsoft.com/`。

[!code-python[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/python/sample.py?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>创建个性化体验创建服务客户端

接下来，创建一个用于返回个性化体验创建服务客户端的方法。 该方法的参数为 `PERSONALIZER_RESOURCE_ENDPOINT`，ApiKey 为 `PERSONALIZER_RESOURCE_KEY`。

[!code-python[Create the Personalizer client](~/samples-personalizer/quickstarts/python/sample.py?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>获取以操作形式表示的内容选项

操作表示你希望个性化体验创建服务从中选择最佳内容项的内容选择。 将以下方法添加到 Program 类，以表示操作及其特征的集合。

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=getActions)]

[!code-python[Present time out day preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTimeOfDay)]

[!code-python[Present food taste preference to the user](~/samples-personalizer/quickstarts/python/sample.py?name=createUserFeatureTastePreference)]

## <a name="create-the-learning-loop"></a>创建学习循环

个性化体验创建服务学习循环是一个[排名](#request-the-best-action)和[奖励](#send-a-reward)调用周期。 在本快速入门中，用于个性化内容的每个排名调用都后接一个奖励调用，该奖励调用让个性化体验创建服务知道该服务的表现如何。

以下代码循环调用以下循环：在命令行询问用户的首选项，将该信息发送给个性化体验创建服务以选择最佳操作，向客户显示所选项以让他们从列表中进行选择，然后向个性化体验创建服务发送奖励，指出服务在做选择时的表现如何。

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=mainLoop&highlight=9,10,29)]

在运行代码文件之前，添加以下方法以[获取内容选项](#get-content-choices-represented-as-actions)：

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## <a name="request-the-best-action"></a>请求最佳操作


为了完成排名请求，程序将要求用户指定偏好，以创建内容选项的 `currentContent`。 该过程可以创建要从操作中排除的内容，显示为 `excludeActions`。 排名请求需要 actions 及其特征、currentContext 特征、excludeActions 以及唯一的事件 ID 才能接收响应。

本快速入门使用简单的日期时间和用户食品偏好上下文特征。 在生产系统中，确定和[评估](../concept-feature-evaluation.md)[操作与特征](../concepts-features.md)可能是一件非常重要的事情。

[!code-python[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/python/sample.py?name=rank)]

## <a name="send-a-reward"></a>发送奖励


为了获取奖励评分以在奖励请求中发送，程序将从命令行获取用户的选择，为该选择分配一个数值，然后将唯一事件 ID 和奖励评分（作为数值）发送到奖励 API。

本快速入门分配一个简单的数字（0 或 1）作为奖励评分。 在生产系统中，确定何时向[奖励](../concept-rewards.md)调用发送哪种内容可能不是一个简单的过程，这取决于具体的需求。

[!code-python[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/python/sample.py?name=reward&highlight=9)]

## <a name="run-the-program"></a>运行程序

从应用程序目录使用 python 运行应用程序。

```console
python sample.py
```

![快速入门程序会提出一些问题来收集用户的偏好（称为“特征”），然后提供排名最高的操作。](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)