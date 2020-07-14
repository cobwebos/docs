---
title: 使用 Web 应用 - 个性化体验创建服务
description: 自定义具有个性化体验创建服务循环的 C# .NET Web 应用，以根据操作（包含特征）和上下文特征向用户提供正确的内容。
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: diberry
ms.openlocfilehash: 9514e92432c2be5441dec5ff998a9deede35d7f4
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207605"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>教程：向 .NET Web 应用添加个性化体验创建服务

自定义具有个性化体验创建服务循环的 C# .NET Web 应用，以根据操作（包含特征）和上下文特征向用户提供正确的内容。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 设置个性化体验创建服务密钥和终结点
> * 收集特征
> * 调用排名和奖励 API
> * 显示最佳操作，指定为 rewardActionId



## <a name="select-the-best-content-for-a-web-app"></a>选择 Web 应用的最佳内容

当网页上有需要个性化为一个最佳项 (rewardActionId) 以进行显示的操作（某些类型的内容）列表时，Web 应用应使用个性化体验创建服务。 操作列表的示例包括新闻文章、按钮放置位置和产品名称的用词选择。

需要将操作列表以及上下文特征发送到个性化体验创建服务循环。 个性化体验创建服务会选择一个最佳操作，然后 Web 应用将显示该操作。

在本教程中，操作为食物类型：

* 意大利面食
* 冰淇淋
* 果汁
* 沙拉
* 爆米花
* 咖啡
* 汤

为了帮助个性化体验创建服务了解你的操作，请在发送的每个排名 API 请求中包含操作（包含特征）和上下文特征 。

模型的“特征”是有关可以跨 Web 应用用户群的成员进行聚合（分组）的操作或上下文的信息。 特征不具有个体特定性（例如用户 ID）或高度特定性（例如一天中的准确时间）。

### <a name="actions-with-features"></a>操作（包含特征）

每个操作（内容项）都有可帮助区分食物项的特征。

这些特征未配置为 Azure 门户中的循环配置的一部分。 相反，它们在每个排名 API 调用中以 JSON 对象的形式发送。 这样，操作及其特征就可以灵活地随时间推移而增大、变化和缩小，这使得个性化体验创建服务能够顺应趋势。

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

上下文特征有助于个性化体验创建服务了解操作的上下文。 此示例应用程序的上下文包括：

* 当天时间 - 上午、下午、晚上、深夜
* 用户的口味偏好 - 咸、甜、苦、酸或香
* 浏览器上下文 - 用户代理、地理位置、引用者

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

## <a name="how-does-the-web-app-use-personalizer"></a>Web 应用如何使用个性化体验创建服务？

Web 应用使用个性化体验创建服务从食物选项列表中选择最佳操作。 它通过在每个排名 API 调用中发送以下信息来实现此操作：
* 操作及其特征，如 `taste` 和 `spiceLevel`
* 上下文特征，如当天 `time`、用户的 `taste` 偏好、浏览器的用户代理信息以及上下文特征
* 要排除的操作，例如“果汁”
* eventId，排名 API 的每个调用中的此内容都不同。

## <a name="personalizer-model-features-in-a-web-app"></a>Web 应用中的个性化体验创建服务模型特征

个性化体验创建服务需要操作（内容）和当前上下文（用户和环境）的特征。 特征用于使模型中的操作与当前上下文保持协调。 该模型是个性化体验创建服务关于操作、上下文及其特征的过去所知信息的一种表示形式，这些信息使它能做出明智的决策。

模型（包括特征）根据 Azure 门户中的模型更新频率设置按计划进行更新。

> [!CAUTION]
> 此应用程序中的特征旨在说明特征和特征值，但不一定是要在 Web 应用中使用的最佳特征。

### <a name="plan-for-features-and-their-values"></a>规划特征及其值

所选特征的规划和设计应与技术体系结构中任何架构或模型中应用的规划和设计相同。 可以通过业务逻辑或第三方系统设置特征值。 特征值不应具有高度特定性，否则将不会应用于一组或一类特征。

### <a name="generalize-feature-values"></a>通用化特征值

#### <a name="generalize-into-categories"></a>通用化为类别

