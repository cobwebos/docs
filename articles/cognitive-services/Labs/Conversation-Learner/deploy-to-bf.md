---
title: 如何部署对话学习器机器人 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 学习如何部署对话学习器机器人。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bb977df92cf0ada1e50a929a9ea714313a70165a
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171470"
---
# <a name="how-to-deploy-a-conversation-learner-bot"></a>如何部署对话学习器机器人

本文介绍如何在本地或 Azure 中部署对话学习器机器人。

## <a name="prerequisite-determine-the-model-id"></a>先决条件：确定模型 ID 

若要在对话学习器 UI 之外运行机器人，则必须设置机器人要使用的对话学习器模型 ID，即对话学习器云中机器学习模型的 ID。  （与此不同的是，通过对话学习器 UI 运行机器人时，由 UI 选择模型 ID。）  

以下是获取模型 ID 的方法：

1. 启动机器人和对话学习器 UI。  有关完整说明，请参阅快速入门指南；概括而言：

    在一个命令窗口中：

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

    在另一命令窗口中

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

2. 打开浏览器，转到 http://localhost:5050 

3. 单击要为之获取 ID 的对话学习器模型

4. 单击左侧导航栏中的“设置”。

5. “模型 ID”GUID 显示在页面顶部附近。

## <a name="option-1-deploying-a-conversation-learner-bot-to-run-locally"></a>选项1：部署要在本地运行的对话学习器机器人

此选项会将机器人部署到本地计算机，并显示使用 Bot Framework 模拟器进行访问的方法。

### <a name="configure-your-bot-for-access-outside-the-conversation-learner-ui"></a>将机器人配置为在对话学习器 UI 之外对机器人进行访问

在本地运行机器人时，将应用程序 ID 添加到机器人的 `.env` 文件中：

    ```
    CONVERSATION_LEARNER_MODEL_ID=<YOUR_MODEL_ID>
    ```

然后启动机器人：

    ```
    [open a command window]
    cd my-bot
    npm start
    ```

机器人现在在本地运行。  可使用 Bot Framework 模拟器进行访问。

### <a name="download-and-install-the-emulator"></a>下载并安装模拟器

    ```
    git clone https://github.com/Microsoft/BotFramework-Emulator
    npm install
    npm run build
    npm start
    ```

### <a name="connect-the-emulator-to-your-bot"></a>将模拟器连接到机器人

1. 在模拟器左上角的“输入终结点 URL”框中，输入 `http://127.0.0.1:3978/api/messages`。  将其他字段留空，然后单击“连接”。

2. 你现在已经在与机器人交谈了。

## <a name="option-2-deploy-to-azure"></a>选项 2：部署到 Azure

采用与发布其他机器人类似的方式发布对话学习器机器人。 在较高级别，将代码上传到托管网站，设置相应配置值，然后使用各种渠道注册机器人。 本视频详细介绍了如何使用 Azure 机器人服务发布机器人。

部署机器人并开始运行后，即可使用 Azure 机器人渠道注册将机器人连接到不同渠道，如 Facebook、Teams、Skype 等。 有关该过程，请参阅文档： https://docs.microsoft.com/en-us/bot-framework/bot-service-quickstart-registration

以下是将对话学习器机器人部署到 Azure 的分步说明。  这些说明假定可从基于云的源（如 VSTS、GitHub、BitBucket 或 OneDrive）获得机器人源，并配置机器人以实现持续部署。

1. 登录位于 https://portal.azure.com 的 Azure 门户

2. 创建新的“Web 应用机器人”资源 

    1. 为机器人命名
    2. 单击“机器人模板”，依次选择“Node.js”、“基本”，然后单击“选择”按钮
    3. 单击“创建”以创建 Web 应用机器人。
    4. 等待 Web 应用机器人资源创建完成。

3. 在 Azure 门户中，编辑刚刚创建的 Web 应用机器人资源。

    1. 单击左侧的“应用程序设置”导航项
    1. 向下滚动到“应用设置”部分
    2. 添加这些设置：

        环境变量 | 值
        --- | --- 
        CONVERSATION_LEARNER_SERVICE_URI | "https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/"
        CONVERSATION_LEARNER_MODEL_ID      | 应用程序 ID GUID，通过模型“设置”下的对话学习器 UI 获取
        LUIS_AUTHORING_KEY               | 此模型的 LUIS 创作密钥
    
    4. 单击页面顶部附近的“保存”
    5. 打开左侧的“生成”导航项
    6. 单击“配置连续部署” 
    7. 单击部署下的“设置”图标
    8. 单击“所需设置”
    9. 选择可用于获取机器人代码的源，并配置该源。
