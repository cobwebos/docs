---
title: "Azure Mobile Engagement Android SDK 的高级报告选项"
description: "介绍如何执行高级报告，以捕获 Azure Mobile Engagement Android SDK 的分析信息"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7da7abd5-19d6-4892-94d8-818e5424b2cd
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 2a1445afa2c2fca1a31ad9c012b9c8a917ebf65c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-reporting-with-engagement-on-android"></a>在 Android 上使用 Engagement 执行高级报告
> [!div class="op_single_selector"]
> * [通用 Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

本主题介绍 Android 应用程序中的其他报告方案。 可以将这些选项应用于在[入门](mobile-engagement-android-get-started.md)教程中创建的应用。

## <a name="prerequisites"></a>先决条件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

完成的教程相当直接且简单，但是有一些可以选择的高级选项。

## <a name="modifying-your-activity-classes"></a>修改 `Activity` 类
在[入门教程](mobile-engagement-android-get-started.md)中，只需使 `*Activity` 子类继承自相应的 `Engagement*Activity` 类。 例如，如果旧活动扩展了 `ListActivity`，可能会使其扩展 `EngagementListActivity`。

> [!IMPORTANT]
> 使用 `EngagementListActivity` 或 `EngagementExpandableListActivity` 时，请确保对 `requestWindowFeature(...);` 的任何调用在对 `super.onCreate(...);` 的调用之前进行，否则会发生崩溃。
> 
> 

可以在 `src` 文件夹中找到这些类，然后将其复制到项目中。 该类还存在于 **JavaDoc** 中。

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>备用方法：手动调用 `startActivity()` 和 `endActivity()`
如果无法或不想重载 `Activity` 类，则可以通过直接调用 `EngagementAgent` 方法来启动和结束活动。

> [!IMPORTANT]
> 即使在应用程序关闭时（Android 上的应用程序永远不会关闭），Android SDK 也从不调用 `endActivity()` 方法。 因此，*强烈*建议在“*所有*”活动的 `onResume` 回调中调用 `startActivity()` 方法，在“*所有*”活动的 `onPause()` 回调中调用 `endActivity()` 方法。 这是确保会话不被泄漏的唯一方法。 如果会话泄漏，Engagement 服务将永远不从 Engagement 后端断开（因为只要会话处于挂起状态，该服务就保持连接）。
> 
> 

下面是一个示例：

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

此示例类似于 `EngagementActivity` 类及其变体，它的源代码在 `src` 文件夹中提供。

## <a name="using-applicationoncreate"></a>使用 Application.onCreate()
置于 `Application.onCreate()` 中的任意代码和其他应用程序回调针对所有应用程序进程运行，包括 Engagement 服务。 这可能会产生不需要的负面影响，例如，不需要的内存分配和 Engagement 进程中的线程，或重复广播接收器或服务。

如果重写 `Application.onCreate()`，我们建议在 `Application.onCreate()` 函数的开头添加以下代码片段：

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

可以为 `Application.onTerminate()`、`Application.onLowMemory()` 和 `Application.onConfigurationChanged(...)` 执行相同的操作。

也可以扩展 `EngagementApplication`，而不是扩展 `Application`：仅在当前进程不是托管 Engagement 服务的进程时，回调 `Application.onCreate()` 执行进程检查并调用 `Application.onApplicationProcessCreate()`，相同的规则适用于其他回调。

## <a name="tags-in-the-androidmanifestxml-file"></a>AndroidManifest.xml 文件中的标记
在 AndroidManifest.xml 文件的服务标记中，`android:label` 属性允许选择 Engagement 服务名称，它显示在最终用户手机的“正在运行的服务”屏幕中。 我们建议将此属性设置为 `"<Your application name>Service"`（例如，`"AcmeFunGameService"`）。

指定 `android:process` 属性可确保 Engagement 服务在其自己的进程（在与应用程序相同的进程中运行 Engagement 会影响主要/UI 线程的响应能力）中运行。

## <a name="building-with-proguard"></a>使用 ProGuard 生成
如果使用 ProGuard 生成应用程序包，则需要保留某些类。 可以使用以下配置代码段：

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
     }