此应用使用 `time` 作为特征，但将时间分组为不同的类别，如 `morning`、`afternoon`、`evening` 和 `night`。 上面就是不以高度特定的方式使用时间信息的示例（`10:05:01 UTC+2` 则是高度特定的时间）。

#### <a name="generalize-into-parts"></a>通用化为部分

此应用使用浏览器中的 HTTP 请求特征。 首先使用的是包含所有数据的非常具体的字符串，例如：

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

HttpRequestFeatures 类库将此字符串通用化为具有多个值的 userAgentInfo 对象 。 系统会将过于具体的任何值设置为空字符串。 发送请求的上下文特征时，其 JSON 格式如下：

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


## <a name="using-sample-web-app"></a>使用示例 Web 应用

基于浏览器的示例 Web 应用（其中提供所有代码）需要安装以下应用程序才能运行应用。

安装以下软件：

* [.NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) - 示例后端服务器使用 .NET Core
* [Node.js](https://nodejs.org/) - 客户端/前端依赖于此应用程序
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 或 [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/) - 使用 Visual Studio 2019 的开发人员环境或 .NET Core CLI 生成和运行应用

### <a name="set-up-the-sample"></a>设置示例
1. 克隆 Azure 个性化体验创建服务示例存储库。

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. 导航到 samples/HttpRequestFeatures 以打开解决方案 `HttpRequestFeaturesExample.sln`。

    如果出现请求，请允许 Visual Studio 更新个性化体验创建服务的 .NET 包。

### <a name="set-up-azure-personalizer-service"></a>设置 Azure 个性化体验创建服务

1. 在 Azure 门户中[创建个性化体验创建服务资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。

1. 在 Azure 门户的“密钥和终结点”选项卡中，找到 `Endpoint` 和 `Key1`/`Key2`（两者均可）。这是你的 `PersonalizerServiceEndpoint` 和 `PersonalizerApiKey`。
1. 填写 appsettings.json 中的 `PersonalizerServiceEndpoint`。
1. 按以下一种方式将 `PersonalizerApiKey` 配置为[应用机密](https://docs.microsoft.com/aspnet/core/security/app-secrets)：

    * 如果使用的是 .NET Core CLI，则可以使用 `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` 命令。
    * 如果使用的是 Visual Studio，则可以右键单击项目，然后选择“管理用户机密”菜单选项来配置个性化体验创建服务密钥。 执行此操作后，Visual Studio 将打开一个 `secrets.json` 文件，你可以在其中添加密钥，如下所示：

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>运行示例

用以下一种方法生成并运行 HttpRequestFeaturesExample：

* Visual Studio 2019：按 F5
* .NET Core CLI：依次运行 `dotnet build` 和 `dotnet run`

通过 Web 浏览器，可以发送排名请求和奖励请求并查看其响应，还可查看从你的环境中提取的 http 请求特征。

> [!div class="mx-imgBorder"]
> ![生成并运行 HTTPRequestFeaturesExample 项目。 此时将打开一个浏览器窗口，其中显示单页应用程序。](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>演示个性化体验创建服务循环

1. 选择“生成新排名请求”按钮，为排名 API 调用创建新的 JSON 对象。 这会创建操作（包含特征）和上下文特征并显示值，以便你可以查看 JSON 的外观。

    对于你自己将来的应用程序，客户端和/或服务器上可能会生成操作和特征，或具有对其他服务的调用。

1. 选择“发送排名请求”以将 JSON 对象发送到服务器。 服务器将调用个性化体验创建服务排名 API。 服务器将收到响应，并将排名在首位的操作返回到客户端进行显示。

1. 设置奖励值，然后选择“发送奖励请求”按钮。 如果不更改奖励值，客户端应用程序将始终向个性化体验创建服务发送值 `1`。

    > [!div class="mx-imgBorder"]
    > ![生成并运行 HTTPRequestFeaturesExample 项目。 此时将打开一个浏览器窗口，其中显示单页应用程序。](./media/tutorial-web-app/reward-score-api-call.png)

    对于你自己将来的应用程序，在收集客户端上的用户行为信息以及服务器上的业务逻辑后，可能会生成奖励分数。

## <a name="understand-the-sample-web-app"></a>了解示例 Web 应用

示例 Web 应用具有 C# .NET 服务器，该服务器管理特征集合，并发送和接收对个性化体验创建服务终结点的 HTTP 调用。

示例 Web 应用使用“挖空前端客户端应用程序”来捕获特征并处理用户界面操作，例如单击按钮以及将数据发送到 .NET 服务器。

以下部分介绍了开发人员在使用个性化体验创建服务时需要了解的服务器和客户端的各部分内容。

## <a name="rank-api-client-application-sends-context-to-server"></a>排名 API：客户端应用程序向服务器发送上下文

客户端应用程序收集用户的浏览器用户代理。

> [!div class="mx-imgBorder"]
> ![生成并运行 HTTPRequestFeaturesExample 项目。 此时将打开一个浏览器窗口，其中显示单页应用程序。](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>排名 API：服务器应用程序调用个性化体验创建服务

这是使用客户端应用程序的典型 .NET Web 应用，其中提供了很多样板文件代码。 系统将从以下代码段中删除不特定于个性化体验创建服务的任何代码，以便你可以专注于特定于个性化体验创建服务的代码。

### <a name="create-personalizer-client"></a>创建个性化体验创建服务客户端

在服务器的 Startup.cs 中，将使用个性化体验创建服务终结点和密钥来创建个性化体验创建服务客户端。 客户端应用程序不需要与此应用中的个性化体验创建服务进行通信，而是依赖于服务器执行这些 SDK 调用。

Web 服务器的 .NET 启动代码为：

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

在服务器的 PersonalizerController.cs 中，GenerateRank 服务器 API 汇总了调用排名 API 的准备工作 

* 为排名调用创建新 `eventId`
* 获取操作列表
* 从用户处获取特征列表并创建上下文特征
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

发送到个性化体验创建服务的 JSON，其中包含操作（包含特征）和当前上下文特征，如下所示：

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

### <a name="return-personalizer-rewardactionid-to-client"></a>将个性化体验创建服务 rewardActionId 返回到客户端

排名 API 会将所选的最佳操作 rewardActionId 返回到服务器。

显示 rewardActionId 中返回的操作。

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

在本教程中，显示的值为 `rewardActionId`。

在你自己将来的应用程序中，可能会突出显示确切文本、按钮或网页上的一部分。 系统将返回此列表以用于对分数进行后期分析，而不是对内容进行排序。 仅应显示 `rewardActionId` 内容。

## <a name="reward-api-collect-information-for-reward"></a>奖励 API：收集奖励信息

应仔细规划[奖励分数](concept-rewards.md)，就像规划特征一样。 奖励分数通常应为 0 和 1 之间的值。 该值可能在客户端应用程序中基于用户行为进行部分计算，并在服务器上基于业务逻辑和目标进行部分计算。

如果服务器未在 Azure 门户中为个性化体验创建服务资源配置的“奖励等待时间”内调用奖励 API，则系统会对该事件使用“默认奖励”（也在 Azure 门户中配置） 。

在此示例应用程序中，可以选择一个值来查看奖励对所选内容的影响。

## <a name="additional-ways-to-learn-from-this-sample"></a>可从此示例中学到的其他方法

此示例使用 Azure 门户中为个性化体验创建服务资源配置的多个基于时间的事件。 试用这些值，然后返回到此示例 Web 应用，以查看更改对排名和奖励调用的影响：

* 奖励等待时间
* 模型更新频率

要试用的其他设置包括：
* 默认奖励
* 浏览百分比


## <a name="clean-up-resources"></a>清理资源

完成本教程后，请清理以下资源：

* 删除示例项目目录。
* 删除个性化体验创建服务资源 - 将个性化体验创建服务资源视为专用于操作和上下文，并且仅当你仍使用食物作为操作主体域时，才重复使用该资源。


## <a name="next-steps"></a>后续步骤
* [个性化体验创建服务的工作原理](how-personalizer-works.md)
* [特征](concepts-features.md)：了解有关与操作和上下文配合使用的特征的概念
* [奖励](concept-rewards.md)：了解如何计算奖励
