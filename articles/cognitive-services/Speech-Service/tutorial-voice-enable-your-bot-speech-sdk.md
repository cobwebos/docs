---
title: 教程：语音启用机器人使用语音 SDK-语音服务
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将使用 Microsoft Bot Framework 创建一个回显机器人，将其部署到 Azure，并将其注册到机器人框架直接线路语音通道。 然后，你将配置一个适用于 Windows 的示例客户端应用程序，该应用程序可让你与机器人联系，并听到它回复你。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: trbye
ms.openlocfilehash: c55d81db848dcb1aebe9dacb03387565b3d8db48
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745613"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>教程：使用语音 SDK 实现机器人的语音

你现在可以使用语音服务的强大功能轻松地语音启用聊天机器人。

在本教程中，你将使用 Microsoft Bot Framework 创建一个回显机器人，将其部署到 Azure，并将其注册到机器人框架直接线路语音通道。 然后，你将配置一个适用于 Windows 的示例客户端应用程序，该应用程序可让你与机器人联系，并听到它回复你。

本教程适用于刚开始使用 Azure、Bot 框架 bot、直行语音或语音 SDK 的开发人员，并且想要使用有限的编码快速构建工作系统。 不需要经验或对这些服务的熟悉。

完成本练习后，你将设置一个系统，该系统将按如下所示操作：

1. 示例客户端应用程序已配置为连接到直接线路语音通道和 Echo 机器人
1. 按下按钮时，将从默认的麦克风记录音频（如果激活自定义关键字，则会连续录制）
1. （可选）自定义关键字检测发生，将音频流式传输到云
1. 使用语音 SDK，应用连接到直接线路语音通道并流式传输音频
1. （可选）在服务上进行更高准确性关键字验证
1. 音频会传递到语音识别服务，并转录到文本
1. 识别的文本将作为 Bot 框架活动传递到 Echo 机器人 
1. 文本语音转换（TTS）服务将响应文本转为音频，并流式传输回客户端应用程序以便播放

![关系图-标记](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "语音通道流")

> [!NOTE]
> 本教程中的步骤不需要付费服务。 作为新的 Azure 用户，你将能够使用免费的 Azure 试用版订阅和语音服务的免费层来完成本教程。

本教程的内容：
> [!div class="checklist"]
> * 创建新的 Azure 资源
> * 生成、测试 Echo 机器人示例并将其部署到 Azure App Service
> * 向直接线路语音频道注册机器人
> * 生成并运行 Windows 语音助手客户端以与 Echo Bot 交互
> * 添加自定义关键字激活
> * 了解如何更改识别语音的语言

## <a name="prerequisites"></a>先决条件

下面是完成本教程所需的操作：

