---
title: "Azure Mobile Engagement Windows Universal SDK 集成 | Microsoft Docs"
description: "适用于 Azure Mobile Engagement 的 Windows Universal SDK 集成"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 9ded187d-5c07-4377-a41c-ce205dd38b50
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.translationtype: Human Translation
ms.sourcegitcommit: 51858d6daca27a75e7f87af507600c7a193c874f
ms.openlocfilehash: d616ad58156a19e89b3e106639a38df67cbd0abb
ms.contentlocale: zh-cn
ms.lasthandoff: 02/09/2017

---
# <a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>适用于 Azure Mobile Engagement 的 Windows Universal SDK 集成
本文档介绍适用于 Azure Mobile Engagement Windows Universal SDK 的所有集成和配置选项。

## <a name="prerequisites"></a>先决条件
在开始本教程之前，必须先完成我们的 [15 分钟教程](mobile-engagement-windows-store-dotnet-get-started.md)。

## <a name="advanced-features"></a>高级功能
### <a name="reporting-features"></a>报告功能
你可以添加以下功能：

1. [高级报告选项](mobile-engagement-windows-store-advanced-reporting.md)
2. [高级配置选项](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>通知
[如何在 Windows Universal 应用中集成市场宣传（通知）](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>标记计划实施：
[如何在 Windows Universal 应用中使用高级的 Mobile Engagement 标记 API](mobile-engagement-windows-store-use-engagement-api.md)

## <a name="release-notes"></a>发行说明
### <a name="341-11032016"></a>3.4.1 (11/03/2016)

* 稳定性改进。

对于较早版本，请参阅[完整的发行说明](mobile-engagement-windows-store-release-notes.md)

## <a name="upgrade-procedures"></a>升级过程
如果已将较旧版本的 Engagement 集成到应用程序中，则在升级 SDK 时必须考虑以下几点。

如果你错过了几个版本的 SDK，则可能需要执行几个过程。 请参阅完整的[升级过程](mobile-engagement-windows-store-upgrade-procedure.md)。 例如，如果从 0.10.1 迁移至 0.11.0，则必须首先按照“从 0.9.0 至 0.10.1”的过程操作，然后按照“从 0.10.1 至 0.11.0”的过程操作。

### <a name="from-330-to-340"></a>从 3.3.0 至 3.4.0
#### <a name="test-logs"></a>测试日志
现在可以启用/禁用/筛选由 SDK 生成的控制台日志。 若要自定义，请将属性 `EngagementAgent.Instance.TestLogEnabled` 更新为 `EngagementTestLogLevel` 枚举中提供的值之一，例如：

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### <a name="resources"></a>资源
市场宣传覆盖已得到改进。 它是 SDK NuGet 软件包资源的一部分。

升级到新版本的 SDK 时，可以选择是否要保留资源的覆盖文件夹中的现有文件：

* 如果上一个覆盖正在为你工作，或者你要手动集成 `WebView` 元素，你可以决定保留现有文件，它仍将正常工作。
* 若要更新到新的覆盖，请将资源中的整个 `overlay` 文件夹替换为 SDK 包中的新文件夹（UWP 应用：升级完成后，你可以从 %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources 中获得新的覆盖文件夹）。

> [!WARNING]
> 使用新的覆盖重写在以前的版本上所做的任何自定义。
> 
> 

### <a name="upgrade-from-older-versions"></a>从较旧版本升级
请参阅[升级过程](mobile-engagement-windows-store-upgrade-procedure.md)


