---
title: Microsoft Graph API
description: Microsoft Graph API 是一种 RESTful Web API，可用于访问 Microsoft 云服务资源。
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 13cbeca909b445d75fcbcf7b3751183421479d42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90052556"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft Graph API 是一种 RESTful Web API，可用于访问 Microsoft 云服务资源。 注册应用并获取用户或服务的身份验证令牌后，可以向 Microsoft Graph API 发出请求。 有关详细信息，请参阅 [Microsoft Graph 概述](/graph/overview)。

Microsoft Graph 公开 REST API 和客户端库以访问以下 Microsoft 365 服务上的数据：
- Microsoft 365 服务：Delve、Excel、Microsoft Bookings、Microsoft Teams、OneDrive、OneNote、Outlook/Exchange、Planner 和 SharePoint
- 企业移动性和安全性服务：高级威胁分析、高级威胁防护、Azure Active Directory、Identity Manager 和 Intune
- Windows 10 服务：活动、设备、通知
- Dynamics 365 Business Central

## <a name="versions"></a>版本

Microsoft Graph 当前支持两个版本：v1.0 和 beta 版本。 v1.0 版本包含了正式版 API。 对所有生产应用使用 v1.0 版本。 beta 版本包含当前为预览版的 API。 由于我们可能对 beta API 进行重大更改，因此建议你仅使用 beta 版本测试正在开发的应用；请勿在生产应用中使用 beta API。 有关详细信息，请参阅 [Microsoft Graph 的版本控制、支持和重大更改策略](/graph/versioning-and-support)。

若要开始使用 beta API，请参阅 [Microsoft Graph beta 终结点参考](/graph/api/overview?view=graph-rest-beta)

若要开始使用 v1.0 API，请参阅 [Microsoft Graph REST API v1.0 参考](/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>入门

若要读取或写入资源（例如用户或电子邮件），请构造如下所示的请求：

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

有关构造的请求的元素的详细信息，请参阅[使用 Microsoft Graph API](/graph/use-the-api)

可使用快速入门示例了解如何访问 Microsoft Graph API 的强大功能。 可用样本通过一种身份验证访问两个服务：Microsoft 帐户和 Outlook。 每个快速入门都访问 Microsoft 帐户用户的配置文件中的信息，并显示其日历中的事件。
快速入门包括四个步骤：
- 选择平台
- 获取应用 ID（客户端 ID）
- 生成示例
- 登录并查看日历上的事件

完成快速入门后，你便有了一个可随时运行的应用。 有关详细信息，请参阅 [Microsoft Graph 快速入门常见问题解答](/graph/quick-start-faq)。 若要开始学习这些示例，请参阅 [Microsoft Graph 快速入门](https://developer.microsoft.com/graph/quick-start)。

## <a name="tools"></a>工具

Microsoft Graph 浏览器是一种基于 Web 的工具，可用于通过 Microsoft Graph API 生成和测试请求。 可在以下位置访问 Microsoft Graph 浏览器：`https://developer.microsoft.com/graph/graph-explorer`。

Postman 是一种可用于通过 Microsoft Graph API 生成和测试请求的工具。 可在以下位置下载 Postman：`https://www.getpostman.com/`。 要与 Postman 中的 Microsoft Graph 进行交互，请使用 Postman 中的 Microsoft Graph 集合。 有关详细信息，请参阅[将 Postman 与 Microsoft Graph API 配合使用](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)。