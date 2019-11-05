---
title: 教程：使用语音 SDK 语音启用机器人
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将使用 Microsoft Bot Framework 创建一个回显机器人，将其部署到 Azure，并将其注册到机器人框架直接线路语音通道。 然后，你将配置一个适用于 Windows 的示例客户端应用程序，该应用程序可让你与机器人联系，并听到它回复你。
services: cognitive-services
author: dargilco
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: dcohen
ms.openlocfilehash: 89bf4a3a6b8ea0cb04f3a1a663cc2365fa4fefc3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468689"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>教程：使用语音 SDK 实现机器人的语音

你现在可以使用语音服务的强大功能轻松地语音启用聊天机器人。

在本教程中，你将使用 Microsoft Bot Framework 创建一个回显机器人，将其部署到 Azure，并将其注册到机器人框架直接线路语音通道。 然后，你将配置一个适用于 Windows 的示例客户端应用程序，该应用程序可让你与机器人联系，并听到它回复你。

本教程适用于刚开始使用 Azure、Bot 框架 bot、直行语音或语音 SDK 的开发人员，并且想要使用有限的编码快速构建工作系统。 不需要经验或对这些服务的熟悉。

完成本练习后，你将设置一个系统，该系统将按如下所示操作：

1. 示例客户端应用程序已配置为连接到直接线路语音通道和 Echo 机器人
2. 按下按钮时，将从默认的麦克风记录音频（如果激活自定义关键字，则会连续录制）
3. （可选）自定义关键字检测发生，将音频流式传输到云
4. 使用语音 SDK，应用连接到直接线路语音通道并流式传输音频
5. （可选）在服务上进行更高准确性关键字验证
6. 音频会传递到语音识别服务，并转录到文本
7. 识别的文本将作为 Bot 框架活动传递到 Echo 机器人 
8. 文本语音转换（TTS）服务将响应文本转为音频，并流式传输回客户端应用程序以便播放

![关系图-标记](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "语音通道流")

> [!NOTE]
> 本教程中的步骤不需要付费服务。 作为新的 Azure 用户，你将能够使用免费的 Azure 跟踪订阅和免费的语音服务层来完成本教程。

本教程的内容：
> [!div class="checklist"]
> * 创建新的 Azure 资源
> * 生成、测试 Echo 机器人示例并将其部署到 Azure App Service
> * 向直接线路语音频道注册机器人
> * 构建并运行直接连线语音客户端以与 Echo Bot 交互
> * 添加自定义关键字激活
> * 了解如何更改识别语音的语言

## <a name="prerequisites"></a>必备组件

下面是完成本教程所需的操作：

