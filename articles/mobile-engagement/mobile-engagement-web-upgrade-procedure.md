---
title: "Azure Mobile Engagement Web SDK 升级过程 | Microsoft Docs"
description: "Azure Mobile Engagement Web SDK 的最新更新和过程"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a20529b4-ec8d-4503-8ae9-09b5f0846d5b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: afa8037dcb7a53042fa606e2c4014b442d4be326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Azure Mobile Engagement Web SDK 升级过程
如果已将早期版本的 Azure Mobile Engagement Web SDK 集成到 Web 应用程序中，那么在升级 SDK 时需要考虑以下几点。

如果跳过 Mobile Engagement Web SDK 的多个版本，则可能需要在升级过程中完成若干个步骤。 例如，如果从 1.4.0 迁移到 1.6.0 时，首先要按步骤从 1.4.0 升级到 1.5.0。 然后，按步骤从 1.5.0 升级到 1.6.0。

无论从哪个版本升级，都请将任何早期版本的文件 azure engagement.js 替换为该文件的最新版本。

## <a name="upgrade-from-121-to-200"></a>从 1.2.1 升级到 2.0.0
本节介绍如何将 Mobile Engagement Web SDK 集成从由 Capptain SAS 提供的 Capptain 服务迁移到 Azure Mobile Engagement 应用。 如果从较早版本进行迁移，请参阅 Capptain 网站先迁移到 1.2.1，再应用以下过程。

此版本的 Mobile Engagement Web SDK 不支持 Samsung 智能电视、 Opera 电视、 webOS 或 Reach 功能。

> [!IMPORTANT]
> Capptain 和 Azure Mobile Engagement 不是同一服务。 下面的过程会仅重点介绍迁移客户端应用的方法。 迁移应用中的 Mobile Engagement Web SDK 不会将数据从 Capptain 服务器迁移到 Mobile Engagement 服务器。
> 
> 

### <a name="javascript-files"></a>JavaScript 文件
将文件 capptain-sdk.js 替换为文件 azure-engagement.js，然后相应地更新脚本导入项。

### <a name="remove-capptain-reach"></a>删除 Capptain Reach
此版本的 Mobile Engagement Web SDK 不支持 Reach 功能。 如果已将 Capptain Reach 集成到应用程序中，需要将其删除。

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

### <a name="remove-deprecated-apis"></a>删除已弃用的 API
来自 Capptain 的某些 API 在 Mobile Engagement Web SDK 中已经弃用。

删除对以下 API 的任何调用：`agent.connect`、`agent.disconnect`、`agent.pause` 和 `agent.sendMessageToDevice`。

从 Capptain 配置删除以下回调的任何实例：`onConnected`、`onDisconnected`、`onDeviceMessageReceived` 和 `onPushMessageReceived`。

### <a name="configuration"></a>配置
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

### <a name="javascript-apis"></a>JavaScript API
全局 JavaScript 对象 `window.capptain` 已重命名为 `window.azureEngagement`，但可以将 `window.engagement` 别名用于 API 调用。 不能将此别名用于定义 SDK 配置。

例如，`capptain.deviceId` 变成 `engagement.deviceId`，`capptain.agent.startActivity` 变成 `engagement.agent.startActivity`，依此类推。

