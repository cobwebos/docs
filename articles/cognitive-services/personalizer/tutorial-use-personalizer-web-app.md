---
title: 使用 web 应用-Personalizer
description: '使用 Personalizer 循环自定义 c # .NET web 应用，根据操作（包含功能）和上下文功能向用户提供正确的内容。'
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.author: diberry
ms.openlocfilehash: 9def69a1540e81b99723c16ad34ba522d1737c7f
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2020
ms.locfileid: "84713913"
---
# <a name="add-personalizer-to-a-net-web-app"></a>将 Personalizer 添加到 .NET web 应用

使用 Personalizer 循环自定义 c # .NET web 应用，根据操作（包含功能）和上下文功能向用户提供正确的内容。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 设置 Personalizer 密钥和终结点
> * 收集功能
> * 调用排名和奖励 Api
> * 显示 top 操作，指定为_rewardActionId_



## <a name="select-the-best-content-for-a-web-app"></a>选择 web 应用的最佳内容

当 web 页面上有一系列需要个性化的_操作_（某些类型的内容）需要个性化到要显示的单个顶级项（rewardActionId）时，web 应用应使用 Personalizer。 操作列表的示例包括新闻文章、按钮位置位置和产品名称的文字选项。

将操作列表连同上下文功能一起发送到 Personalizer 循环。 Personalizer 选择单个最佳操作，然后 web 应用显示该操作。

在本教程中，操作为食物类型：

* 意大利面食
* 冰淇淋
* 汁
* 沙拉
* 爆米花
* 咖啡
* 汤

若要帮助 Personalizer 了解你的操作，请同时通过每个排名 API 请求同时发送_包含功能_和_上下文功能_的 _ 操作。

模型的一**项功能**是有关可以跨 web 应用用户群的成员进行聚合（分组）的操作或上下文的信息。 功能_不是_单独特定的（例如用户 ID）或高度特定的（如一天中的准确时间）。

### <a name="actions-with-features"></a>具有功能的操作

每个操作（内容项）都具有有助于区分食物项的功能。

这些功能未配置为 Azure 门户中的循环配置的一部分。 相反，它们是以 JSON 对象的形式发送的，每次排名 API 调用。 这样，操作及其功能就可以灵活地随着时间的推移而增大、变化和缩小，这允许 Personalizer 的发展趋势。

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>上下文特征

上下文功能有助于 Personalizer 了解操作的上下文。 此示例应用程序的上下文包括：

* 每天早上、下午、晚上、晚上的时间
* 用户首选项，salty、最佳、友爱、源或符合以下条件
* 浏览器的上下文-用户代理、地理位置、引用者

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Web 应用如何使用 Personalizer？

Web 应用使用 Personalizer 从食物选项列表中选择最佳操作。 它通过使用每个排名 API 调用发送以下信息来实现此操作：
* **操作**及其功能 `taste` ，如和`spiceLevel`
* **上下文**功能，如 `time` 日、用户 `taste` 首选项以及浏览器的用户代理信息以及上下文功能
* **要排除的操作，** 如汁
* **eventid**，这不同于对排名 API 的每个调用。

## <a name="personalizer-model-features-in-a-web-app"></a>Web 应用中的 Personalizer 模型功能

Personalizer 需要操作（内容）和当前上下文（用户和环境）的功能。 功能用于在模型中将操作与当前上下文对齐。 该模型是 Personalizer 关于操作、上下文及其功能的一种表示形式，它允许 it 人员做出明智的决定。

模型（包括功能）根据 Azure 门户中的**模型更新频率**设置，按计划进行更新。

> [!CAUTION]
> 此应用程序中的功能旨在说明功能和功能值，但不一定是要在 web 应用中使用的最佳功能。

### <a name="plan-for-features-and-their-values"></a>规划功能及其值

应使用与你的技术体系结构中的任何架构或模型相同的规划和设计来选择功能。 可以通过业务逻辑或第三方系统设置功能值。 功能值不应为非常具体，因此它们不会应用于一组或多个功能类别。

### <a name="generalize-feature-values"></a>通用化功能值

