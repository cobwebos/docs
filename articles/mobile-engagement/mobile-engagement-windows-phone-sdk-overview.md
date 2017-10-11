---
title: "Azure Mobile Engagement Windows Phone Silverlight SDK 概述 |Microsoft 文档"
description: "Azure Mobile Engagement Windows Phone Silverlight SDK 概述"
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
ms.openlocfilehash: c4e8ceee4104c3d3a6c3e6b79322ba1cf8463b22
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Windows Phone 的 Azure Mobile Engagement Silverlight SDK 概述
从这里开始，以获取有关如何集成在 Windows Phone Silverlight 应用程序中的 Azure Mobile Engagement 的详细信息。 如果你想要试一试首先，请确保你完成我们[15 分钟教程](mobile-engagement-windows-phone-get-started.md)。

单击以查看[SDK 内容](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>集成过程
1. 从此处开始：[如何将 Mobile Engagement 集成在 Windows Phone Silverlight 应用程序](mobile-engagement-windows-phone-integrate-engagement.md)
2. 通知：[如何在 Windows Phone Silverlight 应用程序集成 Reach （通知）](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. 标记计划的实施：[如何使用 Windows Phone Silverlight 应用程序中标记 API 高级的 Mobile Engagement](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>发行说明
###<a name="331-11032016"></a>3.3.1 (11/03/2016)
属于*MicrosoftAzure.MobileEngagement* Nuget 包**v3.4.1**

* 稳定性改进。

有关早期版本，请参阅[完成发行说明](mobile-engagement-windows-phone-release-notes.md)

## <a name="upgrade-procedures"></a>升级过程
如果你已具有集成到你的应用程序我们 SDK 的较旧版本，你必须升级 SDK 时，请考虑以下几点。

你可能必须执行几个过程，如果你错过了多个版本的 SDK。 请参阅完整[升级过程](mobile-engagement-windows-phone-upgrade-procedure.md)。 例如，如果你将从迁移 0.10.1 到你必须首先按照"发件人 0.10.1 到 0.9.0"步骤的 0.11.0 然后"发件人到 0.11.0 0.10.1"过程。

### <a name="from-200-to-330"></a>从到 3.3.0 2.0.0
#### <a name="test-logs"></a>测试日志
产生的 SDK 的控制台日志现在可以启用/禁用/筛选。 要自定义这种情况，更新属性`EngagementAgent.Instance.TestLogEnabled`中提供的值之一`EngagementTestLogLevel`枚举，例如：

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>从较旧版本升级
请参阅[升级过程](mobile-engagement-windows-phone-upgrade-procedure.md)