- 带有工作麦克风和扬声器（或耳机）的 Windows 10 PC
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)或更高版本
- [.NET Core SDK](https://dotnet.microsoft.com/download)版本2.1 或更高版本
- 一个 Azure 帐户。 [免费注册](https://azure.microsoft.com/free/ai/)。
- 一个 [GitHub](https://github.com/) 帐户
- [Git for Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>创建资源组

你将在本教程中创建的客户端应用使用少量的 Azure 服务。 若要减少来自机器人的响应的往返时间，需要确保这些服务位于同一 Azure 区域。 在本部分中，你将在**美国西部**区域创建一个资源组。 此资源组将在为 Bot 框架、直接线路语音通道和语音服务创建单个资源时使用。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 从左侧导航栏中选择 "**资源组**"。 然后单击 "**添加**" 以添加新的资源组。
3. 系统将提示你提供一些信息：
   * 将 "**订阅**" 设置为 "**免费试用**" （也可以使用现有订阅）。
   * 输入**资源组**的名称。 建议**SpeechEchoBotTutorial**。
   * 从 "**区域**" 下拉箭头中，选择 "**美国西部**"。
4. 单击 "**查看和创建**"。 应该会看到一个横幅，其中显示了已**通过的验证**。
5. 单击“创建”。 创建资源组可能需要几分钟的时间。
6. 与稍后在本教程中创建的资源一样，最好将此资源组固定到仪表板以便于访问。 如果要固定此资源组，请单击仪表板右上角的 "固定" 图标。

### <a name="choosing-an-azure-region"></a>选择 Azure 区域

如果要在本教程中使用其他区域，这些因素可能会限制你的选择：

* 确保使用[受支持的 Azure 区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#voice-assistants)。
* 直接连线语音频道使用文本到语音服务，该服务具有标准和神经声音。 神经声音[仅限于特定的 Azure 区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)。
* 免费试用密钥可限制为特定区域。

有关区域的详细信息，请参阅[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。

## <a name="create-resources"></a>创建资源

在**美国西部**区域中有一个资源组后，下一步就是为你将在本教程中使用的每项服务创建单独的资源。

### <a name="create-a-speech-services-resource"></a>创建语音服务资源

按照以下说明创建语音资源：

1. 从左侧导航栏中选择 "**创建资源**"。
2. 在搜索栏中，键入 "**语音**"。
3. 选择 "**语音**"，然后单击 "**创建**"。
4. 系统将提示你提供一些信息：
   * 为资源指定**名称**。 建议**SpeechEchoBotTutorial**
   * 对于 "**订阅**"，请确保选择 "**免费试用**"。
   * 对于 "**位置**"，请选择 "**美国西部**"。
   * 对于 "**定价层**"，请选择**F0**。 这是免费级别。
   * 对于 "**资源组**"，选择 " **SpeechEchoBotTutorial**"。
5. 输入所有所需信息后，单击 "**创建**"。 创建资源可能需要几分钟时间。
6. 稍后在本教程中，你将需要此服务的订阅密钥。 你可以随时从资源的**概述**（管理密钥）或**密钥**访问这些密钥。

此时，请检查资源组（**SpeechEchoBotTutorial**）是否有语音资源：

| 名称 | 类型  | LOCATION |
|------|-------|----------|
| SpeechEchoBotTutorial-语音 | 认知服务 | 美国西部 |

### <a name="create-an-azure-app-service-plan"></a>创建 Azure 应用服务计划

下一步是创建应用服务计划。 应用服务计划为要运行的 Web 应用定义一组计算资源。

1. 从左侧导航栏中选择 "**创建资源**"。
2. 在搜索栏中，键入 "**应用服务计划**"。 接下来，从搜索结果中找到并选择 "**应用服务计划**" 卡。
3. 单击“创建”。
4. 系统将提示你提供一些信息：
   * 将 "**订阅**" 设置为 "**免费试用**" （也可以使用现有订阅）。
   * 对于 "**资源组**"，选择 " **SpeechEchoBotTutorial**"。
   * 为资源指定**名称**。 建议**SpeechEchoBotTutorial-AppServicePlan**
   * 对于 "**操作系统**"，选择 " **Windows**"。
   * 对于 "**区域**"，选择 "**美国西部**"。
   * 对于 "**定价层**"，请确保选择 "**标准 S1** "。 这应该是默认值。
5. 单击 "**查看和创建**"。 应该会看到一个横幅，其中显示了已**通过的验证**。
6. 单击“创建”。 创建资源组可能需要几分钟的时间。

此时，请检查资源组（**SpeechEchoBotTutorial**）是否有两个资源：

| 名称 | 类型  | LOCATION |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | 应用服务计划 | 美国西部 |
| SpeechEchoBotTutorial-语音 | 认知服务 | 美国西部 |

## <a name="build-an-echo-bot"></a>构建 Echo Bot

现在，你已创建了一些资源，接下来让我们来构建机器人。 接下来，我们将开始介绍回响机器人示例，如名称所示，将输入的文本回显为其响应。 不用担心，示例代码已准备就绪，无需进行任何更改。 它配置为使用直接线路语音频道，我们将在我们将机器人部署到 Azure 后进行连接。

> [!NOTE]
> [GitHub 上的示例自述文件](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/README.md)中提供了后面的说明以及有关 Echo Bot 的其他信息。

### <a name="download-and-run-the-sample"></a>下载并运行示例

1. 克隆示例存储库：
   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```
2. 启动 Visual Studio。
3. 从工具栏中，选择 "**文件**" > **打开** > **项目/解决方案**"，然后打开已配置为与" 直接线路语音通道 "一起使用的回响 Bot 的项目文件：
   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```
4. 加载项目后，按 "`F5`" 运行项目。

### <a name="test-with-the-bot-framework-emulator"></a>用 Bot Framework 模拟器进行测试

[机器人框架模拟器](https://github.com/microsoft/botframework-emulator)是一个桌面应用，允许机器人开发人员通过隧道本地或远程测试和调试 bot。 请按照以下步骤操作，使用机器人框架模拟器测试 Echo 机器人。

1. 安装[机器人 Framework 模拟器](https://github.com/Microsoft/BotFramework-Emulator/releases/latest)版本4.3.0 或更高版本
2. 启动 Bot Framework 模拟器并打开机器人：
   * **文件** -> **打开机器人**。
3. 输入机器人的 URL。 例如：
   ```
   http://localhost:3978/api/messages
   ```
4. 使用 UI 通过键入的文本与机器人进行通信。 确认收到响应。


## <a name="deploy-your-bot-to-an-azure-app-service"></a>将机器人部署到 Azure App Service

下一步是将回显机器人部署到 Azure。 可以通过多种方法来部署机器人，但在本教程中，我们将重点介绍如何直接从 Visual Studio 进行发布。

> [!NOTE]
> 或者，你可以使用[Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli)和[部署模板](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)部署机器人。

1. 在 Visual Studio 中，打开已配置为与直接线路语音通道一起使用的回响机器人：
   ```
   experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj
   ```
2. 在**解决方案资源管理器**中，右键单击**EchoBot**解决方案，然后选择 "**发布 ...** "
3. 将打开一个名为 "**选取" 的**新窗口。
3. 从左侧导航栏中选择 "**应用服务**"，选择 "**新建**"，然后单击 "**发布**"。
5. "**创建应用服务**" 窗口出现时：
   * 单击 "**添加帐户**"，然后使用 Azure 帐户凭据登录。 如果已登录，请从下拉列表中选择所需的帐户。
   * 对于**应用程序名称**，需要输入机器人的全局唯一名称。 此名称用于创建唯一的机器人 URL。 将填充默认值，包括日期和时间（例如： "EchoBot20190805125647"）。 您可以使用本教程的默认名称。
   * 对于 "**订阅**"，将其设置为 "**免费试用**"
   * 对于 "**资源组**"，选择**SpeechEchoBotTutorial**
   * 对于**托管计划**，请选择**SpeechEchoBotTutorial-AppServicePlan**
6. 单击“创建”
7. 在 Visual Studio 中应会看到如下所示的成功消息：
   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```
8. 默认浏览器应打开并显示一个页面，其中显示 "你的机器人已就绪！"。
9. 此时，请在 Azure 门户中检查资源组**SpeechEchoBotTutorial** ，并确认有三个资源：

| 名称 | 类型  | LOCATION |
|------|-------|----------|
| EchoBot20190805125647 | 应用服务 | 美国西部 |
| SpeechEchoBotTutorial-AppServicePlan | 应用服务计划 | 美国西部 |
| SpeechEchoBotTutorial-语音 | 认知服务 | 美国西部 |

## <a name="enable-web-sockets"></a>启用 Web 套接字

需要进行较小的配置更改，使机器人能够使用 web 套接字与直接线路语音通道通信。 按照以下步骤启用 web 套接字：

1. 导航到[Azure 门户](https://portal.azure.com)，找到应用服务。 资源应命名为类似于**EchoBot20190805125647** （唯一的应用名称）。
2. 在左侧导航窗格中，选择 "**设置**"，然后单击 "**配置**"。
3. 选择 "**常规设置**" 选项卡。
4. 找到 " **Web 套接字**" 的切换，并将其设置为 **"开**"。
5. 单击“保存”。

> [!TIP]
> 您可以使用 Azure App Service 页顶部的控件来停止或重新启动该服务。 故障排除时，这可能很方便。

## <a name="create-a-channel-registration"></a>创建频道注册

创建用于托管机器人的 Azure App Service 后，下一步就是创建**机器人频道注册**。 创建频道注册是将机器人注册到机器人框架通道（包括直接线路语音通道）的先决条件。

> [!NOTE]
> 如果要了解有关 bot 如何利用频道的详细信息，请参阅[将 Bot 连接到通道](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)。

1. 第一步是为注册创建新的资源。 在 [Azure 门户](https://portal.azure.com)中，单击“创建资源”。
2. 在搜索栏类型**机器人**中显示结果后，选择 "**机器人频道注册**"。
3. 单击“创建”。
4. 系统将提示你提供一些信息：
   * 对于 " **Bot 名称**"，输入**SpeechEchoBotTutorial-BotRegistration**。
   * 对于 "**订阅**"，请选择 "**免费试用**"。
   * 对于 "**资源组**"，选择 " **SpeechEchoBotTutorial**"。
   * 对于 "**位置**"，请选择 "**美国西部**"。
     * 对于 "**定价层**"，请选择**F0**。
     * 对于 "**消息传递终结点**"，请输入 web 应用的 URL，并在末尾追加 `/api/messages` 路径。 例如：如果全局唯一的应用名称为**EchoBot20190805125647**，则消息传递终结点将为： `https://EchoBot20190805125647.azurewebsites.net/api/messages/`。
     * 对于**application insights**，你可以将其设置为**Off**。 有关详细信息，请参阅[机器人分析](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0)。
     * 忽略**自动创建应用 ID 和密码**。
5. 导航回**机器人频道注册**，并单击 "**创建**"。

此时，请查看 Azure 门户中的资源组**SpeechEchoBotTutorial** 。 它现在应显示四个资源：

| 名称 | 类型  | LOCATION |
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
2. 从左侧导航栏中选择 "**频道**"。
   * 查找**更多通道**，找到并单击 "**直接连线语音**"。
   * 查看标题为 "**配置直接行语音**" 的页面上的文本，然后单击 "**保存**"。
   * 在创建过程中，将生成两个**机密密钥**。 这些密钥在机器人中是唯一的。 使用[SPEECH SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/)编写客户端应用时，将提供其中一个密钥，以便在客户端应用、直接线路语音通道和机器人服务之间建立连接。 在本教程中，您将使用语音语音客户端（WPF C#）。
   * 单击 "**显示**"，并将其中一个密钥复制到可以轻松访问的位置。 别担心，你始终可以从 Azure 门户访问密钥。
3. 从左侧导航栏中，单击 "**设置**"。
   * 选中标记为**启用流式处理终结点**的框。 这是在机器人和直接线路语音通道之间启用基于 web 套接字构建的通信协议所必需的。
   * 单击“保存”。

> [!TIP]
> 若要了解详细信息，请参阅[连接机器人以定向语音](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)。 此页包含其他信息和已知问题。

## <a name="build-the-direct-line-speech-client"></a>生成语音语音客户端

在此步骤中，您将构建直接的语音客户端。 客户端是中C#的一种 WINDOWS PRESENTATION FOUNDATION （WPF）应用，它使用[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk)来管理与机器人的通信。 在编写自定义客户端应用之前，可以使用它来与机器人进行交互和测试。

直接连线语音客户端具有一个简单的 UI，可用于配置与机器人的连接、查看文本对话、以 JSON 格式查看 Bot 框架活动，以及显示自适应卡。 它还支持使用自定义关键字。 你将使用此客户端与机器人对话，并收到语音回复。

在继续之前，请确保麦克风和扬声器已启用且正常工作。

1. 导航到[Direct Line Speech Client](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md)的 GitHub 存储库。
2. 按照提供的说明克隆存储库，生成项目，配置客户端，然后启动客户端。
3. 单击 "**重新连接**"，确保看到消息**按下麦克风按钮，或键入即可开始与机器人通信**。
4. 让我们对它进行测试。单击麦克风按钮，并口述几个英语单词。 将显示已识别的文本。 完成此操作后，bot 将以自己的语音进行回复，并显示 "echo" 后跟识别的单词。
5. 你还可以使用文本与机器人通信。 只需在底部栏中键入文本。 

### <a name="troubleshooting-errors-in-direct-line-speech-client"></a>排查直接连线语音客户端中的错误

如果在主应用窗口中收到错误消息，请使用此表来确定并解决该错误：

| 错误 | 您该怎么办？ |
|-------|----------------------|
|应用错误（有关详细信息，请参阅日志）： Cognitiveservices account：值不能为 null。 参数名称： speechConfig | 这是客户端应用错误。 请确保主应用窗口中的*机器人机密*值为非空（请参阅[使用直接线路语音通道注册机器人](#register-the-direct-line-speech-channel)部分） |
|错误 AuthenticationFailure： WebSocket 升级失败，出现身份验证错误（401）。 检查正确的订阅密钥（或授权令牌）和区域名称| 在应用的 "设置" 页中，确保已正确输入语音订阅密钥及其区域。<br>请确保正确输入了机器人密码。 |
|错误 ConnectionFailure：远程主机关闭了连接。 错误代码：1011。 错误详细信息：无法在发送消息之前连接到机器人 | 请确保[已选中 "启用流式处理终结点"](#register-the-direct-line-speech-channel)框和/或将[ **Web 套接字**切换](#enable-web-sockets)到 "打开"。<br>请确保 Azure App Service 正在运行。 如果已启动，请尝试重新启动应用服务。|
|错误 ConnectionFailure：远程主机关闭了连接。 错误代码：1011。 错误详细信息：响应状态代码不指示成功：500（InternalServerError）| 机器人在其输出活动的 "[朗读](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)" 字段中指定了一个神经声音，但与您的语音订阅密钥关联的 Azure 区域不支持神经语音。 请参阅[标准和神经声音](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)。|
|错误 ConnectionFailure：远程主机关闭了连接。 错误代码：1000。 错误详细信息：超出最大 web 套接字连接空闲持续时间（> 300000 ms）| 如果到通道的连接处于打开状态且处于非活动状态的时间超过五分钟，则会出现此错误。 |

如果未在表中解决问题，请参阅[语音助手：常见问题解答](faq-voice-assistants.md)。

### <a name="view-bot-activities"></a>查看机器人活动

每个机器人发送和接收**活动**消息。 在直接语音语音客户端的 "**活动日志**" 窗口中，你将看到带有客户端从机器人接收到的每个活动的有时间戳的日志。 还可以查看客户端使用[`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync)方法发送到机器人的活动。 选择日志项时，它会将关联活动的详细信息显示为 JSON。

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
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

若要详细了解 JSON 输出中返回的内容，请参阅[活动中的字段](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)。 出于本教程的目的，您可以将重点放在[文本](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text)和[讲述](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)字段上。

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>查看调用语音 SDK 的客户端源代码

直接连线语音客户端使用包含语音 SDK 的 NuGet 包[cognitiveservices account。](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) 开始查看示例代码的一个好方法是[`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs)文件中的方法 InitSpeechConnector （），这会创建两个语音 SDK 对象：
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) -用于配置设置（语音订阅密钥、密钥区域、机器人机密）
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) -管理通道连接和客户端订阅事件，以便处理识别的语音和机器人响应。

## <a name="add-custom-keyword-activation"></a>添加自定义关键字激活

语音 SDK 支持自定义关键字激活。 与 Microsoft 助手的 "你好 Cortana" 类似，你可以编写一个应用，该应用会持续侦听你选择的关键字。 请记住，关键字可以是单个词，也可以是多单词短语。

> [!NOTE]
> 术语*关键字*通常与字词*唤醒词*互换使用，你可能会看到在 Microsoft 文档中使用了这两项。

关键字检测在客户端应用上完成。 如果使用关键字，则只有在检测到关键字时才会将音频流式传输到直接语音通道。 直接连线语音频道包括一个名为*关键字验证（KWV）* 的组件，该组件在云中执行更复杂的处理，以验证所选关键字是否位于音频流的开头。 如果关键字 word 验证成功，则通道将与机器人通信。

按照以下步骤创建关键字模型，将 "语音语音客户端" 配置为使用此模型，最后使用机器人进行测试。

1. 按照这些说明[使用语音服务创建自定义关键字](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)。
2. 解压缩在上一步中下载的模型文件。 应为关键字命名。 正在查找名为 `kws.table`的文件。
3. 在 "直接语音" 客户端中，找到 "**设置**" 菜单（查找右上方的齿轮图标）。 查找 "**模型文件路径**" 并输入步骤2中 `kws.table` 文件的完整路径名称。
4. 确保选中标记为 "**已启用**" 的框。 在复选框旁边应会看到此消息： "下次连接时将侦听关键字"。 如果提供了错误的文件或路径无效，应会看到一条错误消息。
5. 输入语音**订阅密钥**、**订阅密钥区域**，然后单击 **"确定"** 以关闭 "**设置**" 菜单。
6. 选择**机器人机密**，并单击 "**重新连接**"。 应该会看到一条消息，其中显示： "新会话已启动-类型，按下麦克风按钮，或说关键字为"。 应用现在正在持续侦听。
7. 说任何以关键字开头的短语。 例如： " **{关键字}** ，它的时间是多少？"。 Uttering 关键字后，不需要暂停。 完成后，会发生两种情况：
   * 你会看到你的辐射
   * 不久之后，你会听到机器人的响应
8. 继续试验机器人支持的三个输入类型：
   * 键入文本在底部栏中
   * 按下麦克风图标和讲话
   * 说以关键字开头的短语

### <a name="view-the-source-code-that-enables-keyword"></a>查看启用关键字的源代码

在 "直接连线客户端源代码" 中，查看以下文件以查看用于启用关键字检测的代码：

1. [`DLSpeechClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/Models.cs)包括对语音 SDK 方法[`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)的调用，该方法用于实例化磁盘上的本地文件中的模型。
1. [`DLSpeechClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/DLSpeechClient/MainWindow.xaml.cs)包括对语音 SDK 方法[`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)的调用，该方法将激活连续关键字检测。

## <a name="optional-change-the-language-and-redeploy-your-bot"></a>可有可无更改语言并重新部署机器人

你创建的机器人将侦听并响应英语。 但是，你并不局限于使用英语。 在本部分中，你将了解如何更改机器人侦听和响应的语言，以及如何重新部署机器人。

### <a name="change-the-language"></a>更改语言

1. 首先打开 `\experimental\directline-speech\csharp_dotnetcore\02.echo-bot\Bots\echo-bot.cs`。
2. 接下来，找到 SSML。 很容易找到，因为它包含在 `<speak></speak>` 的标记中。
3. 在 SSML 字符串中，找到 `<voice name>` 标记，将其替换为 `<voice name='de-DE-Stefan-Apollo'>`并保存。 此格式字符串告诉文本到语音服务如何使用语音 `de-DE-Stefan-Apollo`（经过了德语优化）返回合成的语音响应。

>[!NOTE]
> 你不限于德语，并且可以从[语音服务](language-support.md#text-to-speech)的可用语音列表中进行选择。

### <a name="redeploy-your-bot"></a>重新部署机器人

完成机器人的必要更改后，下一步就是将其重新发布到 Azure App Service 并试用：

1. 在 Visual Studio 中生成解决方案并修复任何生成错误。
2. 在 "解决方案资源管理器" 窗口中，右键单击**EchoBot**项目，然后选择 "**发布**"。
3. 以前的部署配置已作为默认值加载。 只需单击 "EchoBot20190805125647" 旁边**Web 部署的**"**发布**"。
4. "**发布成功**" 消息将显示在 Visual Studio 的 "输出" 窗口中，并且网页将启动并显示消息 "机器人已就绪！"。
5. 打开 "语音语音客户端应用"，单击 "设置" 按钮（右上角的齿轮图标），并在 "语言" 字段中输入 `de-de`。 这将设置要识别的口述语言，并覆盖默认 `en-us`。
6. 按照[生成直接连线语音客户端](#build-the-direct-line-speech-client)中的说明重新连接到你的新部署的机器人，并以新语言讲述，并听到你用新的语音以该语言回复了机器人。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用本教程中部署的 echo 机器人，只需删除 Azure 资源组**SpeechEchoBotTutorial**，即可将其删除及其关联的所有 Azure 资源。

1. 在[Azure 门户](https://portal.azure.com)中，单击左侧导航栏中的 "**资源组**"。
2. 找到名为 " **SpeechEchoBotTutorial**" 的资源组。 单击三个点（...）。
3. 选择“删除资源组”。

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
  * 探索现有[机器人框架解决方案](https://github.com/microsoft/botframework-solutions)：生成[自定义语音助手](https://docs.microsoft.com/azure/cognitive-services/speech-service/voice-assistants)并[对其进行语音启用](https://github.com/microsoft/botframework-solutions/blob/master/docs/howto/assistant/csharp/speechenablement.md)
