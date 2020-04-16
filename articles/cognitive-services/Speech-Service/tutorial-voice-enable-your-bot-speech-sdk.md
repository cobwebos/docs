---
title: 教程：语音使用语音 SDK 启用您的自动程序 - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本教程中，您将使用 Microsoft 机器人框架创建 Echo Bot，将其部署到 Azure，并将其注册到机器人框架直接行语音通道。 然后，您将为 Windows 配置一个示例客户端应用，该应用允许您与机器人通话，并听到它响应您。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: trbye
ms.openlocfilehash: b2c119f6552773bce7bb93a503c22324278ac0bc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399463"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>教程：使用语音 SDK 启用自动程序

现在，您可以使用语音服务的强大功能轻松启用语音聊天机器人。

在本教程中，您将使用 Microsoft 机器人框架创建 Echo Bot，将其部署到 Azure，并将其注册到机器人框架直接行语音通道。 然后，您将为 Windows 配置一个示例客户端应用，该应用允许您与机器人通话，并听到它响应您。

本教程专为刚刚开始使用 Azure、机器人框架机器人、直线语音或语音 SDK 的开发人员而设计，并希望快速构建编码有限的工作系统。 无需体验或熟悉这些服务。

在本练习结束时，您将设置一个系统，该系统将运行如下：

1. 示例客户端应用程序配置为连接到直线语音通道和回声自动程序
1. 音频从按钮按下的默认麦克风录制（如果激活自定义关键字，则连续录制）
1. 可选地，自定义关键字检测发生，将音频流门到云
1. 使用语音 SDK，应用程序连接到直线语音通道并流式传输音频
1. 可选，更高的精度关键字验证发生在服务上
1. 音频传递给语音识别服务并转录为文本
1. 识别的文本作为机器人框架活动传递给回声机器人 
1. 响应文本通过文本到语音 （TTS） 服务转换为音频，并流式传输回客户端应用程序以进行播放

![图表标记](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "语音通道流")

> [!NOTE]
> 本教程中的步骤不需要付费服务。 作为新的 Azure 用户，您可以使用免费 Azure 试用版订阅的配额和语音服务的免费套餐来完成本教程。

本教程的内容：
> [!div class="checklist"]
> * 创建新的 Azure 资源
> * 生成、测试和将 Echo Bot 示例部署到 Azure 应用服务
> * 使用直线语音通道注册您的自动程序
> * 生成并运行 Windows 语音助理客户端，以便与您的 Echo 机器人交互
> * 添加自定义关键字激活
> * 学习改变公认的口语和口语的语言

## <a name="prerequisites"></a>先决条件

以下是完成本教程所需的内容：

