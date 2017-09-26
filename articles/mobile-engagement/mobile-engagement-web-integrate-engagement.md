---
title: "Azure Mobile Engagement Web SDK 集成 | Microsoft Docs"
description: "Azure Mobile Engagement Web SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7d8eaa180e277741a583522ee62d68f5247b92bb
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>在 Web 应用程序中集成 Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

这篇文章中的此过程描述在 Web 应用程序中激活 Azure Mobile Engagement 分析和监视功能的最简单方法。

遵循这些步骤操作，以激活计算关于用户、会话、活动、崩溃和技术的所有统计数据所需的日志报告。 对于与应用程序相关的统计信息，例如事件、 错误和作业，必须使用 Azure Mobile Engagement API 手动激活日志报告。 有关详细信息，请了解[如何在 Web 应用程序中使用高级 Mobile Engagement 标记 API](mobile-engagement-web-use-engagement-api.md)。

## <a name="introduction"></a>介绍
[下载 Azure Mobile Engagement Web SDK](http://aka.ms/P7b453)。
Mobile Engagement Web SDK 附带一个 JavaScript 文件 azure-engagement.js，你必须在站点或 Web 应用程序的每一页中都包含该文件。

> [!IMPORTANT]
> 运行此脚本之前，必须运行一个用于为你的应用程序配置 Mobile Engagement 的脚本或代码片段。
> 
> 

## <a name="browser-compatibility"></a>浏览器兼容性
除了跨域 AJAX 请求之外，Mobile Engagement Web SDK 还使用本机 JSON 编码和解码（依赖 W3C CORS 规范）。 它与以下浏览器兼容︰

* Microsoft Edge 12+
* Internet Explorer 10+
* Firefox 3.5+
* Chrome 4+
* Safari 6+
* Opera 12+

## <a name="configure-mobile-engagement"></a>配置 Mobile Engagement
编写一个创建全局 `azureEngagement` JavaScript 对象的脚本，如以下示例所示。 由于你的站点可能有多个页面，此示例假定此脚本包含在每个页面中。 在此示例中，JavaScript 对象被命名为 `azure-engagement-conf.js`。

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

应用程序的 `connectionString` 值会显示在 Azure 门户中。

> [!NOTE]
> `appVersionName` 和 `appVersionCode` 都是可选的。 但是，我们建议你配置它们，以便分析可以处理版本信息。
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>在你的页面中包含 Mobile Engagement 脚本
通过以下方式之一，将 Mobile Engagement 脚本添加到你的页面中：

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

或者这个：

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>别名
加载 Mobile Engagement Web SDK 脚本之后，该郐本会创建 **engagement** 别名以访问 SDK API。 不能使用此别名定义 SDK 配置。 此别名在本文档中用作参考。

请注意，如果默认别名与页面中的另一个全局变量冲突，你可以在加载 Mobile Engagement Web SDK 之前按如下方式先在配置中重新定义它：

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>基本报告
Mobile Engagement 中的基本报告涵盖会话级统计信息，比如关于用户、会话、活动和崩溃的统计信息。

### <a name="session-tracking"></a>会话跟踪
Mobile Engagement 会话可以划分为一系列活动，每个会话由一个名称进行标识。

在经典网站中，我们建议在站点的每个页面上声明一个不同的活动。 对于当前页永远不会更改的网站或 Web 应用程序，你可能需要跟踪更小规模上的活动，例如在页面内。

无论哪种方式，要启动或更改当前用户活动，请调用 `engagement.agent.startActivity` 函数。 例如：

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

在关闭应用程序页之后三分钟内，Mobile Engagement 服务器会自动结束打开的会话。

或者，你可以通过调用 `engagement.agent.endActivity` 手动结束会话。 此操作会将当前用户活动设置为 'Idle'。  会话将结束 10 秒钟之后，除非对 `engagement.agent.startActivity` 的新调用恢复会话，否则会话将在 10 秒后结束。

你可以在全局用户参与对象中配置 10 秒的延迟，如下所示︰

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> 不能在 `onunload` 回调中使用 `engagement.agent.endActivity`，因为你不能在此阶段进行 AJAX 调用。
> 
> 

## <a name="advanced-reporting"></a>高级报告
（可选）如果想报告应用程序特定事件、错误和作业，则需要使用 Mobile Engagement API。 你可以通过 `engagement.agent` 对象访问 Mobile Engagement API。

你可以在 Mobile Engagement API 中访问 Mobile Engagement 的所有高级功能。 在文章[如何在 Web 应用程序中使用高级 Mobile Engagement 标记 API](mobile-engagement-web-use-engagement-api.md)中有关于 API 的详细说明。

## <a name="customize-the-urls-used-for-ajax-calls"></a>自定义用于 AJAX 调用的 URL
你可以自定义 Mobile Engagement Web SDK 使用的 URL。 例如，要重新定义日志 URL（日志记录的 SDK 终结点），你可以像下面这样重写配置︰

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

如果你的 URL 函数返回一个以 `/``//``http://` 或 `https://` 开头的字符串，则不会使用默认方案。 默认情况下，会对这些 URL 使用 `https://` 方案。 如果你想要自定义默认方案，请像下面这样重写配置︰

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };

