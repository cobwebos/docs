---
title: "Azure Mobile Engagement Windows Phone Silverlight SDK 概述 | Microsoft Docs"
description: "Azure Mobile Engagement 的 Windows Phone Silverlight SDK 概述"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0e3d2420-0509-4952-8891-392e3dad9aaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 51858d6daca27a75e7f87af507600c7a193c874f
ms.openlocfilehash: c4e8ceee4104c3d3a6c3e6b79322ba1cf8463b22


---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Azure Mobile Engagement 的 Windows Phone Silverlight SDK 概述
接下来将详细介绍如何在 Windows Phone Silverlight 应用中集成  Azure Mobile Engagement。 如果想要先进行尝试，请确保完成我们的 [15 分钟教程](mobile-engagement-windows-phone-get-started.md)。

单击查看 [SDK 内容](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>集成过程
1. 从这里开始：[如何在 Windows Phone Silverlight 应用中集成 Mobile Engagement](mobile-engagement-windows-phone-integrate-engagement.md)
2. 有关通知：[如何在 Windows Phone Silverlight 应用中集成 Reach（通知）](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. 标记计划实施：[如何在 Windows Phone Silverlight 应用中使用高级 Mobile Engagement 标记 API](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>发行说明
###<a name="331-11032016"></a>3.3.1 (11/03/2016)
*MicrosoftAzure.MobileEngagement* Nuget 包 **v3.4.1** 的一部分

* 稳定性改进。

对于较早版本，请参阅[完整的发行说明](mobile-engagement-windows-phone-release-notes.md)

## <a name="upgrade-procedures"></a>升级过程
如果已将我们较旧版本的 SDK 集成到应用程序中，则在升级 SDK 时必须考虑以下几点。

如果错过了几个版本的 SDK，则可能需要执行多个过程。 请参阅完整的[升级过程](mobile-engagement-windows-phone-upgrade-procedure.md)。 例如，如果从 0.10.1 迁移至 0.11.0，则必须首先按照“从 0.9.0 至 0.10.1”的过程操作，然后按照“从 0.10.1 至 0.11.0”的过程操作。

### <a name="from-200-to-330"></a>从 2.0.0 至 3.3.0
#### <a name="test-logs"></a>测试日志
现在可以启用/禁用/筛选由 SDK 生成的控制台日志。 若要对其自定义，请将属性 `EngagementAgent.Instance.TestLogEnabled` 更新为 `EngagementTestLogLevel` 枚举中提供的值之一，例如：

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>从较旧版本升级
请参阅[升级过程](mobile-engagement-windows-phone-upgrade-procedure.md)




<!--HONumber=Feb17_HO2-->


