---
title: "适用于 Web 应用的 Azure Mobile Engagement 入门 | Microsoft Docs"
description: "了解如何使用适用于 Web 应用的具有分析和推送通知功能的 Azure Mobile Engagement。"
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 04afe53a-4caf-4c80-bd75-20cc630cd75c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: hero-article
ms.date: 06/01/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 555342e88c912a3f43c578a40dc34933996ade4c
ms.openlocfilehash: ef75623a0b8c2a4d38f527a26cbc2cf070765302


---
# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>适用于 Web 应用的 Azure Mobile Engagement 入门
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

本主题介绍如何使用 Azure Mobile Engagement 来了解你的 Web 应用使用情况。

本教程需要的内容如下：

* Visual Studio 2015 或你选择的任何其他编辑器
* [Web SDK](http://aka.ms/P7b453) 

此 Web SDK 是预览版，目前仅支持分析，尚不支持发送浏览器或应用内推送通知。 

> [!NOTE]
> 若要完成本教程，你必须有一个有效的 Azure 帐户。 如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started)。
> 
> 

## <a name="setup-mobile-engagement-for-your-web-app"></a>为 Web 应用设置 Mobile Engagement
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="a-idconnecting-appaconnect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>将应用连接到 Mobile Engagement 后端
本教程提供的“基本集成”是收集数据的最低要求。

我们将使用 Visual Studio 创建一个基本 Web 应用来演示集成，但你也可以按照在 Visual Studio 外部创建任何 Web 应用程序的步骤。 

### <a name="create-a-new-web-app"></a>创建新的 Web 应用
以下步骤假定使用 Visual Studio 2015，步骤与 Visual Studio 早期版本中的类似。 

1. 启动 Visual Studio，然后在“主页”屏幕上，选择“新建项目”。
2. 在弹出窗口中，选择“Web” -> “ASP.Net Web 应用程序”。 输入应用“名称”、“位置”和“解决方案名称”，然后单击“确定”。
3. 在“选择模板”弹出窗口中，在“ASP.Net 4.5 模板”下选择“空”，然后单击“确定”。 

现在已创建一个新的空白 Web 应用项目，我们将向其中集成 Azure Mobile Engagement Web SDK。

### <a name="connect-your-app-to-mobile-engagement-backend"></a>将应用连接到 Mobile Engagement 后端
1. 在解决方案中创建名为 **javascript** 的新文件夹，并将 Web SDK JS 文件 **azure engagement.js** 添加到其中。 
2. 使用以下代码在此 javascript 文件夹中添加名称为 **main.js** 的新文件。 请确保更新连接字符串。 此 `azureEngagement` 对象将用于访问 Web SDK 方法。 
   
        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };
   
    ![具有 js 文件的 Visual Studio][1]

## <a name="enable-real-time-monitoring"></a>启用实时监视
为了开始发送数据并确保用户处于活动状态，必须向 Mobile Engagement 后端发送至少一个活动。 在 Web 应用上下文中的活动是一个网页。 

1. 在解决方案中创建名为 **home.html** 的新页面，并将其设置为 Web 应用的起始页。 
2. 通过在正文标记内添加以下内容，将先前添加的两个 javascript 包含到此页中。 
   
        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>
3. 更新正文标记以调用 EngagementAgent 的 `startActivity` 方法
   
        <body onload="engagement.agent.startActivity('Home')">
4. **home.html** 的外观如下所示
   
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

## <a name="connect-app-with-real-time-monitoring"></a>将应用与实时监视相连接
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

  ![][2]

## <a name="extend-analytics"></a>扩展分析
以下是当前适用于 Web SDK 的所有方法，可用于分析︰

1. 活动/网页︰
   
        engagement.agent.startActivity(name);
        engagement.agent.endActivity();
2. 事件
   
        engagement.agent.sendEvent(name, extras);
3. 错误
   
        engagement.agent.sendError(name, extras);
4. 作业
   
        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png




<!--HONumber=Nov16_HO2-->


