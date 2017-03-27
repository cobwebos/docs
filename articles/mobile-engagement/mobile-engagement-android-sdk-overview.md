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
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2b89a13230439698854656f0bb5367f1ed35ce1c


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
你可以添加以下功能：

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
### <a name="423-08102016"></a>4.2.3 (08/10/2016)
* 不再存在任何 WIFI 锁。
* 修复了初始化前调用 getDeviceId 时出现的死锁状态（4.2.0 中介绍的问题）。

对于所有版本，请参阅[完整的发行说明](mobile-engagement-android-release-notes.md)。

## <a name="upgrade-procedures"></a>升级过程
如果已将旧版本的 SDK 集成到应用程序中，则请参阅[升级过程](mobile-engagement-android-upgrade-procedure.md)。




<!--HONumber=Nov16_HO3-->