#### <a name="generalize-into-categories"></a>通用化分类

此应用程序 `time` 将用作一项功能，但将时间分为几类 `morning` ，如、、 `afternoon` `evening` 和 `night` 。 这是使用时间信息，而不是以非常特定的方式（如）的示例 `10:05:01 UTC+2` 。

#### <a name="generalize-into-parts"></a>通用化到各个部分

此应用使用浏览器中的 HTTP 请求功能。 这从包含所有数据的非常具体的字符串开始，例如：

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

**HttpRequestFeatures**类库将此字符串通用化为包含单独值的**userAgentInfo**对象。 任何太具体的值都设置为空字符串。 当发送请求的上下文功能时，它具有以下 JSON 格式：

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>使用示例 web 应用

基于浏览器的示例 web 应用（提供了所有代码）需要安装以下应用程序才能运行应用。

安装以下软件：

* [.Net core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) -示例后端服务器使用 .net core
* [Node.js](https://nodejs.org/) -客户端/前端依赖于此应用程序
* [Visual studio 2019](https://visualstudio.microsoft.com/vs/)或[.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/) -使用 visual studio 2019 的开发人员环境或 .NET Core CLI 来生成并运行应用程序

### <a name="set-up-the-sample"></a>设置示例
1. 克隆 Azure Personalizer 示例存储库。

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. 导航到_samples/HttpRequestFeatures_以打开解决方案 `HttpRequestFeaturesExample.sln` 。

    如果请求，则允许 Visual Studio 更新 Personalizer 的 .NET 包。

### <a name="set-up-azure-personalizer-service"></a>设置 Azure Personalizer 服务

1. 在 Azure 门户中[创建 Personalizer 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。

1. 在 Azure 门户中，在 `Endpoint` `Key1` `Key2` "**密钥和终结点**" 选项卡中找到和或（将工作）。这些是你 `PersonalizerServiceEndpoint` 和你的 `PersonalizerApiKey` 。
1. 填写 `PersonalizerServiceEndpoint` **appsettings.js上**的中的。
1. `PersonalizerApiKey`使用以下方法之一将配置为[应用密码](https://docs.microsoft.com/aspnet/core/security/app-secrets)：

    * 如果使用的是 .NET Core CLI，则可以使用 `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` 命令。
    * 如果使用的是 Visual Studio，则可以右键单击该项目，然后选择 "**管理用户机密**" 菜单选项来配置 Personalizer 密钥。 执行此操作后，Visual Studio 将打开一个 `secrets.json` 文件，你可以在其中添加密钥，如下所示：

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>运行示例

用以下方法之一生成并运行 HttpRequestFeaturesExample：

* Visual Studio 2019：按**F5**
* .NET Core CLI： `dotnet build` then`dotnet run`

通过 web 浏览器，你可以发送排名请求和奖励请求并查看其响应，以及从你的环境中提取的 http 请求功能。

> [!div class="mx-imgBorder"]
> ![生成并运行 HTTPRequestFeaturesExample 项目。 此时将打开一个浏览器窗口以显示单页应用程序。](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>演示 Personalizer 循环

1. 选择 "**生成新的排名请求**" 按钮，为排名 API 调用创建一个新的 JSON 对象。 这会创建操作（具有功能）和上下文功能并显示值，以便你可以查看 JSON 的外观。

    对于您自己的应用程序，操作和功能的生成可能会在客户端、服务器、两个服务器或对其他服务的调用中发生。

1. 选择 "**发送排名请求**" 将 JSON 对象发送到服务器。 服务器调用 Personalizer 排名 API。 服务器接收响应，并将顶部排名操作返回到客户端以显示。

1. 设置 "奖励" 值，然后选择 "**发送奖励请求**" 按钮。 如果不更改奖励值，则客户端应用程序将始终向 Personalizer 发送的值 `1` 。

    > [!div class="mx-imgBorder"]
    > ![生成并运行 HTTPRequestFeaturesExample 项目。 此时将打开一个浏览器窗口以显示单页应用程序。](./media/tutorial-web-app/reward-score-api-call.png)

    对于您自己的未来应用程序，在从客户端上的用户行为收集信息以及服务器上的业务逻辑后，可能会产生回报分数。

## <a name="understand-the-sample-web-app"></a>了解 web 应用示例

示例 web 应用有一个**c # .net**服务器，该服务器管理功能集合，以及发送和接收对 Personalizer 终结点的 HTTP 调用。

示例 web 应用使用**挖空的前端客户端应用程序**来捕获功能并处理用户界面操作，例如单击按钮，以及将数据发送到 .net 服务器。

以下部分介绍了开发人员在使用 Personalizer 时需要了解的服务器和客户端的各个部分。

## <a name="rank-api-client-application-sends-context-to-server"></a>排名 API：客户端应用程序向服务器发送上下文

客户端应用程序收集用户的浏览器_用户代理_。

> [!div class="mx-imgBorder"]
> ![生成并运行 HTTPRequestFeaturesExample 项目。 此时将打开一个浏览器窗口以显示单页应用程序。](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>排名 API：服务器应用程序调用 Personalizer

这是一个使用客户端应用程序的典型 .NET web 应用程序，我们为你提供了很多样板印版代码。 从以下代码段中删除不特定于 Personalizer 的任何代码，以便你可以专注于特定于 Personalizer 的代码。

### <a name="create-personalizer-client"></a>创建 Personalizer 客户端

在服务器的**Startup.cs**中，Personalizer 终结点和密钥用于创建 Personalizer 客户端。 客户端应用程序不需要与此应用程序中的 Personalizer 通信，而是依赖于服务器进行这些 SDK 调用。

Web 服务器的 .NET 启动代码是：

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>选择最佳操作

在服务器的**PersonalizerController.cs**中， **GenerateRank**服务器 API 汇总了调用排名 API 的准备工作

* `eventId`为排名调用创建新的
* 获取操作列表
* 从用户获取功能列表并创建上下文功能
* （可选）设置任何排除的操作
* 调用排名 API，将结果返回到客户端

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

发送到 Personalizer 的 JSON，其中包含操作（包含功能）和当前上下文功能，如下所示：

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>将 Personalizer rewardActionId 返回到客户端

排名 API 将所选的最佳操作**rewardActionId**返回到服务器。

显示**rewardActionId**中返回的操作。

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>客户端显示 rewardActionId 操作

在本教程中，将 `rewardActionId` 显示值。

在将来的应用程序中，可能会突出显示某些文本、按钮或网页上的部分。 对于任何分数分析后，将返回该列表，而不是内容的排序。 只 `rewardActionId` 应显示内容。

## <a name="reward-api-collect-information-for-reward"></a>奖励 API：收集奖励信息

应认真规划[奖励评分](concept-rewards.md)，就像计划功能一样。 奖励分数通常应为介于0和1之间的值。 基于业务逻辑和目标，_可以_在客户端应用程序中，根据用户行为，并在服务器上部分计算值。

如果服务器在 Personalizer 资源的 Azure 门户中配置的**奖励等待时间**内未调用奖励 API，则会为该事件使用**默认奖励**（也在 Azure 门户中配置）。

在此示例应用程序中，可以选择一个值，以查看该回报如何影响所选内容。

## <a name="additional-ways-to-learn-from-this-sample"></a>从此示例中学习的其他方法

此示例使用在 personalizer 资源的 Azure 门户中配置的多个基于时间的事件。 播放这些值，然后返回到此示例 web 应用以查看更改对排名和奖励调用的影响：

* 奖励等待时间
* 模型更新频率

要播放的其他设置包括：
* 默认奖励
* 浏览百分比


## <a name="clean-up-resources"></a>清理资源

完成本教程后，请清理以下资源：

* 删除示例项目目录。
* 删除你的 Personalizer 资源-将 Personalizer 资源视为专用于操作，并且仅当你仍使用食物作为操作使用者域时，才使用该资源。


## <a name="next-steps"></a>后续步骤
* [个性化体验创建服务的工作原理](how-personalizer-works.md)
* [功能](concepts-features.md)：了解有关使用带有操作和上下文的功能的概念
* [奖励](concept-rewards.md)：了解如何计算奖励