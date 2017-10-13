---
title: "Azure Mobile Engagement 的 Android SDK 集成"
description: "介绍了如何在 Android 应用中集成 Azure Mobile Engagement SDK"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: 35935e911f1f17989beb71978396c6d1b7d601d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Azure Mobile Engagement 的 Android SDK 集成
> [!div class="op_single_selector"]
> * [通用 Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

本文档介绍适用于 Azure Mobile Engagement Android SDK 的所有集成和配置选项。

## <a name="prerequisites"></a>先决条件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>高级功能
### <a name="reporting-features"></a>报告功能
可以添加以下功能：

1. [高级报告选项](mobile-engagement-android-advanced-reporting.md)
2. [位置报告选项](mobile-engagement-android-location-reporting.md)
3. [高级配置选项](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>通知:
[如何在 Android 应用中集成市场宣传（通知）](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM)：[如何使用 Mobile Engagement 集成 GCM](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM)：[如何使用 Mobile Engagement 集成 ADM](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>标记计划实施：
[如何在 Android 应用中使用高级 Mobile Engagement 标记 API](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>发行说明

### <a name="431-07172017"></a>4.3.1 (07/17/2017)
* 修复在调用 `EngagementAgentUtils.isInDedicatedEngagementProcess`（`EngagementApplication` 类也使用）时可能很少发生的故障。

### <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Android 8 支持（以前版本的 SDK 在 Android 8 上无法使用）。
* 不再存在对支持库的依赖性。
* 删除 `EngagementFragmentActivity` 类。
* 由于 Android 8 的[后台执行限制](https://developer.android.com/preview/features/background.html)，后台日志可能延迟到用户与设备交互时生成，这将在设备进入睡眠状态的情况下对延迟的“已发送”推送营销活动和“显示的系统通知”统计信息产生影响（仍将显示通知，并且将实时响铃和振动，在这些方面没有问题）。
* 由于[后台位置限制](https://developer.android.com/preview/features/background-location-limits.html)，Android 8 上的实时后台位置不会经常更新。

对于所有版本，请参阅[完整的发行说明](mobile-engagement-android-release-notes.md)。

## <a name="upgrade-procedures"></a>升级过程
如果已将旧版本的 SDK 集成到应用程序中，则请参阅[升级过程](mobile-engagement-android-upgrade-procedure.md)。

