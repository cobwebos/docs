---
title: 在聊天机器人中使用个性化体验创建服务 - 个性化体验创建服务
description: 自定义具有个性化体验创建服务循环的 C# .NET 聊天机器人，以根据操作（包含特征）和上下文特征向用户提供正确的内容。
ms.topic: tutorial
ms.date: 07/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d95a6999186b964e59ff8f287d917b1f93e1813
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089881"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>教程：在 .NET 聊天机器人中使用个性化体验创建服务

使用具有个性化体验创建服务循环的 C# .NET 聊天机器人向用户提供正确的内容。 该聊天机器人向用户推荐特定的咖啡或茶。 用户可以接受或拒绝该推荐。 这将为个性化体验创建服务提供信息，有助于提出更合适的推荐。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 设置 Azure 资源
> * 配置并运行机器人
> * 使用 Bot Framework Emulator 与机器人交互
> * 了解机器人使用个性化体验创建服务的位置和方式


## <a name="how-does-the-chat-bot-work"></a>聊天机器人的工作原理是怎样的？

聊天机器人通常是与用户进行交互式对话。 该特定聊天机器人使用个性化体验创建服务选择最佳操作（咖啡或茶），然后向用户推荐。 个性化体验创建服务使用强化学习做出该选择。

聊天机器人需要管理对话轮次。 聊天机器人使用[机器人框架](https://github.com/microsoft/botframework-sdk)管理机器人体系结构和会话，并使用认知服务、[语言理解](../LUIS/index.yml) (LUIS) 来理解用户自然语言的意图。

聊天机器人是一个网站，其中包含用于回答请求的特定路由 `http://localhost:3978/api/messages`。 在本地开发机器人时，可以使用机器人模拟器以可视化方式与正在运行的聊天机器人进行交互。

### <a name="user-interactions-with-the-bot"></a>用户与机器人的交互

这是一个简单的聊天机器人，你可以向其输入文本查询。

|用户输入文本|机器人对文本做出响应|机器人为确定响应文本而执行的操作的说明|
|--|--|--|
|未输入文本 - 机器人开始对话。|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|机器人按指导性文本开始对话，并告知你上下文的内容：`Tuesday`、`Snowy`。|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|使用 LUIS 确定查询的意图，然后显示菜单选项，即咖啡和茶。 操作的特征如下 |
|`What do you suggest`|`How about Latte?`|使用 LUIS 确定查询的意图，然后调用“排名 API”，并将排名第一的选项作为提问 `How about {response.RewardActionId}?`。 此外，还将显示 JSON 调用和响应以便说明。|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|使用 LUIS 确定查询的意图，然后调用奖励为 `1`的“奖励 API”，显示 JSON 调用和响应以便说明。|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|使用 LUIS 确定查询的意图，然后调用奖励为 `0`的“奖励 API”，显示 JSON 调用和响应以便说明。|
|`Reset`|返回指导性文本。|使用 LUIS 确定查询的意图，然后显示指导性文本并重置上下文。|


### <a name="personalizer-in-this-bot"></a>该机器人中的个性化体验创建服务

该聊天机器人使用个性化体验创建服务根据一系列操作（某些类型的内容）和上下文特征选择排名第一的操作（特定的咖啡或茶）。

机器人将操作列表以及上下文特征发送到个性化体验创建服务循环。 个性化体验创建服务会将机器人显示的单个最佳操作返回给机器人。

在本教程中，操作为咖啡和茶类型：

|咖啡|茶|
|--|--|
|卡布奇诺<br>意大利浓咖啡<br>拿铁<br>穆哈咖啡|GreenTea<br>Rooibos|

**排名 API：** 为帮助个性化体验创建服务了解你的操作，该机器人会在每个排名 API 请求中发送以下内容：

* 操作（包含特征）
* 上下文特征

模型的“特征”是有关可以跨聊天机器人用户群的成员进行聚合（分组）的操作或上下文的信息。 特征不具有个体特定性（例如用户 ID）或高度特定性（例如一天中的准确时间）。

特征用于使模型中的操作与当前上下文保持协调。 该模型是个性化体验创建服务关于操作、上下文及其特征的过去所知信息的一种表示形式，这些信息使它能做出明智的决策。

模型（包括特征）根据 Azure 门户中的模型更新频率设置按计划进行更新。

所选特征的规划和设计应与技术体系结构中任何架构或模型中应用的规划和设计相同。 可以通过业务逻辑或第三方系统设置特征值。

> [!CAUTION]
> 该应用程序中的特征用于演示，不一定是 Web 应用中适用于你的用例的最佳特征。

#### <a name="action-features"></a>操作特征

每个操作（内容项）都有可帮助区分咖啡或茶项的特征。

这些特征未配置为 Azure 门户中的循环配置的一部分。 相反，它们在每个排名 API 调用中以 JSON 对象的形式发送。 这样，操作及其特征就可以灵活地随时间推移而增大、变化和缩小，这使得个性化体验创建服务能够顺应趋势。

咖啡和茶的特征包括以下内容：

* 咖啡豆的原产地，例如肯尼亚和巴西
* 咖啡或茶是否有机？
* 浅焙咖啡还是深焙咖啡

尽管上述列表中咖啡具有三个特征，但茶仅有一个。 只有将特征传递给个性化体验创建服务，操作才有意义。 如果空值不适用于操作，则请勿为特征传递空值。

#### <a name="context-features"></a>上下文特征

上下文特征可帮助个性化体验创建服务了解环境的上下文，例如显示器设备、用户、位置以及与用例相关的其他特征。

该聊天机器人的上下文包括以下内容：

* 天气类型（下雪、下雨、晴天）
* 星期几

在该聊天机器人中特征的选择是随机的。 在实际的机器人中，请为上下文特征采用真实数据。

### <a name="design-considerations-for-this-bot"></a>关于该机器人的设计注意事项

关于该会话，请注意以下事项：
* **机器人交互**：对话非常简单，因为它是在一个简单的用例中展示排名和奖励。 它并没有展示 Bot Framework SDK 或 Emulator 的全部功能。
* **个性化体验创建服务**：随机选择特征是为了模拟使用情况。 请勿在生产个性化体验创建服务方案中随机选择特征。
* **语言理解 (LUIS)** ：仅少量 LUIS 模型的示例言语适用于此示例。 请勿在生产 LUIS 应用程序中使用这么少的示例言语。


## <a name="install-required-software"></a>安装必需软件
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 如果你希望使用 .NET Core CLI，则可下载的示例存储库中包含说明。
- [Microsoft Bot Framework Emulator](https://aka.ms/botframeworkemulator) 是一个桌面应用程序，允许机器人开发人员对 localhost 上的或者通过隧道远程运行的机器人进行测试和调试。

## <a name="download-the-sample-code-of-the-chat-bot"></a>下载聊天机器人的示例代码

个性化体验创建服务示例存储库中提供了聊天机器人。 克隆或[下载](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip)存储库，然后使用 Visual Studio 2019 打开 `/samples/ChatbotExample` 目录中的示例。

若要克隆存储库，请在 Bash shell（终端）中使用以下 Git 命令。

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>创建并配置个性化体验创建服务和 LUIS 资源

### <a name="create-azure-resources"></a>创建 Azure 资源

若要使用该聊天机器人，需要为个性化体验创建服务和语言理解 (LUIS) 创建 Azure 资源。

* [创建 LUIS 资源](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal)。 在创建步骤中选择“两者”，因为同时需要创作和预测资源。
* [创建个性化体验创建服务资源](how-to-create-resource.md)，然后从 Azure 门户复制密钥和终结点。 你将需要在 .NET 项目的 `appsettings.json` 文件中设置这些值。

### <a name="create-luis-app"></a>创建 LUIS 应用

如果不熟悉 LUIS，则需要[登录](https://www.luis.ai)并立即迁移你的帐户。 无需新建资源，选择在本教程的上一节中创建的资源即可。

1. 若要新建 LUIS 应用程序，请在 [LUIS 门户](https://www.luis.ai)中选择你的订阅和创作资源。
1. 然后在该页面上，选择“+ 新建聊天应用”，然后选择“作为 JSON 导入” 。
1. 在弹出对话框中选择“选择文件”，然后选择 `/samples/ChatbotExample/CognitiveModels/coffeebot.json` 文件。 输入名称 `Personalizer Coffee bot`。
1. 在 LUIS 门户右上方的导航栏中，选择“训练”按钮。
1. 选择“发布”按钮，将应用发布到预测运行时的“生产槽” 。
1. 选择“管理”，然后选择“设置” 。 复制“应用 ID”的值。 你将需要在 .NET 项目的 `appsettings.json` 文件中设置该值。
1. 在“管理”部分中，选择“Azure 资源” 。 这将在应用上显示关联的资源。
1. 选择“添加预测资源”。 在弹出对话框中，依次选择你的订阅以及在本教程的上一节中创建的预测资源，然后选择“完成”。
1. 复制“主键”和“终结点 URL”的值 。 你将需要在 .NET 项目的 `appsettings.json` 文件中设置这些值。

### <a name="configure-bot-with-appsettingsjson-file"></a>使用 appsettings.json 文件配置机器人

1. 在 Visual Studio 2019 中打开聊天机器人解决方案文件 `ChatbotSamples.sln`。
1. 打开项目的根目录中的 `appsettings.json`。
1. 设置在本教程的上一节中复制的所有五个设置。

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>生成并运行机器人

配置 `appsettings.json` 后，即可生成并运行聊天机器人。 执行该操作后，浏览器会打开正在运行的网站 `http://localhost:3978`。

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="浏览器的屏幕截图，其中显示了聊天机器人网站。":::

由于本教程对机器人的操作进行了说明，因此请继续运行网站，以便与机器人交互。


## <a name="set-up-the-bot-emulator"></a>设置机器人模拟器

1. 打开 Bot Framework Emulator，然后选择“打开机器人”。

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="浏览器的屏幕截图，其中显示了聊天机器人网站。":::


1. 使用以下机器人 URL 配置机器人，然后选择“连接” ：

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="浏览器的屏幕截图，其中显示了聊天机器人网站。":::

    模拟器连接到聊天机器人，并显示说明文本以及有助于本地开发的日志记录和调试信息。

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="浏览器的屏幕截图，其中显示了聊天机器人网站。":::

## <a name="use-the-bot-in-the-bot-emulator"></a>在机器人模拟器中使用机器人

1. 通过输入 `I would like to see the menu` 要求查看菜单。 聊天机器人显示项。
1. 输入 `Please suggest a drink for me.`使机器人推荐项。模拟器在聊天窗口中显示排名请求和响应，因此你可以看到完整的 JSON。 机器人会提出推荐，例如 `How about Latte?`
1. 回答你想喝拿铁咖啡，即表示你接受个性化体验创建服务中排名第一的选择 `I like it.`。模拟器在聊天窗口中显示奖励分数为 1 的奖励请求和响应，因此你可以看到完整的 JSON。 机器人将做出以下响应：`That’s great! I’ll keep learning your preferences over time.` 和 `Would you like to get a new suggestion or reset the simulated context to a new day?`

    如果对所选内容做出 `no` 响应，则会向个性化体验创建服务发送奖励分数 0。


## <a name="understand-the-net-code-using-personalizer"></a>使用个性化体验创建服务了解 .NET 代码

.NET 解决方案是一种简单的机器人框架聊天机器人。 与个性化体验创建服务相关的代码位于以下文件夹：
* `/samples/ChatbotExample/Bots`
    * `PersonalizerChatbot.cs` 文件，用于机器人和个性化体验创建服务之间的交互
* `/samples/ChatbotExample/ReinforcementLearning` - 管理个性化体验创建服务模型的操作和特征
* `/samples/ChatbotExample/Model` - 用于个性化体验创建服务的操作和特征的文件，以及用于 LUIS 方法的文件

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs - 使用个性化体验创建服务

`PersonalizerChatbot` 类派生自 `Microsoft.Bot.Builder.ActivityHandler`。 它具有用于管理对话流的三种属性和方法。

> [!CAUTION]
> 请勿复制本教程中的代码。 使用[个性化体验创建服务示例存储库](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)中的示例代码。

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

前缀为 `Send` 的方法用于管理与机器人和 LUIS 的对话。 `ChooseRankAsync` 和 `RewardAsync` 方法可以与个性化体验创建服务进行交互。

#### <a name="calling-rank-api-and-display-results"></a>调用排名 API 并显示结果

`ChooseRankAsync` 方法通过收集具有特征和上下文特征的操作来生成 JSON 数据，然后发送到个性化体验创建服务的排名 API。

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>调用奖励 API 并显示结果

`RewardAsync` 方法通过确定分数来生成 JSON 数据，然后发送到个性化体验创建服务的奖励 API。 该分数是根据用户文本中标识的 LUIS 意图确定的，并且发送自 `OnTurnAsync` 方法。

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>关于机器人的设计注意事项

该示例旨在演示机器人中的个性化体验创建服务的简单端到端解决方案。 你的用例可能会更复杂。

如果打算在生产机器人中使用个性化体验创建服务，请计划以下内容：
* 每次需要经排名的选择时，请实时访问个性化体验创建服务。 不能对排名 API 进行批处理或缓存。  可以延迟奖励调用或将其脱机加载到独立进程，如果未在规定的时间内返回奖励，则将为活动设置默认奖励值。
* 基于用例的奖励计算：本示例显示了两个奖励值，0 和 1，没有中间值，也没有负值。 你的系统需要更精细的评分。
* 机器人通道：本示例使用单个通道，但如果你打算使用多个通道或单个通道上的其他机器人版本，则可能需要将其视为个性化体验创建服务模型的上下文特征的一部分。

## <a name="clean-up-resources"></a>清理资源

完成本教程后，请清理以下资源：

* 删除示例项目目录。
* 删除 Azure 门户中的个性化体验创建服务和 LUIS 资源。

## <a name="next-steps"></a>后续步骤
* [个性化体验创建服务的工作原理](how-personalizer-works.md)
* [特征](concepts-features.md)：了解有关与操作和上下文配合使用的特征的概念
* [奖励](concept-rewards.md)：了解如何计算奖励
