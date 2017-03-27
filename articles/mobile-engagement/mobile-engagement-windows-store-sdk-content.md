---
title: "Windows Universal 应用 SDK 内容"
description: "了解 Azure Mobile Engagement 的 Windows Universal 应用 SDK 的内容"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ea47cba55c9689e23eb3592ae2559649d3377055
ms.lasthandoff: 11/17/2016


---
# <a name="windows-universal-apps-sdk-content"></a>Windows Universal 应用 SDK 内容
本文档列出并介绍 SDK 在应用程序中部署的内容。

## <a name="the-resources-folder"></a>`/Resources` 文件夹
此文件夹中包含 Mobile Engagement 需要的所有资源。 还可以自定义这些资源以适用于你的应用。

* `EngagementConfiguration.xml`：Mobile Engagement 的配置文件，可在此处自定义 Mobile Engagement 设置（Mobile Engagement 连接字符串、报告故障等）。

### <a name="html-folder"></a>/html 文件夹
* `EngagementNotification.html`：应用内横幅的 `Notification` Web 视图 html 设计。
* `EngagementAnnouncement.html`：应用内插播式视图的 `Announcement` Web 视图 html 设计。

### <a name="images-folder"></a>/images 文件夹
* `EngagementIconNotification.png`：通知左侧显示的品牌图标，将其替换为你的品牌图标。
* `EngagementIconOk.png`：操作或验证按钮的市场宣传内容页的 `Ok` 图标。
* `EngagementIconNOK.png`：当市场宣传内容页面的验证按钮被禁用时使用的 `NOK` 图标。
* `EngagementIconClose.png`：消除按钮的市场宣传通知和内容的 `Close` 图标。

### <a name="overlay-folder"></a>/overlay 文件夹
* `EngagementPageOverlay.cs`：负责将 Engagement 市场宣传应用内 UI 添加到其子级的覆盖页。


