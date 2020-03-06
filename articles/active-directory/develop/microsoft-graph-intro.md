---
title: Microsoft Graph API |Microsoft Docs
description: Microsoft Graph API 是一种 RESTful 的 web API，可用于访问 Microsoft 云服务资源。
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 65465c6897853617f7f33a989f97d01fb0518a5e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304415"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft Graph API 是一种 RESTful 的 web API，可用于访问 Microsoft 云服务资源。 注册应用并获取用户或服务的身份验证令牌后，可以向 Microsoft Graph API 发出请求。 有关详细信息，请参阅[Microsoft Graph 概述](https://docs.microsoft.com/graph/overview)。

Microsoft Graph 公开 REST Api 和客户端库，以访问以下 Microsoft 365 服务上的数据：
- Office 365 服务： Delve、Excel、Microsoft 预订、Microsoft 团队、OneDrive、OneNote、Outlook/Exchange、Planner 和 SharePoint
- 企业移动性和安全服务：高级威胁分析、高级威胁防护、Azure Active Directory、标识管理器和 Intune
- Windows 10 服务：活动、设备、通知
- Dynamics 365 Business Central

## <a name="versions"></a>版本

Microsoft Graph 当前支持两个版本：1.0 版和 beta 版。 V1.0 版本包含了一般可用的 Api。 使用1.0 版的所有生产应用。 Beta 版包含当前以预览版提供的 Api。 由于我们可能会引入对 beta Api 的重大更改，因此我们建议你仅使用 beta 版本来测试正在开发的应用;请勿在生产应用中使用 beta Api。 有关详细信息，请参阅[Microsoft Graph 的版本控制、支持和重大更改策略](https://docs.microsoft.com/graph/versioning-and-support)。

若要开始使用 beta Api，请参阅[Microsoft Graph beta endpoint reference](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta)

若要开始使用 v1.0 Api，请参阅[Microsoft Graph REST API 1.0 版参考](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>入门

若要读取或写入资源（例如用户或电子邮件），需要构造如下所示的请求：

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

有关构造请求的元素的详细信息，请参阅[使用 MICROSOFT GRAPH API](https://docs.microsoft.com/graph/use-the-api)

快速入门示例可用于说明如何访问 Microsoft Graph API 的强大功能。 可通过一种身份验证访问两个服务的示例： Microsoft 帐户和 Outlook。 每个快速入门都从 Microsoft 帐户用户的配置文件访问信息并显示其日历中的事件。
快速入门涉及四个步骤：
- 选择平台
- 获取你的应用 ID （客户端 ID）
- 生成示例
- 登录并查看日历上的事件

完成快速入门后，便拥有了一个可以运行的应用程序。 有关详细信息，请参阅[Microsoft Graph 快速入门常见问题解答](https://docs.microsoft.com/graph/quick-start-faq)。 若要开始学习示例，请参阅[Microsoft Graph 快速入门](https://developer.microsoft.com/graph/quick-start)。

## <a name="tools"></a>工具

Microsoft Graph 资源管理器是一种基于 web 的工具，您可以使用它来生成和测试使用 Microsoft Graph Api 的请求。 可在以下位置访问 Microsoft Graph 资源管理器： `https://developer.microsoft.com/graph/graph-explorer`。

Postman 是一种工具，你还可以使用它来生成和测试使用 Microsoft Graph Api 的请求。 可在以下位置下载 Postman： `https://www.getpostman.com/`。 若要在 Postman 中与 Microsoft Graph 进行交互，请使用 Postman 中的 Microsoft Graph 集合。 有关详细信息，请参阅[将 Postman 与 MICROSOFT GRAPH API 一起使用](https://docs.microsoft.comgraph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)。
