---
title: 如何使用 Node.js 创建对话学习器模型 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何使用 Node.js 创建对话学习器模型。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 68ff9c5402c3fa409999e9933a6c1f7bf6d5a089
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172324"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>使用 Node.js 创建对话学习器模型

对话学习器降低了生成机器人所存在的复杂性。 使用对话学习器可以建立允许手工编写的代码和机器学习的混合开发工作流，以减少编写机器人所需的代码量。 仍可编写模型某些固定部分的代码，例如，检查用户是否已登录，或者发出 API 请求来检查商店库存。 但是，可以从领域专家或开发人员提供的示例对话中学习状态和操作选择的其他变化。

## <a name="invitation-required"></a>需要邀请

*访问项目对话学习器需要邀请。*

项目对话学习器包括一个添加到机器人的 SDK，以及该 SDK 在执行机器学习时需要访问的一个云服务。  目前，访问项目对话学习器云服务需要邀请。  如果尚未收到邀请，请[请求邀请](https://aka.ms/conversation-learner-request-invite)。  在未收到邀请的情况下，无法访问云 API。

## <a name="prerequisites"></a>先决条件

- Node 8.5.0 或更高版本，以及 NPM 5.3.0 或更高版本。 从 [https://nodejs.org](https://nodejs.org) 安装。
  
- LUIS 创作密钥：

  1. 登录到 [http://www.luis.ai](http://www.luis.ai)。

  2. 在右上角单击自己的姓名，然后单击“设置”

  3. 创作密钥显示在生成的页面中

  （LUIS 创作密钥充当 2 个角色。  首先，它充当对话学习器的创作密钥。  其次，对话学习器使用 LUIS 来提取实体；LUIS 创作密钥用于代表你创建 LUIS 模型）

- Google Chrome Web 浏览器。 从 [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html) 安装。

- git。 从 [https://git-scm.com/downloads](https://git-scm.com/downloads) 安装。

- VSCode。 从 [https://code.visualstudio.com/](https://code.visualstudio.com/) 安装。 请注意，VSCode 不是必需的，但建议使用它。

## <a name="quick-start"></a>快速入门 

1. 安装并生成：

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > 在执行 `npm install` 期间，如果发生以下错误，可以忽略此错误：`gyp ERR! stack Error: Can't find Python executable`

2. 配置：

   在 `cl-bot-01` 目录中创建名为 `.env` 的文件。  该文件的内容应是：

   ```
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. 启动机器人：

    ```
    npm start
    ```

    这会在 `cl-bot-01/src/app.ts` 中运行一个泛型空机器人。

3. 运行对话学习器 UI：

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

4. 打开浏览器，转到 http://localhost:5050 

现在，你正在使用对话学习器，可以创建和训练对话学习器模型。  

> [!NOTE]
> 启动时，可以根据邀请使用项目对话学习器。  如果 http://localhost:5050 显示 HTTP `403` 错误，则表示你的帐户未收到邀请。  请[请求邀请](https://aka.ms/conversation-learner-request-invite)。

## <a name="tutorials-demos-and-switching-between-bots"></a>教程、演示以及在机器人之间切换

上述指令启动了泛型空机器人。  若要改为运行教程或演示机器人：

1. 如果已打开对话学习器 Web UI，请返回到 http://localhost:5050/home 处的模型列表。
    
2. 如果另一个机器人（例如 `npm start` 或 `npm run demo-pizza`）正在运行，请将其停止。  不需要停止 UI 进程或关闭 Web 浏览器。

3. 从命令行运行演示机器人（上述步骤 2）。  演示包括：

  ```bash
  npm run tutorial-general
  npm run tutorial-entity-detection
  npm run tutorial-session-callbacks
  npm run tutorial-api-calls
  npm run demo-password
  npm run demo-pizza
  npm run demo-storage
  npm run demo-vrapp
  ```

4. 在 Chrome 中通过加载 http://localhost:5050/home 切换到对话学习器 Web UI（如果尚未这样做）。 

5. 单击“导入教程”（只需执行一次）。  此过程大约需要花费一分钟，会将所有教程的对话学习器模型复制到你的对话学习器帐户。

6. 在对话学习器 UI 中，单击对应于已启动的演示的演示模型。

演示的源文件位于 `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>创建包含后端代码的机器人

1. 如果已打开对话学习器 Web UI，请返回到 http://localhost:5050/home 处的模型列表。
    
2. 如果某个机器人（例如 `npm run demo-pizza`）正在运行，请将其停止。  不需要停止 UI 进程或关闭 Web 浏览器。

3. 如果需要，请编辑 `cl-bot-01/src/app.ts` 中的代码。

4. 重新生成并重启机器人：

    ```bash    
    npm run build
    npm start
    ```

5. 在 Chrome 中通过加载 http://localhost:5050/home 切换到对话学习器 Web UI（如果尚未这样做）。 

6. 在 UI 中创建新的对话学习器模型，并开始训练。

7. 若要在 `cl-bot-01/src/app.ts` 中进行代码更改，请重复上述步骤，从步骤 2 开始。

## <a name="vscode"></a>VSCode

在 VSCode 中，每个演示以及 `cl-bot-01/src/app.ts` 中的“空机器人”都有对应的运行配置。  在 VSCode 中打开 `cl-bot-01` 文件夹。

## <a name="advanced-configuration"></a>高级配置

有一个 `.env.example` 模板文件显示了可以设置哪些环境变量来配置示例。

可以通过将 `.env` 文件添加到项目的根目录来调整这些端口，以避免计算机上运行的其他服务之间发生冲突：

```bash
cp .env.example .env
```

此文件使用标准配置，可让你在本地运行机器人，并开始使用对话学习器。  （以后若要将机器人部署到 Bot Framework，需要对此文件进行某些编辑。）

## <a name="support"></a>支持

- 在 [Stack Overflow](https://stackoverflow.com) 中使用“microsoft cognitive”标记问题
- 在 [User Voice 页](https://aka.ms/conversation-learner-uservoice)中请求功能
- 在 [github 存储库](https://github.com/Microsoft/ConversationLearner-Samples)中提问

## <a name="contributing"></a>供稿

本项目采用 [Microsoft 开源行为准则](https://opensource.microsoft.com/codeofconduct/)。 有关详细信息，请参阅[行为准则常见问题](https://opensource.microsoft.com/codeofconduct/faq/)；若要其他任何问题或意见，请联系 [opencode@microsoft.com](mailto:opencode@microsoft.com)。

## <a name="source-repositories"></a>源存储库

- [conversationlearner-samples](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-ui](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Hello world](./tutorials/1-hello-world.md)
