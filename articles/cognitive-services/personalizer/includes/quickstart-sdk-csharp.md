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
ms.openlocfilehash: 7fd63841cabd91d46dd311f571fd100bbcfdd0fe
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122734"
---
[参考文档](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [示例](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>必备条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 的当前版本。

## <a name="using-this-quickstart"></a>使用此快速入门

使用此快速入门有几个步骤：

* 在 Azure 门户中，创建一个个性化体验创建服务资源
* 在 Azure 门户中，对于个性化体验创建服务资源，在“配置”页上，将模型更新频率更改为非常短的间隔 
* 在代码编辑器中，创建一个代码文件并编辑该代码文件
* 在命令行或终端中，从命令行安装 SDK
* 在命令行或终端中，运行代码文件

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>新建 C# 应用程序

在首选编辑器或 IDE 中创建新的 .NET Core 应用程序。

在控制台窗口（例如 CMD、PowerShell 或 Bash）中，使用 dotnet `new` 命令创建名为 `personalizer-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：`Program.cs`。

```console
dotnet new console -n personalizer-quickstart
```

将目录更改为新创建的应用文件夹。 可使用以下代码生成应用程序：

```console
dotnet build
```

生成输出不应包含警告或错误。

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="install-the-sdk"></a>安装 SDK

在应用程序目录中，使用以下命令安装适用于 .NET 的个性化体验创建服务客户端库：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

如果你使用的是 Visual Studio IDE，客户端库可用作可下载的 NuGet 包。

## <a name="object-model"></a>对象模型

个性化体验创建服务客户端是 [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) 对象，使用包含密钥的 Microsoft.Rest.ServiceClientCredentials 向 Azure 进行身份验证。

若要请求内容的单一最佳项，请创建一个 [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)，然后将其传递给 [client.Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) 方法。 Rank 方法返回 RankResponse。

若要向个性化体验创建服务发送奖励评分，请创建一个 [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)，然后将其传递给 [client.Reward](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) 方法。

在本快速入门中，可以很容易地确定奖励评分。 在生产系统中，确定哪些因素会影响[奖励评分](../concept-rewards.md)以及影响程度可能是一个复杂的过程，你的判断可能会随时改变。 此设计决策应是个性化体验创建服务体系结构中的主要决策之一。

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 .NET 的个性化体验创建服务客户端库执行以下任务：

* [创建个性化体验创建服务客户端](#create-a-personalizer-client)
* [排名 API](#request-the-best-action)
* [奖励 API](#send-a-reward)

## <a name="add-the-dependencies"></a>添加依赖项

在首选的编辑器或 IDE 中，从项目目录打开 **Program.cs** 文件。 将现有 `using` 代码替换为以下 `using` 指令：

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>添加个性化体验创建服务资源信息

在 **Program** 类中，为资源的 Azure 密钥以及从环境变量提取的终结点创建名为 `PERSONALIZER_RESOURCE_KEY` 和 `PERSONALIZER_RESOURCE_ENDPOINT` 的变量。 如果在启动应用程序后创建了环境变量，则需要关闭并重新加载运行它的编辑器、IDE 或 shell 以访问该变量。 稍后将在本快速入门中创建这些方法。

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>创建个性化体验创建服务客户端

接下来，创建一个用于返回个性化体验创建服务客户端的方法。 该方法的参数为 `PERSONALIZER_RESOURCE_ENDPOINT`，ApiKey 为 `PERSONALIZER_RESOURCE_KEY`。

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>获取食品项作为可排名操作

操作表示你希望个性化体验创建服务从中选择最佳内容项的内容选择。 将以下方法添加到 Program 类，以表示操作及其特征的集合。

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>获取上下文的用户首选项

将以下方法添加到 Program 类，以从命令行获取用户的日期时间输入及其当前食品偏好。 它们将用作上下文特征。

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

这两个方法都使用 `GetKey` 方法从命令行读取用户的选择。

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>创建学习循环

个性化体验创建服务学习循环是一个[排名](#request-the-best-action)和[奖励](#send-a-reward)调用周期。 在本快速入门中，用于个性化内容的每个排名调用都后接一个奖励调用，该奖励调用让个性化体验创建服务知道该服务的表现如何。

以下代码循环调用以下循环：在命令行询问用户的首选项，将该信息发送给个性化体验创建服务以选择最佳操作，向客户显示所选项以让他们从列表中进行选择，然后向个性化体验创建服务发送奖励评分，指出服务在做选择时的表现如何。

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

在运行代码文件之前，添加以下方法以[获取内容选项](#get-food-items-as-rankable-actions)：

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>请求最佳操作

为了完成排名请求，程序将要求用户指定偏好，以创建内容选项的 `currentContent`。 该过程可以创建要从操作中排除的内容，显示为 `excludeActions`。 排名请求需要 actions 及其特征、currentContext 特征、excludeActions 以及唯一的事件 ID 才能接收响应。

本快速入门使用简单的日期时间和用户食品偏好上下文特征。 在生产系统中，确定和[评估](../concept-feature-evaluation.md)[操作与特征](../concepts-features.md)可能是一件非常重要的事情。

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>发送奖励

为了获取奖励评分以在奖励请求中发送，程序将从命令行获取用户的选择，为该选择分配一个数值，然后将唯一事件 ID 和奖励评分（作为数值）发送到奖励 API。

本快速入门分配一个简单的数字（0 或 1）作为奖励评分。 在生产系统中，确定何时向[奖励](../concept-rewards.md)调用发送哪种内容可能不是一个简单的过程，这取决于具体的需求。

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>运行程序

从应用程序目录，使用 dotnet `run` 命令运行应用程序。

```console
dotnet run
```

![快速入门程序会提出一些问题来收集用户的偏好（称为“特征”），然后提供排名最高的操作。](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[本快速入门的源代码](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs)已在个性化体验创建服务示例 GitHub 存储库中提供。
