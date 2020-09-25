---
title: 群组聊天英雄示例
titleSuffix: An Azure Communication Services sample overview
description: 概述了使用 Azure 通信服务的聊天英雄示例，使开发人员能够详细了解该示例的内部工作原理并了解如何对其进行修改。
author: ddematheu
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 295c4bde64ad21a19d21fd48f2556114b26b202d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943373"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>群组聊天英雄示例入门

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> links to our Hero Sample repo need to be updated when the sample is publicly available.
---->

Azure 通信服务的群组聊天英雄示例演示了如何使用通信服务聊天 Web 客户端库来构建群组呼叫体验。

在本地计算机上运行该示例之前，我们将通过本示例快速入门了解其工作原理。 然后，使用你自己的 Azure 通信服务资源将此示例部署到 Azure。

> [!IMPORTANT]
> [从 GitHub 下载示例](https://github.com/Azure/Communication/tree/master/samples)

## <a name="overview"></a>概述

该示例同时包含了客户端应用程序和服务器端应用程序。 客户端应用程序是使用 Microsoft Fluent UI 框架的 React/Redux Web 应用程序。 此应用程序会将请求发送到 ASP.NET Core 服务器端应用程序，该服务器端应用程序会帮助客户端应用程序连接到 Azure。 

示例如下所示：

:::image type="content" source="./media/chat/landing-page.png" alt-text="屏幕截图显示示例应用程序登录页。":::

当你按下“开始聊天”按钮时，Web 应用程序从服务器端应用程序获取用户访问令牌。 然后使用该令牌将客户端应用连接到 Azure 通信服务。 检索到令牌后，系统会提示你指定在聊天中代表你的名称和表情符号。 

:::image type="content" source="./media/chat/pre-chat.png" alt-text="屏幕截图显示应用程序的聊天前屏幕。":::

配置显示名称和表情符号后，便可以加入聊天会话。 现在，你将看到核心聊天体验所在的主要聊天画布。

:::image type="content" source="./media/chat/main-app.png" alt-text="屏幕截图显示示例应用程序主屏幕。":::

主要聊天屏幕的组件：

- **主要聊天区域**：这是用户可以发送和接收消息的核心聊天体验。 若要发送消息，可以使用输入区域，然后按 Enter（或使用发送按钮）。 发送方使用正确的名称和表情符号对收到的聊天消息进行分类。 聊天区域中会出现两种类型的通知：1) 用户键入信息时的键入通知 2) 消息的发送和读取通知。
- **标头**：在这里，用户将看到聊天会话的标题、用于切换参与者和设置侧边栏的控件以及用于退出聊天会话的离开按钮。
- **侧边栏**：使用标题中的控件进行切换时，将在此处显示参与者和设置信息。 参与者侧边栏包含聊天的参与者列表以及用于邀请参与者加入聊天会话的链接。 设置侧边栏用于配置聊天会话标题。 

下面你将找到有关设置该示例的先决条件和步骤的详细信息。

## <a name="prerequisites"></a>必备知识

- 创建具有活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js（8.11.2 及更高版本）](https://nodejs.org/en/download/)
- [Visual Studio（2017 及更高版本）](https://visualstudio.microsoft.com/vs/)
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)（请确保安装与 Visual Studio 实例相对应的版本 [32 位和 64 位]）
- 创建 Azure 通信服务资源。 有关详细信息，请参阅[创建 Azure 通信资源](../quickstarts/create-communication-resource.md)。 需要为此快速入门记录资源连接字符串。

## <a name="locally-deploying-the-service--client-app"></a>本地部署服务和客户端应用

单会话聊天示例实质上是两个“应用程序”：客户端和服务器应用程序。

在 chat.csproj 上打开 Visual Studio 并在调试模式下运行，这将启动聊天前端服务。 当从浏览器访问服务器应用时，它会将流量重定向到本地部署的聊天前端服务。

可以通过使用聊天 URL 打开多个浏览器会话来模拟多用户聊天，从而在本地测试示例。

### <a name="before-running-the-sample-for-the-first-time"></a>第一次运行示例之前

1. 打开 PowerShell、Windows 终端、命令提示符或等效项的实例，然后导航到要将示例克隆到的目录。
2. `git clone`
3. 转到“Chat/ClientApp”文件夹并运行 `npm run setup`
   1. 如果看到错误 1，请在上面的输出中查找 URL，你需要转到该 URL 来授权客户端。 （URL 如下所示：`app.vssps.visualstudio.com/oauth2/authorize?clientid=...`）在浏览器中访问该 URL 后，请从浏览器窗口复制并运行该命令。
   2. 完成上一步后，再次运行命令 `npm run setup`。
4. 从 Azure 门户获取 `Connection String`。 有关连接字符串的详细信息，请参阅[创建 Azure 通信资源](../quickstarts/create-communication-resource.md)
5. 获取 `Connection String` 后，将连接字符串添加到“聊天”文件夹下的 Chat/appsettings.json 文件中。 在变量 `ResourceConnectionString` 中输入连接字符串。

### <a name="local-run"></a>本地运行

1. 转到“聊天”文件夹
2. 在 Visual Studio 中打开 `Chat.csproj` 解决方案
3. 运行 `Chat` 项目。*

*浏览器将在 localhost:5000（节点部署客户端应用的位置）打开。 Internet Explorer 不支持应用。

#### <a name="troubleshooting"></a>疑难解答

- 未生成解决方案，并在 NPM 安装/生成过程中引发错误

清除/重新生成 C# 解决方案

## <a name="publish-the-sample-to-azure"></a>将示例发布到 Azure

1. 右键单击 `Chat` 项目并选择“发布”。
2. 创建新的发布配置文件，并选择你的 Azure 订阅。
3. 发布之前，请使用 `Edit App Service Settings` 添加连接字符串，将 `ResourceConnectionString` 作为键填写，并提供连接字符串（从 appsettings.json 复制）作为值。

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../quickstarts/create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- 了解[聊天概念](../concepts/chat/concepts.md)
- 熟悉[聊天客户端库](../concepts/chat/sdk-features.md)

## <a name="additional-reading"></a>其他阅读材料

- [Azure 通信预览](https://github.com/Azure/communication-preview) - 了解有关聊天 Web sdk 的详细信息
- [Redux](https://redux.js.org/) - 客户端状态管理
- [FluentUI](https://developer.microsoft.com/fluentui#/) - Microsoft 支持的 UI 库
- [React](https://reactjs.org/) - 用于构建用户界面的库
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) - 用于构建 Web 应用程序的框架
