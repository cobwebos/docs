---
title: 快速入门：使用自定义命令创建语音助手
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何使用 Speech Studio 创建和测试基本的自定义命令应用程序。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 05b47a786fe845460177b66b5bd54cdb140c246e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289414"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>使用自定义命令创建语音助手

本快速入门介绍如何使用 Speech Studio 创建和测试基本的自定义命令应用程序。 你还可以从 Windows 客户端应用程序访问此应用程序。 通过**自定义命令**，可以轻松地构建经过优化的丰富语音命令应用程序，以实现语音首次交互体验。 它提供统一的创作体验、自动托管模型和相对较低的复杂性，从而帮助你集中精力为语音命令方案构建最佳解决方案。

## <a name="region-availability"></a>区域可用性
目前，自定义命令支持在以下区域创建的语音订阅：
* 美国西部
* 美国西部 2
* 美国东部
* 美国东部 2
* 北欧
* 西欧
* 美国中西部
* 印度中部
* 东亚
* 东南亚

## <a name="prerequisites"></a>必备条件

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">在支持自定义命令的区域中创建 Azure Speech 资源。<span class="docon docon-navigate-external x-hidden-focus"></span></a> 有关支持的区域的列表，请参阅上述 " **区域可用性** " 部分。
> * 下载示例 [智能房间 Lite](https://aka.ms/speech/cc-quickstart) json 文件。
> * 下载最新版本的 [Windows 语音助手客户端](https://aka.ms/speech/va-samples-wvac)。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>转到 Speech Studio 以获取自定义命令

1. 在 web 浏览器中，转到 [Speech Studio](https://speech.microsoft.com/)。
1. 输入登录到门户所需的凭据。

   默认视图是您的语音订阅列表。
   > [!NOTE]
   > 如果看不到 "选择订阅" 页，可以通过在顶部栏上的 "设置" 菜单中选择 "语音资源" 来进行导航。

1. 选择您的语音订阅，然后选择 " **转到工作室**"。
1. 选择 " **自定义命令**"。

   默认视图是在所选订阅下具有的自定义命令应用程序的列表。

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>将现有应用程序作为新的自定义命令项目导入

1. 选择 " **新建项目** " 以创建项目。

1. 在 " **名称** " 框中，输入 "项目名称" 作为 `Smart-Room-Lite` (或您选择的其他内容) 。
1. 在 " **语言** " 列表中，选择 " **英语 (美国) **"。
1. 选择 " **浏览文件** "，然后在 "浏览" 窗口中选择 " **SmartRoomLite.js** 文件。

    > [!div class="mx-imgBorder"]
    > ![创建项目](media/custom-commands/import-project.png)

1.  在 " **LUIS 创作资源** " 列表中，选择 "创作资源"。 如果没有有效的创作资源，请选择 "  **创建新的 LUIS 创作资源**" 创建一个资源。

    > [!div class="mx-imgBorder"]
    > ![创建资源](media/custom-commands/create-new-luis-resource.png)
    
    
    1. 在 " **资源名称** " 框中，输入资源的名称。
    1. 在 " **资源组** " 列表中，选择一个资源组。
    1. 在 " **位置** " 列表中，选择一个位置。
    1. 在 " **定价层** " 列表中，选择一个层。
    
    
    > [!NOTE]
    > 可以通过在 "资源组" 字段中输入所需的资源组名称来创建资源组。 选择 " **创建** " 后，将创建资源组。


1. 接下来，选择 " **创建** " 以创建项目。
1. 创建项目后，选择你的项目。
你现在应看到新的自定义命令应用程序的概述。

## <a name="try-out-some-voice-commands"></a>尝试一些语音命令
1. 选择右窗格顶部的 " **定型** "。
1. 训练完成后，请选择 " **测试** "，然后尝试以下最谈话：
    - 打开电视
    - 将温度设置为80度
    - 关掉
    - 电视
    - 设置 5 PM 的警报

## <a name="integrate-custom-commands-application-in-an-assistant"></a>在助手中集成自定义命令应用程序
你需要发布应用程序，然后才能从 Speech Studio 外部访问此应用程序。 若要发布应用程序，你将需要配置预测 LUIS 资源。  

### <a name="update-prediction-luis-resource"></a>更新预测 LUIS 资源


1. 在左窗格中选择 " **设置** "，然后在中间窗格中选择 "  **LUIS 资源** "。
1. 选择一个预测资源，或选择 " **创建新资源**" 创建一个。
1. 选择“保存”。
    
    > [!div class="mx-imgBorder"]
    > ![设置 LUIS 资源](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> 由于创作资源每月仅支持1000预测终结点请求，因此在发布自定义命令应用程序之前，mandatorily 需要设置 LUIS 预测资源。

### <a name="publish-the-application"></a>发布应用程序

选择右窗格顶部的 "  **发布** "。 发布完成后，将显示一个新窗口。 记下 " **应用程序 ID** " 和 " **语音资源密钥** " 的值。 需要这两个值才能从 Speech Studio 外部访问该应用程序。

此外，也可以通过选择 "设置" " **Settings**  >  **常规**" 部分获取这些值。

### <a name="access-application-from-client"></a>从客户端访问应用程序

在本文的范围内，我们将使用作为必备组件的一部分下载的 Windows 语音助手客户端。 解压缩文件夹。
1. 启动 **VoiceAssistantClient.exe**。
1. 创建新的发布配置文件，并输入 **连接配置文件**的值。 在 " **常规设置** " 部分中，输入 "值" " **订阅密钥** " (这与在发布应用程序时保存的 **语音资源密钥** 值相同) 、 **订阅密钥区域** 和 **自定义命令应用 ID**。
    > [!div class="mx-imgBorder"]
    > ![WVAC 创建配置文件](media/custom-commands/create-profile.png)
1. 选择 " **保存并应用配置文件**"。
1. 现在，通过语音/文本试用以下输入
    > [!div class="mx-imgBorder"]
    > ![WVAC 创建配置文件](media/custom-commands/conversation.png)


> [!TIP]
> 您可以单击 " **活动日志** " 中的条目来检查从自定义命令服务发送的原始响应。

## <a name="next-steps"></a>后续步骤

本文使用现有应用程序。 接下来，在操作 [方法部分](how-to-custom-commands-create-application-with-simple-commands.md)中，你将了解如何从头开始设计、开发、调试、测试和集成自定义命令应用程序。
