---
title: "Azure Mobile Engagement Web SDK 概述 |Microsoft Docs"
description: "Azure Mobile Engagement Web SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 5bbc2fda-0f3f-43d0-a73d-0f2c0f8dc25b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 10/18/2016
ms.author: piyushjo
ms.openlocfilehash: 770a83131a3e661771db50b22ce7de25b2d541cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="azure-mobile-engagement-web-sdk"></a>Azure Mobile Engagement Web SDK
从此处可以了解如何在 Web 应用中集成 Azure Mobile Engagement 的所有详细信息。 如果想在开始使用 Web 应用之前做一些尝试，请查看 [15 分钟的教程](mobile-engagement-web-app-get-started.md)。

## <a name="integration-procedures"></a>集成过程
1. 了解[如何在 Web 应用中集成 Mobile Engagement](mobile-engagement-web-integrate-engagement.md)。
2. 有关标记计划实施，请了解[如何在 Web 应用中使用高级 Mobile Engagement 标记 API](mobile-engagement-web-use-engagement-api.md)。

## <a name="release-notes"></a>发行说明
### <a name="202-10182016"></a>2.0.2 (10/18/2016)
* 修复了隐私浏览模式崩溃 (Safari)。
* 修复了已禁用 Cookie 的浏览器崩溃。

对于所有版本，请参阅[完整的发行说明](mobile-engagement-web-release-notes.md)。

## <a name="upgrade-procedures"></a>升级过程
### <a name="upgrade-from-121-to-200"></a>从 1.2.1 升级到 2.0.0
以下各节描述如何将 Mobile Engagement Web SDK 集成从 Capptain SAS 提供的 Capptain 服务迁移到 Azure Mobile Engagement 应用。 如果要从早于 1.2.1 的版本迁移，请参阅 Capptain 网站先迁移到 1.2.1，再应用下面的过程。

此版本的 Mobile Engagement Web SDK 不支持 Samsung 智能电视、 Opera 电视、 webOS 或 Reach 功能。

> [!IMPORTANT]
> Capptain 和 Azure Mobile Engagement 是不同的服务，以下程序仅重点描述如何迁移客户端应用。 迁移应用中的 Mobile Engagement Web SDK 不会将数据从 Capptain 服务器迁移到 Mobile Engagement 服务器。
> 
> 

#### <a name="javascript-files"></a>JavaScript 文件
将文件 capptain-sdk.js 替换为文件 azure-engagement.js，然后相应地更新脚本导入项。

#### <a name="remove-capptain-reach"></a>删除 Capptain Reach
此版本的 Mobile Engagement Web SDK 不支持 Reach 功能。 如果已将 Capptain Reach 集成到应用程序，需要将其删除。

从页面删除 Reach CSS 导入项，同时删除相关的 .css 文件（默认文件为 capptain-reach.css）。

删除以下 Reach 资源：关闭图像（默认为 capptain-close.png）和品牌图标（默认为 capptain-notification-icon）。

删除用于应用内通知的 Reach UI。 默认布局如下所示：

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

删除用于文本、Web 公告和投票的 Reach UI。 默认布局如下所示：

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

从配置中删除 `reach` 对象（如果存在）。 如下所示：

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

删除任何其他 Reach 自定义项，例如类别。

#### <a name="remove-deprecated-apis"></a>删除已弃用的 API
来自 Capptain 的某些 API 在 Mobile Engagement Web SDK 中已经弃用。

删除对以下 API 的任何调用：`agent.connect`、`agent.disconnect`、`agent.pause` 和 `agent.sendMessageToDevice`。

从 Capptain 配置删除任何以下回调：`onConnected`、`onDisconnected`、`onDeviceMessageReceived` 和 `onPushMessageReceived`。

#### <a name="configuration"></a>配置
Mobile Engagement 使用连接字符串来配置 SDK 标识符，例如应用程序标识符。

将应用程序 ID 替换为连接字符串。 请注意，SDK 配置的全局对象已从 `capptain` 更改为 `azureEngagement`。

迁移前：

    window.capptain = {
      appId: ...,
      [...]
    };

迁移后：

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

应用程序的连接字符串显示在 Azure 门户中。

#### <a name="javascript-apis"></a>JavaScript API
全局 JavaScript 对象 `window.capptain` 已重命名为 `window.azureEngagement`，但可以将 `window.engagement` 别名用于 API 调用。 不能将此别名用于定义 SDK 配置。

例如，`capptain.deviceId` 变成 `engagement.deviceId`，`capptain.agent.startActivity` 变成 `engagement.agent.startActivity`，依此类推。

如果已将早期版本的 Azure Mobile Engagement Web SDK 集成到应用程序，请阅读[升级过程](mobile-engagement-web-upgrade-procedure.md)相关内容。

