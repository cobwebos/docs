---
title: "Windows 通用应用 SDK 内容"
description: "了解的 Azure Mobile Engagement 的 Windows 通用应用 SDK 的内容"
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
ms.openlocfilehash: b28d525ab16487b963772e23fdecd11f94dcabd1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="windows-universal-apps-sdk-content"></a>Windows 通用应用 SDK 内容
本文档列出并描述部署的应用程序中的 SDK 的内容。

## <a name="the-resources-folder"></a>`/Resources`文件夹
此文件夹包含 Mobile Engagement 需要的所有资源。 你还可以自定义它们以适应你的应用程序。

* `EngagementConfiguration.xml`: Mobile Engagement 的配置文件，这是可以在其中自定义移动用户参与策略设置 （Mobile Engagement 连接字符串，报表崩溃...）。

### <a name="html-folder"></a>/html 文件夹
* `EngagementNotification.html`: `Notification` Web 应用内横幅的视图 html 设计。
* `EngagementAnnouncement.html`: `Announcement` Web 应用内间隙视图的视图 html 设计。

### <a name="images-folder"></a>/images 文件夹
* `EngagementIconNotification.png`： 显示一个通知，左侧的品牌图标替换此品牌图标。
* `EngagementIconOk.png`: `Ok` Reach 内容页的操作或验证按钮的图标。
* `EngagementIconNOK.png`: `NOK` Reach 内容页的验证按钮被禁用时使用的图标。
* `EngagementIconClose.png`: `Close` Reach 通知和消除按钮的内容的图标。

### <a name="overlay-folder"></a>/overlay 文件夹
* `EngagementPageOverlay.cs`： 添加此服务负责贴页面访问给其子级中应用程序 UI。

