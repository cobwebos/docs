---
title: "Windows 通用应用 Engagement SDK 的高级配置"
description: "使用 Windows 通用应用的 Azure Mobile Engagement 的高级配置选项"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6d85dd5d-ac07-43ba-bbe4-e91c3a17690b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: cb9454212c94cf65093219c3d24c71277ede7877
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Windows 通用应用 Engagement SDK 的高级配置
> [!div class="op_single_selector"]
> * [通用 Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
> 
> 

此过程描述如何为 Azure Mobile Engagement Android 应用配置各种配置选项。

## <a name="prerequisites"></a>先决条件
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="advanced-configuration"></a>高级配置
### <a name="disable-automatic-crash-reporting"></a>禁用自动崩溃报告
可以禁用 Engagement 的自动崩溃报告功能。 然后，当未经处理的异常发生时，用户参与策略将不执行任何操作。

> [!WARNING]
> 如果禁用此功能，那么当应用中发生未经处理的崩溃时，Engagement 不会发送崩溃，**也**不会关闭会话和作业。
> 
> 

若要禁用自动故障报告，自定义配置，具体取决于你声明的方法：

#### <a name="from-engagementconfigurationxml-file"></a>在 `EngagementConfiguration.xml` 文件中
将报告崩溃设置为 `<reportCrash>` 和 `</reportCrash>` 标记之间的 `false`。

#### <a name="from-engagementconfiguration-object-at-run-time"></a>在运行时的 `EngagementConfiguration` 对象中
使用 EngagementConfiguration 对象将报告崩溃设置为 false。

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>禁用实时报告
默认情况下，Engagement 服务实时报告日志。 如果应用程序频繁报告日志，最好对日志进行缓冲并在固定时间一次性进行报告。 这称为"突发模式"。

若要执行此操作，调用方法：

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

参数值为**毫秒**。 每当要重新激活实时日志记录时，请调用该方法但不包含任何参数，或将值设置为 0。

突发模式虽然会略微增加电池寿命，但将对 Engagement 监视器造成影响：所有会话和作业持续时间均被取整为突发阈值（因此，短于突发阈值的会话和作业可能不可见）。 我们建议使用不长于 30000（30 秒）的突发阈值。 仅限保存 300 项日志。 如果发送的日志太长，可能会丢失某些日志。

> [!WARNING]
> 不能将突发阈值配置为小于一秒。 如果这样做，SDK 会显示错误跟踪，并将阀值自动重置为默认值，即零秒。 这会触发 SDK 实时报告日志。
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