- 带有工作麦克风和扬声器（或耳机）的 Windows 10 电脑
- [视觉工作室 2017](https://visualstudio.microsoft.com/downloads/)或更高版本
- [.NET 框架运行时 4.6.1](https://dotnet.microsoft.com/download)或更高版本
- 一个 Azure 帐户。 [免费注册](https://azure.microsoft.com/free/ai/)。
- [GitHub](https://github.com/)帐户
- [用于窗口的 Git](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>创建资源组

您将在本教程中创建的客户端应用使用少量 Azure 服务。 要减少来自自动程序响应的往返时间，您需要确保这些服务位于同一 Azure 区域中。 在本节中，您将**在美国西部**区域创建一个资源组。 在为 Bot 框架、直线语音通道和语音服务创建单个资源时，将使用此资源组。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">创建资源组<span class="docon docon-navigate-external x-hidden-focus"></span></a>
1. 系统将提示您提供一些信息：
   * 将**订阅**设置为**免费试用**版（您还可以使用现有订阅）。
   * 输入**资源组**的名称。 我们建议**语音回声机器人教程-资源组**。
   * 从 **"区域**"下拉列表，选择**美国西部**。
1. 单击“审阅并创建”****。 您应该会看到一个横幅，该横幅读取**验证通过**。
1. 单击“创建”。  创建资源组可能需要几分钟时间。
1. 与本教程稍后将创建的资源一样，最好将此资源组固定到仪表板以方便访问。 如果要固定此资源组，请单击仪表板右上角的固定图标。

### <a name="choosing-an-azure-region"></a>选择 Azure 区域

如果您想在本教程中使用其他区域，这些因素可能会限制您的选择：

* 确保使用[受支持的 Azure 区域](regions.md#voice-assistants)。
* 直线语音通道使用文本到语音服务，该服务具有标准和神经语音。 神经语音[仅限于特定的 Azure 区域](regions.md#standard-and-neural-voices)。
* 免费试用密钥可能仅限于特定区域。

有关区域的详细信息，请参阅[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。

## <a name="create-resources"></a>创建资源

现在，您已在受支持的区域中拥有一个资源组，下一步是为本教程中要使用的每个服务创建单个资源。

### <a name="create-a-speech-service-resource"></a>创建语音服务资源

按照以下说明创建语音资源：

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">创建语音服务资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 系统将提示您提供一些信息：
   * 为您的资源指定**名称**。 我们建议**语音回声机器人教程-语音**
   * 对于**订阅**，请确保选择了**免费试用**版。
   * 对于**位置**，请选择**美国西部**。
   * 对于**定价层**，选择**F0**。 这是免费套餐。
   * 对于**资源组**，选择**语音回声机器人教程 -资源组**。
5. 输入所有必需信息后，单击"**创建**"。 创建资源可能需要几分钟时间。
6. 在本教程的后面部分，您将需要此服务的订阅密钥。 您可以随时从资源的**概述**（管理密钥）或**密钥**访问这些密钥。

此时，请检查您的资源组 （**语音EchoBot教程-资源组**） 具有语音资源：

| 名称 | 类型  | 位置 |
|------|-------|----------|
| 语音回声机器人教程-语音 | 认知服务 | 美国西部 |

### <a name="create-an-azure-app-service-plan"></a>创建 Azure 应用服务计划

下一步是创建应用服务计划。 应用服务计划为要运行的 Web 应用定义一组计算资源。

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">创建 Azure 应用服务计划<span class="docon docon-navigate-external x-hidden-focus"></span></a>
4. 系统将提示您提供一些信息：
   * 将**订阅**设置为**免费试用**版（您还可以使用现有订阅）。
   * 对于**资源组**，选择**语音回声机器人教程 -资源组**。
   * 为您的资源指定**名称**。 我们建议**语音回声机器人教程-应用程序服务计划**
   * 对于**操作系统**，请选择**Windows**。
   * 对于**区域**，选择**美国西部**。
   * 对于**定价层**，请确保选择了**标准 S1。** 这应该是默认值。 如果不是，请确保将**操作系统**设置为**Windows，** 如上所述。
5. 单击“审阅并创建”****。 您应该会看到一个横幅，该横幅读取**验证通过**。
6. 单击“创建”。  创建资源组可能需要几分钟时间。

此时，请检查您的资源组 （**语音EchoBot教程-资源组**） 有两个资源：

| 名称 | 类型  | 位置 |
|------|-------|----------|
| 语音回声机器人教程-应用程序服务计划 | 应用服务计划 | 美国西部 |
| 语音回声机器人教程-语音 | 认知服务 | 美国西部 |

## <a name="build-an-echo-bot"></a>构建回声机器人

现在，您已经创建了一些资源，让我们构建一个自动程序。 我们将从 Echo Bot 示例开始，顾名思义，该示例与您输入的文本回响为响应。 别担心，示例代码已准备就绪，无需进行任何更改即可使用。 它配置为使用直接线路语音通道，我们将在将自动程序部署到 Azure 后连接该通道。

> [!NOTE]
> 以下说明以及有关 Echo Bot 的其他信息可在[GitHub 上的 README 中](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)获取。

### <a name="run-the-bot-sample-on-your-machine"></a>在计算机上运行自动程序示例

1. 克隆示例存储库：

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. 启动 Visual Studio。
3. 从工具栏中，选择 **"文件** > **打开** > **项目/解决方案**"，然后打开回声机器人项目解决方案：

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. 加载项目后，按<kbd>F5</kbd>生成并运行项目。
5. 浏览器应启动，您将看到一个屏幕如下所示。
    > [!div class="mx-imgBorder"]
    > [![本地主机上的回声机器人运行](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "在本地主机上运行的 EchoBot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>使用机器人框架仿真器测试机器人示例

[机器人框架仿真器](https://github.com/microsoft/botframework-emulator)是一个桌面应用程序，允许机器人开发人员在本地或通过隧道远程测试和调试其机器人。 仿真器支持键入的文本作为输入（而不是语音）。 自动程序将用文本进行响应。 按照以下步骤使用机器人框架仿真器测试在本地运行的 Echo Bot，并带有文本输入和文本输出。 将自动程序部署到 Azure 后，我们将使用语音输入和语音输出对其进行测试。

1. 安装[机器人框架仿真器](https://github.com/Microsoft/BotFramework-Emulator/releases/latest)版本 4.3.0 或更高版本
2. 启动机器人框架仿真器并打开您的自动程序：
   * **文件** -> **打开机器人**。
3. 输入自动程序的 URL。 例如：

   ```
   http://localhost:3978/api/messages
   ```
   然后按"连接"。
4. 机器人应该立即迎接你与"你好，欢迎！ 。 键入任何文本消息，并确认您从自动程序获得响应。
5. 这就是与 Echo Bot 实例交换通信可能如下所示的内容：[![机器人框架-仿真器](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "机器人框架仿真器")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>将自动程序部署到 Azure 应用服务

下一步是将回声自动程序部署到 Azure。 有几种方法可以部署自动程序，但在本教程中，我们将专注于直接从 Visual Studio 发布。

> [!NOTE]
> 或者，可以使用[Azure CLI](https://docs.microsoft.com/azure/bot-service/bot-builder-deploy-az-cli)和[部署模板](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates)部署自动程序。

1. 从 Visual Studio，打开已配置为与直线语音通道一起使用的回声机器人：

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. 在**解决方案资源管理器**中，右键单击**EchoBot**项目并选择 **"发布..."**
1. 将打开名为 **"选取发布目标"** 的新窗口。
1. 从**Azure 服务**导航中选择**应用服务**，选择 **"新建**"，然后单击"**创建配置文件**"。
1. 当**出现"创建应用服务"** 窗口时：
   * 单击"**添加帐户**"，然后使用 Azure 帐户凭据登录。 如果已登录，请从下拉列表中选择所需的帐户。
   * 对于**应用名称**，您需要为自动程序输入全局唯一名称。 此名称用于创建唯一的自动程序 URL。 将填充一个默认值，包括日期和时间（例如："EchoBot20190805125647"）。 您可以为本教程使用默认名称。
   * 对于**订阅**，将其设置为**免费试用**
   * 对于**资源组**，选择**语音回声机器人教程-资源组**
   * 对于**托管计划**，选择**语音EchoBot教程-应用程序服务计划**
   * 对于**应用程序见解**，请保留为 **"无"**
1. 单击“创建” 
1. 您应该在 Visual Studio 中看到一条如下所示的成功消息：

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. 默认浏览器应打开并显示一个页面，内容为："您的自动程序已准备就绪！
1. 此时，请检查 Azure 门户中的资源组**语音EchoBot教程-资源组**，并确认有三种资源：

| 名称 | 类型  | 位置 |
|------|-------|----------|
| EchoBot20190805125647 | 应用服务 | 美国西部 |
| 语音回声机器人教程-应用程序服务计划 | 应用服务计划 | 美国西部 |
| 语音回声机器人教程-语音 | 认知服务 | 美国西部 |

## <a name="enable-web-sockets"></a>启用 Web 套接字

您需要进行一些小的配置更改，以便您的机器人可以使用 Web 套接字与直接语音通道进行通信。 按照以下步骤启用 Web 套接字：

1. 导航到[Azure 门户](https://portal.azure.com)，并找到应用服务。 资源应命名为**类似 EchoBot20190805125647（** 您的唯一应用名称）。
2. 在**Azure 服务**导航中，在 **"设置"** 下单击"**配置**"。
3. 选择"**常规设置"** 选项卡。
4. 找到**Web 套接字的**切换，并将其设置为 **"打开**"。
5. 单击“ **保存**”。

> [!TIP]
> 您可以使用 Azure 应用服务页顶部的控件停止或重新启动服务。 在故障排除时，这可能派上用场。

## <a name="create-a-channel-registration"></a>创建通道注册

现在，您已经创建了一个 Azure 应用服务来托管您的自动程序，下一步是创建**一个自动程序通道注册**。 创建通道注册是使用 Bot 框架频道（包括直拨语音通道）注册机器人的先决条件。

> [!NOTE]
> 如果您想了解有关机器人如何利用通道的信息，请参阅[将机器人连接到通道](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0)。


1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">创建 Azure 自动程序通道注册<span class="docon docon-navigate-external x-hidden-focus"></span></a>
2. 系统将提示您提供一些信息：
   * 对于**自动处理**，输入**语音回声机器人教程-自动注册**。
   * 对于**订阅**，选择**免费试用**。
   * 对于**资源组**，选择**语音回声机器人教程 -资源组**。
   * 对于**位置**，请选择**美国西部**。
     * 对于**定价层**，选择**F0**。
     * 对于**消息终结点**，输入 Web 应用的 URL，`/api/messages`末尾附加路径。 例如：如果您的全局唯一应用名称是**EchoBot20190805125647，** 则消息传递终结点为： `https://EchoBot20190805125647.azurewebsites.net/api/messages/`。
     * 对于**应用程序见解**，您可以将其设置为 **"关闭**"。 有关详细信息，请参阅[机器人分析](https://docs.microsoft.com/azure/bot-service/bot-service-manage-analytics?view=azure-bot-service-4.0)。
     * 忽略**自动创建应用 ID 和密码**。
5. 在 **"机器人通道注册"** 边栏选项卡的底部，单击"**创建**"。

此时，请在 Azure 门户中检查资源组**语音EchoBot教程-资源组**。 它现在应该显示四个资源：

| 名称 | 类型  | 位置 |
|------|-------|----------|
| EchoBot20190805125647 | 应用服务 | 美国西部 |
| 语音回声机器人教程-应用程序服务计划 | 应用服务计划 | 美国西部 |
| 语音回声机器人教程-机器人注册 | 机器人通道注册 | global |
| 语音回声机器人教程-语音 | 认知服务 | 美国西部 |

> [!IMPORTANT]
> 即使选择了美国西部，"机器人通道注册"资源也会显示全局区域。 这是正常情况。

## <a name="register-the-direct-line-speech-channel"></a>注册直线语音通道

现在是时候注册你的机器人到直接线语音频道。 此通道用于在回声机器人和使用语音 SDK 编译的客户端应用之间创建连接。

1. 在[Azure 门户](https://portal.azure.com)中查找并打开**语音EchoBot教程-自动注册**资源。
1. 从**Azure 服务**导航中，选择 **"通道**"。
   * 查找**更多频道**，找到并单击 **"直线语音**"。
   * 查看标题为 **"配置直线语音**"的页面上的文本，然后展开标记为"认知服务帐户"的下拉菜单。
   * 从菜单中选择您之前创建的语音资源（例如，**语音EchoBotTutorial-语音**），将自动程序与语音订阅键相关联。
   * 单击“ **保存**”。

1. 在 **"机器人管理**"导航中，单击"**设置**"。
   * 选中标记为启用**流式处理终结点**的框。 这是必需的，以启用在自动程序与直线语音通道之间的 Web 套接字上构建的通信协议。
   * 单击“ **保存**”。

> [!TIP]
> 如果您想了解更多信息，请参阅[将自动程序连接到直线语音](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)。 此页包括其他信息和已知问题。

## <a name="run-the-windows-voice-assistant-client"></a>运行 Windows 语音助理客户端

在此步骤中，您将运行 Windows 语音助理客户端。 客户端是 C# 中的 Windows 演示文稿基础 （WPF） 应用，它使用[语音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk)使用直接线路语音通道管理与机器人的通信。 在编写自定义客户端应用之前，使用它与机器人进行交互并测试。 它是开源的，因此您可以下载可执行文件并运行它，也可以自己构建它。

Windows 语音助理客户端具有一个简单的 UI，允许您配置与自动程序的连接、查看文本对话、以 JSON 格式查看 Bot-Framework 活动以及显示自适应卡。 它还支持使用自定义关键字。 您将使用此客户端与您的机器人对话并接收语音响应。

在我们继续之前，请确保您的麦克风和扬声器已启用并正常工作。

1. 导航到[Windows 语音助理客户端](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)的 GitHub 存储库。
1. 按照提供的说明，要么
   * 下载包含要运行的可执行文件的 ZIP 包，或
   * 通过克隆存储库和生成项目，自行构建可执行文件。

1. 启动客户端应用程序并对其进行配置。
1. 单击 **"重新连接**"并确保看到消息 **"按麦克风"按钮，或键入以开始与机器人对话**。
1. 让我们测试一下。单击麦克风按钮，用英语说几句话。 您说话时将显示识别的文本。 当你说完之后，机器人会用自己的声音回答，说"回声"，然后是识别的单词。
1. 您还可以使用文本与机器人通信。 只需在底部栏中键入文本。 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>解决 Windows 语音助理客户端中的错误

如果在主应用窗口中收到错误消息，请使用此表来识别和排除错误：

| 错误 | 该怎么办？ |
|-------|----------------------|
|错误身份验证失败：WebSocket 升级失败，身份验证错误 （401）。 检查正确的订阅密钥（或授权令牌）和地区名称| 在应用的"设置"页中，请确保正确输入语音订阅密钥及其区域。<br>确保正确输入语音键和关键区域。 |
|错误连接失败：连接被远程主机关闭。 错误代码：1011。 错误详细信息：在发送消息之前，我们无法连接到自动程序 | 请确保[选中"启用流式处理终结点"](#register-the-direct-line-speech-channel)框和/或[将 Web**套接字**切换](#enable-web-sockets)为"打开"。<br>确保 Azure 应用服务正在运行。 如果是，请尝试重新启动应用服务。|
|错误连接失败：连接被远程主机关闭。 错误代码：1011。 错误详细信息：响应状态代码不表示成功：500（内部服务器错误）| 机器人在其输出"活动[说话"](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)字段中指定了神经语音，但与语音订阅键关联的 Azure 区域不支持神经语音。 请参阅[标准和神经声音](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)。|
|错误连接失败：连接被远程主机关闭。 错误代码：1000。 错误详细信息：超过最大 Web 套接字连接空闲持续时间（> 300000 ms）| 当与通道的连接保持打开且非活动状态超过五分钟时，这是一个预期错误。 |

如果您的问题未在表中得到解决，请参阅[语音助理：常见问题](faq-voice-assistants.md)。

### <a name="view-bot-activities"></a>查看机器人活动

每个自动程序发送和接收**活动**消息。 在 Windows 语音助理客户端**的活动日志**窗口中，您将看到客户端从自动程序收到的每个活动的时间戳日志。 您还可以查看客户端使用 方法[`DialogServiceConnector.SendActivityAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync)发送到自动程序的活动。 当您选择日志项时，它将以 JSON 的形式显示关联活动的详细信息。

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

要了解有关 JSON 输出中返回的内容的更多内容，请参阅[活动 中的字段](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md)。 为了本教程的目的，您可以专注于["文本](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text)"和["说话"](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)字段。

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>查看对语音 SDK 的调用的客户端源代码

Windows 语音助理客户端使用 NuGet 包[Microsoft.认知服务.语音](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)，其中包含语音 SDK。 开始查看示例代码的一个好地方是文件中[`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)的方法 InitIt语音连接器（），它创建了以下两个语音 SDK 对象：
- [`DialogServiceConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig)- 对于配置设置（例如，语音订阅密钥、关键区域）
- [`DialogServiceConnector`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor)- 管理通道连接和客户端订阅事件，以处理识别的语音和自动程序响应。

## <a name="add-custom-keyword-activation"></a>添加自定义关键字激活

语音 SDK 支持自定义关键字激活。 与 Microsoft 助手的"Hey Cortana"类似，您可以编写一个应用，该应用将持续侦听您选择的关键字。 请记住，关键字可以是单个单词或多字短语。

> [!NOTE]
> 术语*关键字*通常与术语*唤醒词*互换使用，您可能会看到 Microsoft 文档中都使用。

关键字检测在客户端应用上完成。 如果使用关键字，则仅在检测到关键字时才会将音频流式传输到"直线语音"通道。 直线语音通道包括一个称为*关键字验证 （KWV） 的*组件，该组件在云中执行更复杂的处理，以验证您选择的关键字是否位于音频流的开头。 如果关键词验证成功，则通道将与机器人通信。

按照以下步骤创建关键字模型，将 Windows 语音助理客户端配置为使用此模型，最后，使用自动程序对其进行测试。

1. 按照这些说明[使用语音服务创建自定义关键字](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)。
2. 解压缩您在上一步中下载的模型文件。 它应该为您的关键字命名。 您正在寻找一个名为 的文件`kws.table`。
3. 在 Windows 语音助理客户端中，找到 **"设置"** 菜单（查找右上角的齿轮图标）。 找到**模型文件路径**，然后从步骤 2 输入`kws.table`文件的完整路径名称。
4. 请确保选中标记为"**已启用"的**框。 您应该在复选框旁边看到此消息："将在下一次连接时侦听关键字"。 如果提供了错误的文件或无效的路径，则应看到一条错误消息。
5. 输入语音**订阅键**、**订阅键区域**，然后单击 **"确定**"以关闭 **"设置"** 菜单。
6. 单击 **"重新连接**"。 您应该会看到一条消息，内容是："开始新的对话 - 键入、按下麦克风按钮或说出关键字"。 应用程序现在不断侦听。
7. 说出以关键字开头的任何短语。 例如 **："[你的关键字]，** 它是什么时间？ 说出关键字后，无需暂停。 完成后，会发生两件事：
   * 你会看到你说话的抄录
   * 不久之后，您将听到机器人的响应
8. 继续试验自动程序支持的三种输入类型：
   * 底部栏中键入文本
   * 按下麦克风图标并讲话
   * 说出以关键字开头的短语

### <a name="view-the-source-code-that-enables-keyword"></a>查看启用关键字的源代码

在 Windows 语音助理客户端源代码中，查看这些文件以查看用于启用关键字检测的代码：

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs)包括对语音 SDK 方法[`KeywordRecognitionModel.fromFile()`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel?view=azure-node-latest#fromfile-string-)的调用，该方法用于从磁盘上的本地文件实例化模型。
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs)包括对语音 SDK 方法[`DialogServiceConnector.StartKeywordRecognitionAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync)的调用，该方法可激活连续关键字检测。

## <a name="optional-change-the-language-and-bot-voice"></a>（可选）更改语言和自动声音

您创建的自动程序将用英语收听和响应，默认使用美国英语文本到语音。 但是，您不限于使用英语或默认语音。 在本节中，您将学习如何更改自动程序将侦听和响应的语言。 您还将学习如何为该语言选择其他语音。

### <a name="change-the-language"></a>更改语言

可以从[语音到文本](language-support.md#speech-to-text)表中提及的任何一种语言中进行选择。 在下面的示例中，我们将将语言更改为德语。

1. 打开 Windows 语音助理客户端应用，单击设置按钮（右上齿轮图标），并在"语言"`de-de`字段中输入（这是[语音到文本](language-support.md#speech-to-text)表中提及的"区域设置"值）。 这将设置要识别的口语，覆盖默认值`en-us`。 这还指示直线语音通道使用默认的德语语音进行自动回复。
2. 关闭设置页面，然后单击"重新连接"按钮以建立与回声自动程序的新连接。
3. 单击麦克风按钮，用德语说一个短语。 您将看到识别的文本和回显自动程序以默认的德语语音进行回复。

### <a name="change-the-default-bot-voice"></a>更改默认自动程序语音

如果 Bot 以[语音合成标记语言](speech-synthesis-markup.md)（SSML） 而不是简单文本的形式指定回复，则可以选择文本到语音语音和控制发音。 回声机器人不使用 SSML，但我们可以轻松地修改代码来执行此操作。 在下面的示例中，我们将 SSML 添加到回声自动响应中，以便使用德国语音 Stefan Apollo（男性语音）而不是默认的女性语音。 请参阅支持您的语言[的标准语音](language-support.md#standard-voices)[和神经语音](language-support.md#neural-voices)列表。

1. 让我们从打开`samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs`开始。
2. 找到这两行：
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
4. 在可视化工作室中构建解决方案并修复任何生成错误。

方法"MessageFactory.Text"中的第二个参数设置自动程序答复中的["活动说话"字段](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)。 通过上述更改，它已被替换为从简单文本到 SSML，以便指定非默认的德语语音。

### <a name="redeploy-your-bot"></a>重新部署自动程序

现在，您对自动程序进行了必要的更改，下一步是将其重新发布到 Azure 应用服务并尝试：

1. 在"解决方案资源管理器"窗口中，右键单击**EchoBot**项目并选择 **"发布**"。
2. 以前的部署配置已加载为默认值。 只需单击**Publish****EchoBot20190805125647 旁边发布 - Web 部署**。
3. **"发布成功"** 消息将显示在 Visual Studio 输出窗口中，并且将启动一个网页，其中将显示"您的自动程序已准备就绪！
4. 打开 Windows 语音助理客户端应用，单击设置按钮（右上角的齿轮图标），并确保您仍然位于`de-de`"语言"字段中。
5. 按照运行 Windows[语音助理客户端](#run-the-windows-voice-assistant-client)中的说明重新连接到新部署的自动程序，使用新语言说话，并听到使用新语音用该语言进行自动回复。

## <a name="clean-up-resources"></a>清理资源

如果您不打算继续使用本教程中部署的回声机器人，可以通过删除 Azure 资源组**语音EchoBotTutorial-ResourceGroup**来删除它及其所有相关的 Azure 资源。

1. 从[Azure 门户](https://portal.azure.com)中，单击 Azure**服务**导航中的**资源组**。
2. 查找名为"**语音回声机器人教程-资源组"的资源组**。 单击三个点 （...）。
3. 选择“删除资源组”  。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用语音 SDK 构建您自己的客户端应用](quickstart-voice-assistant-csharp-uwp.md)

## <a name="see-also"></a>另请参阅

* 部署到[您附近的 Azure 区域](https://azure.microsoft.com/global-infrastructure/locations/)以查看自动程序响应时间改进
* 部署到[支持高质量神经 TTS 语音的 Azure 区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)
* 与直线语音通道关联的定价：
  * [机器人服务定价](https://azure.microsoft.com/pricing/details/bot-service/)
  * [语音服务](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* 构建和部署自己的启用语音的机器人：
  * 构建[一个机器人框架机器人](https://dev.botframework.com/)。 使用[直线语音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)注册，并[自定义您的自动操作语音](https://docs.microsoft.com/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0)
  * 探索现有的[机器人框架解决方案](https://microsoft.github.io/botframework-solutions/index)：构建[虚拟助手](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/)[并将其扩展到直线语音](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