- 带有工作麦克风和扬声器（或耳机）的 Windows 10 PC
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)或更高版本
- [.NET Framework 运行时 4.6.1](https://dotnet.microsoft.com/download)或更高版本
- 一个 Azure 帐户。 [免费注册](https://azure.microsoft.com/free/ai/)。
- 一个[GitHub](https://github.com/)帐户
- [适用于 Windows 的 Git](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>创建资源组

你将在本教程中创建的客户端应用使用少量的 Azure 服务。 若要减少来自机器人的响应的往返时间，需要确保这些服务位于同一 Azure 区域。 在本部分中，你将在**美国西部**区域创建一个资源组。 此资源组将在为 Bot 框架、直接线路语音通道和语音服务创建单个资源时使用。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">创建资源组<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. 系统将提示你提供一些信息：
   * 将 "**订阅**" 设置为 "**免费试用**" （也可以使用现有订阅）。
   * 输入**资源组**的名称。 建议**SpeechEchoBotTutorial**。
   * 从 "**区域**" 下拉箭头中，选择 "**美国西部**"。
1. 单击“审阅并创建”****。 应该会看到一个横幅，其中显示了已**通过的验证**。
1. 单击**创建**。 创建资源组可能需要几分钟的时间。
1. 与稍后在本教程中创建的资源一样，最好将此资源组固定到仪表板以便于访问。 如果要固定此资源组，请单击仪表板右上角的 "固定" 图标。

### <a name="choosing-an-azure-region"></a>选择 Azure 区域

如果要在本教程中使用其他区域，这些因素可能会限制你的选择：

* 确保使用[受支持的 Azure 区域](regions.md#voice-assistants)。
* 直接连线语音频道使用文本到语音服务，该服务具有标准和神经声音。 神经声音[仅限于特定的 Azure 区域](regions.md#standard-and-neural-voices)。
* 免费试用密钥可限制为特定区域。

有关区域的详细信息，请参阅[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。

## <a name="create-resources"></a>创建资源

现在，在受支持的区域中拥有资源组，下一步是为要在本教程中使用的每个服务创建单独的资源。

### <a name="create-a-speech-service-resource"></a>创建语音服务资源

按照以下说明创建语音资源：

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">创建语音服务资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 系统将提示你提供一些信息：
   * 为资源指定**名称**。 建议**SpeechEchoBotTutorial**
   * 对于 "**订阅**"，请确保选择 "**免费试用**"。
   * 对于 "**位置**"，请选择 "**美国西部**"。
   * 对于 "**定价层**"，请选择**F0**。 这是免费级别。
   * 对于 "**资源组**"，选择 " **SpeechEchoBotTutorial**"。
5. 输入所有所需信息后，单击 "**创建**"。 创建资源可能需要几分钟时间。
6. 稍后在本教程中，你将需要此服务的订阅密钥。 你可以随时从资源的**概述**（管理密钥）或**密钥**访问这些密钥。

此时，请检查资源组（**SpeechEchoBotTutorial**）是否有语音资源：

| 名称 | 类型  | 位置 |
|------|-------|----------|
| SpeechEchoBotTutorial-语音 | 认知服务 | 美国西部 |

### <a name="create-an-azure-app-service-plan"></a>创建 Azure 应用服务计划

下一步是创建应用服务计划。 应用服务计划为要运行的 Web 应用定义一组计算资源。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">创建 Azure App Service 计划<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 系统将提示你提供一些信息：
   * 将 "**订阅**" 设置为 "**免费试用**" （也可以使用现有订阅）。
   * 对于 "**资源组**"，选择 " **SpeechEchoBotTutorial**"。
   * 为资源指定**名称**。 建议**SpeechEchoBotTutorial-AppServicePlan**
   * 对于 "**操作系统**"，选择 " **Windows**"。
   * 对于 "**区域**"，选择 "**美国西部**"。
   * 对于 "**定价层**"，请确保选择 "**标准 S1** "。 这应该是默认值。 如果不是这样，请确保将**操作系统**设置为**Windows** ，如上所述。
5. 单击“审阅并创建”****。 应该会看到一个横幅，其中显示了已**通过的验证**。
6. 单击**创建**。 创建资源组可能需要几分钟的时间。

此时，请检查资源组（**SpeechEchoBotTutorial**）是否有两个资源：

| 名称 | 类型  | 位置 |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | 应用服务计划 | 美国西部 |
| SpeechEchoBotTutorial-语音 | 认知服务 | 美国西部 |

## <a name="build-an-echo-bot"></a>构建 Echo Bot

现在，你已创建了一些资源，接下来让我们来构建机器人。 接下来，我们将开始介绍回响机器人示例，如名称所示，将输入的文本回显为其响应。 不用担心，示例代码已准备就绪，无需进行任何更改。 它配置为使用直接线路语音频道，我们将在我们将机器人部署到 Azure 后进行连接。

> [!NOTE]
> [GitHub 上的示例自述文件](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)中提供了后面的说明以及有关 Echo Bot 的其他信息。

### <a name="run-the-bot-sample-on-your-machine"></a>在计算机上运行机器人示例

1. 克隆示例存储库：

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. 启动 Visual Studio。
3. 从工具栏中，选择 "**文件**" "  >  **打开**  >  **项目/解决方案**"，然后打开 Echo 机器人项目解决方案：

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. 加载项目后，按<kbd>F5</kbd>生成并运行项目。
5. 此时会启动浏览器，此时会看到与此类似的屏幕。
    > [!div class="mx-imgBorder"]
    > [![echobot-在本地主机上运行](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "在 localhost 上运行的 EchoBot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>用 Bot Framework 模拟器测试机器人示例

[机器人框架模拟器](https://github.com/microsoft/botframework-emulator)是一个桌面应用，允许机器人开发人员通过隧道本地或远程测试和调试 bot。 模拟器支持键入的文本（非语音）。 机器人将响应文本。 按照以下步骤操作，使用机器人框架模拟器测试在本地运行的、带有文本输入和文本输出的回显机器人。 将机器人部署到 Azure 后，我们将通过语音输入和语音输出对其进行测试。

1. 安装[机器人 Framework 模拟器](https://github.com/Microsoft/BotFramework-Emulator/releases/latest)版本4.3.0 或更高版本
2. 启动 Bot Framework 模拟器并打开机器人：
   * **文件**  -> **打开机器人**。
3. 输入机器人的 URL。 例如：

   ```
   http://localhost:3978/api/messages
   ```
   然后按 "连接"。
4. 机器人应立即 fibonacci 你 "Hello and 欢迎！" 消息。 键入任何短信，确认从机器人获得响应。
5. 与 Echo Bot 实例通信的交换方式如下： [![机器人-框架模拟器](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "机器人框架模拟器")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>将机器人部署到 Azure App Service

下一步是将回显机器人部署到 Azure。 可以通过多种方法来部署机器人，但在本教程中，我们将重点介绍如何直接从 Visual Studio 进行发布。

> [!NOTE]
> 或者，你可以使用[Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli)和[部署模板](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/03.core-bot)部署机器人。

1. 在 Visual Studio 中，打开已配置为与直接线路语音通道一起使用的回响机器人：

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. 在**解决方案资源管理器**中，右键单击**EchoBot**项目，然后选择 "**发布 ...** "
1. 将打开一个名为 "**选取" 的**新窗口。
1. 从**Azure 服务**导航中选择 "**应用服务**"，选择 "**新建**"，然后单击 "**创建配置文件**"。
1. "**创建应用服务**" 窗口出现时：
   * 单击 "**添加帐户**"，然后使用 Azure 帐户凭据登录。 如果已登录，请从下拉列表中选择所需的帐户。
   * 对于**应用程序名称**，需要输入机器人的全局唯一名称。 此名称用于创建唯一的机器人 URL。 将填充默认值，包括日期和时间（例如： "EchoBot20190805125647"）。 您可以使用本教程的默认名称。
   * 对于 "**订阅**"，将其设置为 "**免费试用**"
   * 对于 "**资源组**"，选择**SpeechEchoBotTutorial**
   * 对于**托管计划**，请选择**SpeechEchoBotTutorial-AppServicePlan**
   * 对于**Application Insights**，请将其保留为 "**无**"
1. 单击“创建” 
1. 在 Visual Studio 中应会看到如下所示的成功消息：

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. 默认浏览器应打开并显示一个页面，其中显示 "你的机器人已就绪！"。
1. 此时，请在 Azure 门户中检查资源组**SpeechEchoBotTutorial** ，并确认有三个资源：

| 名称 | 类型  | 位置 |
|------|-------|----------|
| EchoBot20190805125647 | 应用服务 | 美国西部 |
| SpeechEchoBotTutorial-AppServicePlan | 应用服务计划 | 美国西部 |
| SpeechEchoBotTutorial-语音 | 认知服务 | 美国西部 |

## <a name="enable-web-sockets"></a>启用 Web 套接字

需要进行较小的配置更改，使机器人能够使用 web 套接字与直接线路语音通道通信。 按照以下步骤启用 web 套接字：

1. 导航到[Azure 门户](https://portal.azure.com)，找到应用服务。 资源应命名为类似于**EchoBot20190805125647** （唯一的应用名称）。
2. 在**Azure 服务**导航中的 "**设置**" 下，单击 "**配置**"。
3. 选择 "**常规设置**" 选项卡。
4. 找到 " **Web 套接字**" 的切换，并将其设置为 **"开**"。
5. 单击“保存” 。

> [!TIP]
> 您可以使用 Azure App Service 页顶部的控件来停止或重新启动该服务。 故障排除时，这可能很方便。

## <a name="create-a-channel-registration"></a>创建频道注册

创建用于托管机器人的 Azure App Service 后，下一步就是创建**机器人频道注册**。 创建频道注册是将机器人注册到机器人框架通道（包括直接线路语音通道）的先决条件。

> [!NOTE]
> 如果要了解有关 bot 如何利用频道的详细信息，请参阅[将 Bot 连接到通道](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)。


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">创建 Azure 机器人频道注册<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. 系统将提示你提供一些信息：
   * 对于**机器人句柄**，请输入**SpeechEchoBotTutorial-BotRegistration**。
   * 对于 "**订阅**"，请选择 "**免费试用**"。
   * 对于 "**资源组**"，选择 " **SpeechEchoBotTutorial**"。
   * 对于 "**位置**"，请选择 "**美国西部**"。
     * 对于 "**定价层**"，请选择**F0**。
     * 对于 "**消息传递终结点**"，请输入 web 应用的 URL， `/api/messages` 路径后面追加了路径。 例如：如果全局唯一的应用名称为**EchoBot20190805125647**，则消息传递终结点将为： `https://EchoBot20190805125647.azurewebsites.net/api/messages/` 。
     * 对于**application insights**，你可以将其设置为**Off**。 有关详细信息，请参阅[机器人分析](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0)。
     * 忽略**自动创建应用 ID 和密码**。
5. 在 "**机器人频道注册**" 边栏选项卡底部，单击 "**创建**"。

此时，请查看 Azure 门户中的资源组**SpeechEchoBotTutorial** 。 它现在应显示四个资源：

| 名称 | 类型  | 位置 |
|------|-------|----------|
| EchoBot20190805125647 | 应用服务 | 美国西部 |
| SpeechEchoBotTutorial-AppServicePlan | 应用服务计划 | 美国西部 |
| SpeechEchoBotTutorial-BotRegistration | 机器人频道注册 | 全局 |
| SpeechEchoBotTutorial-语音 | 认知服务 | 美国西部 |

> [!IMPORTANT]
> 即使你选择了 "美国西部"，Bot 通道注册资源也会显示全局区域。 这是正常情况。

## <a name="register-the-direct-line-speech-channel"></a>注册直接连线语音通道

现在是时候将机器人注册到了直接线路语音通道。 此通道用于在回显 bot 和使用语音 SDK 编译的客户端应用之间创建连接。

1. 在[Azure 门户](https://portal.azure.com)中找到并打开**SpeechEchoBotTutorial-BotRegistration**资源。
1. 在**Azure 服务**导航中，选择 "**通道**"。
   * 查找**更多通道**，找到并单击 "**直接连线语音**"。
   * 查看标题为 "**配置直接线路语音**" 的页面上的文本，然后展开标记为 "认知服务帐户" 的下拉菜单。
   * 从菜单中选择先前创建的语音资源（例如**SpeechEchoBotTutorial**），将机器人关联到语音订阅密钥。
   * 单击“保存” 。

1. 在**机器人管理**导航中，单击 "**设置**"。
   * 选中标记为**启用流式处理终结点**的框。 这是在机器人和直接线路语音通道之间启用基于 web 套接字构建的通信协议所必需的。
   * 单击“保存” 。

> [!TIP]
> 若要了解详细信息，请参阅[连接机器人以定向语音](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)。 此页包含其他信息和已知问题。

## <a name="run-the-windows-voice-assistant-client"></a>运行 Windows 语音助手客户端

在此步骤中，将运行 Windows 语音助手客户端。 客户端是 c # 中的 Windows Presentation Foundation （WPF）应用程序，该应用程序使用[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk)来管理与机器人的通信。 在编写自定义客户端应用之前，可以使用它来与机器人进行交互和测试。 它是开源的，因此您可以下载并运行可执行文件，也可以自行生成。

Windows 语音助手客户端具有一个简单的 UI，可用于配置与机器人的连接、查看文本对话、以 JSON 格式查看 Bot 框架活动，以及显示自适应卡。 它还支持使用自定义关键字。 你将使用此客户端与机器人对话，并收到语音回复。

在继续之前，请确保麦克风和扬声器已启用且正常工作。

1. 导航至[Windows 语音助手客户端](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)的 GitHub 存储库。
1. 按照此处提供的说明进行操作
   * 下载包含要运行的可执行文件的 ZIP 包，或
   * 通过克隆存储库和生成项目来自行生成可执行文件。

1. 启动客户端应用程序并对其进行配置。
1. 单击 "**重新连接**"，确保看到消息**按下麦克风按钮，或键入即可开始与机器人通信**。
1. 让我们对它进行测试。单击麦克风按钮，并口述几个英语单词。 将显示已识别的文本。 完成此操作后，bot 将以自己的语音进行回复，并显示 "echo" 后跟识别的单词。
1. 你还可以使用文本与机器人通信。 只需在底部栏中键入文本。 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>排查 Windows 语音助手客户端中的错误

如果在主应用窗口中收到错误消息，请使用此表来确定并解决该错误：

| 错误 | 该怎么办？ |
|-------|----------------------|
|错误 AuthenticationFailure： WebSocket 升级失败，出现身份验证错误（401）。 检查正确的订阅密钥（或授权令牌）和区域名称| 在应用的 "设置" 页中，确保已正确输入语音订阅密钥及其区域。<br>请确保正确输入了语音密钥和密钥区域。 |
|错误 ConnectionFailure：远程主机关闭了连接。 错误代码：1011。 错误详细信息：无法在发送消息之前连接到机器人 | 请确保[已选中 "启用流式处理终结点"](#register-the-direct-line-speech-channel)框和/或将[ **Web 套接字**切换](#enable-web-sockets)到 "打开"。<br>请确保 Azure App Service 正在运行。 如果已启动，请尝试重新启动应用服务。|
|错误 ConnectionFailure：远程主机关闭了连接。 错误代码：1011。 错误详细信息：响应状态代码不指示成功：500（InternalServerError）| 机器人在其输出活动的 "[朗读](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)" 字段中指定了一个神经声音，但与您的语音订阅密钥关联的 Azure 区域不支持神经语音。 请参阅[标准和神经声音](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)。|
|错误 ConnectionFailure：远程主机关闭了连接。 错误代码：1000。 错误详细信息：超出最大 web 套接字连接空闲持续时间（> 300000 ms）| 如果到通道的连接处于打开状态且处于非活动状态的时间超过五分钟，则会出现此错误。 |

如果未在表中解决问题，请参阅[语音助手：常见问题解答](faq-voice-assistants.md)。

### <a name="view-bot-activities"></a>查看机器人活动

每个机器人发送和接收**活动**消息。 在 Windows 语音助手客户端的 "**活动日志**" 窗口中，你将看到带时间戳的日志，其中包含客户端从机器人接收的每个活动。 还可以查看客户端使用方法发送到机器人的活动 [`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync) 。 选择日志项时，它会将关联活动的详细信息显示为 JSON。

下面是客户端收到的活动的示例 json：

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

若要详细了解 JSON 输出中返回的内容，请参阅[活动中的字段](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)。 出于本教程的目的，您可以将重点放在[文本](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text)和[讲述](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)字段上。

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>查看调用语音 SDK 的客户端源代码

Windows 语音助手客户端使用包含语音 SDK 的 NuGet 包[cognitiveservices account。](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) 开始查看示例代码的一个好方法是在文件中使用 InitSpeechConnector （）方法 [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) ，该方法可创建这两个语音 SDK 对象：
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)-对于配置设置（例如，语音订阅密钥、密钥区域）
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)-管理通道连接和客户端订阅事件，以便处理识别的语音和机器人响应。

## <a name="add-custom-keyword-activation"></a>添加自定义关键字激活

语音 SDK 支持自定义关键字激活。 与 Microsoft 助手的 "你好 Cortana" 类似，你可以编写一个应用，该应用会持续侦听你选择的关键字。 请记住，关键字可以是单个词，也可以是多单词短语。

> [!NOTE]
> 术语*关键字*通常与字词*唤醒词*互换使用，你可能会看到在 Microsoft 文档中使用了这两项。

关键字检测在客户端应用上完成。 如果使用关键字，则只有在检测到关键字时才会将音频流式传输到直接语音通道。 直接连线语音频道包括一个名为*关键字验证（KWV）* 的组件，该组件在云中执行更复杂的处理，以验证所选关键字是否位于音频流的开头。 如果关键字 word 验证成功，则通道将与机器人通信。

按照以下步骤创建关键字模型，将 Windows 语音助手客户端配置为使用此模型，最后使用机器人进行测试。

1. 按照这些说明[使用语音服务创建自定义关键字](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)。
2. 解压缩在上一步中下载的模型文件。 应为关键字命名。 正在查找名为的文件 `kws.table` 。
3. 在 Windows 语音助手客户端中，找到 "**设置**" 菜单（查找右上方的齿轮图标）。 查找 "**模型文件路径**" 并输入 `kws.table` 步骤2中的文件的完整路径名称。
4. 确保选中标记为 "**已启用**" 的框。 在复选框旁边应会看到此消息： "下次连接时将侦听关键字"。 如果提供了错误的文件或路径无效，应会看到一条错误消息。
5. 输入语音**订阅密钥**、**订阅密钥区域**，然后单击 **"确定"** 以关闭 "**设置**" 菜单。
6. 单击 "**重新连接**"。 应该会看到一条消息，其中显示： "新会话已启动-类型，按下麦克风按钮，或说关键字为"。 应用现在正在持续侦听。
7. 说任何以关键字开头的短语。 例如： "**{关键字}**，它的时间是多少？"。 Uttering 关键字后，不需要暂停。 完成后，会发生两种情况：
   * 你会看到你的辐射
   * 不久之后，你会听到机器人的响应
8. 继续试验机器人支持的三个输入类型：
   * 键入文本在底部栏中
   * 按下麦克风图标和讲话
   * 说以关键字开头的短语

### <a name="view-the-source-code-that-enables-keyword"></a>查看启用关键字的源代码

在 Windows 语音助手客户端源代码中，查看以下文件以查看用于启用关键字检测的代码：

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs)包括对语音 SDK 方法的调用 [`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-) ，该方法用于实例化磁盘上的本地文件中的模型。
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)包括对语音 SDK 方法的调用 [`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync) ，该方法激活连续关键字检测。

## <a name="optional-change-the-language-and-bot-voice"></a>可有可无更改语言和机器人声音

你创建的机器人将侦听并响应英语，其中默认为美国英语文本语音。 但是，你并不局限于使用英语或默认语音。 在本部分中，你将了解如何更改机器人侦听和响应的语言。 你还将了解如何为该语言选择其他语音。

### <a name="change-the-language"></a>更改语言

你可以从 "[语音到文本](language-support.md#speech-to-text)" 表中所述的任何一种语言中进行选择。 在下面的示例中，我们将语言更改为德语。

1. 打开 Windows 语音助手客户端应用程序，单击 "设置" 按钮（右上角的齿轮图标），并 `de-de` 在 "语言" 字段中输入（这是 "[语音到文本](language-support.md#speech-to-text)" 表中提到的 "区域设置" 值）。 这将设置要识别的口述语言，并覆盖默认语言 `en-us` 。 这也指示 "直接线路语音频道" 使用默认的德语语音进行机器人回复。
2. 关闭 "设置" 页，然后单击 "重新连接" 按钮，建立与 echo bot 的新连接。
3. 单击麦克风按钮，并口述德语短语。 你将看到已识别的文本，并且 echo bot 将用默认的德语语音进行回复。

### <a name="change-the-default-bot-voice"></a>更改默认机器人语音

如果机器人以[语音合成标记语言](speech-synthesis-markup.md)（SSML）而不是简单文本的形式指定答复，则可以选择文本到语音声音并控制发音。 Echo bot 不使用 SSML，但我们可以轻松修改代码来执行此操作。 在下面的示例中，我们将 SSML 添加到回响机器人回复，以便使用德语语音 Stefan Apollo （一种男语音）而不是默认的女性声音。 请参阅你的语言支持的[标准语音](language-support.md#standard-voices)和[神经声音](language-support.md#neural-voices)列表。

1. 首先打开 `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs` 。
2. 找到以下两行：
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. 将它们替换为：
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. 在 Visual Studio 中生成解决方案并修复任何生成错误。

方法 "MessageFactory" 中的第二个参数设置了机器人回复中的[活动讲述字段](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)。 进行上述更改后，已将其从简单文本替换为 SSML，以指定非默认的德语语音。

### <a name="redeploy-your-bot"></a>重新部署机器人

完成机器人的必要更改后，下一步就是将其重新发布到 Azure App Service 并试用：

1. 在 "解决方案资源管理器" 窗口中，右键单击**EchoBot**项目，然后选择 "**发布**"。
2. 以前的部署配置已作为默认值加载。 只需单击 "EchoBot20190805125647" 旁边**Web 部署的**"**发布**"。
3. "**发布成功**" 消息将显示在 Visual Studio 的 "输出" 窗口中，并且网页将启动并显示消息 "机器人已就绪！"。
4. 打开 Windows 语音助手客户端应用程序，单击 "设置" 按钮（右上角的齿轮图标），并确保 `de-de` 在 "语言" 字段中仍有。
5. 按照[运行 Windows 语音助手客户端](#run-the-windows-voice-assistant-client)中的说明重新连接到你的新部署的机器人，并以新语言讲述，并听到你用新的语音回复该语言的机器人。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用本教程中部署的 echo 机器人，只需删除 Azure 资源组**SpeechEchoBotTutorial**，即可将其删除及其关联的所有 Azure 资源。

1. 从 " [Azure 门户](https://portal.azure.com)中，单击" **Azure 服务**"导航中的"**资源组**"。
2. 找到名为 " **SpeechEchoBotTutorial**" 的资源组。 单击三个点（...）。
3. 选择“删除资源组”  。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过语音 SDK 构建你自己的客户端应用](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>另请参阅

* 部署到[附近的 Azure 区域](https://azure.microsoft.com/global-infrastructure/locations/)，查看机器人响应时间改进
* 部署到[支持高质量神经 TTS 声音的 Azure 区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* 与直接线路语音通道关联的定价：
  * [机器人服务定价](https://azure.microsoft.com/pricing/details/bot-service/)
  * [语音服务](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* 构建和部署自己的支持语音的机器人：
  * 构建[机器人框架机器人](https://dev.botframework.com/)。 将其注册为[直接线路语音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)并[自定义用于语音的机器人](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * 探索现有[机器人框架解决方案](https://microsoft.github.io/botframework-solutions/index)：构建[虚拟助手](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/)并[将其扩展为直接行语音](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
