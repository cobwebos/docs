---
title: 微软图形 API |微软文档
description: Microsoft 图形 API 是一种 RESTful Web API，使您能够访问 Microsoft 云服务资源。
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
ms.openlocfilehash: 6c1b4390282f6d54178365714b1e2e665b4cf061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136494"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft 图形 API 是一种 RESTful Web API，使您能够访问 Microsoft 云服务资源。 注册应用并获取用户或服务的身份验证令牌后，可以向 Microsoft 图形 API 发出请求。 有关详细信息，请参阅[微软图形概述](https://docs.microsoft.com/graph/overview)。

Microsoft Graph 公开 REST API 和客户端库以访问以下 Microsoft 365 服务的数据：
- Office 365 服务：Delve、Excel、微软预订、微软团队、OneDrive、OneNote、Outlook/Exchange、规划器和 SharePoint
- 企业移动和安全服务：高级威胁分析、高级威胁防护、Azure 活动目录、标识管理器和 Intune
- Windows 10 服务：活动、设备、通知
- Dynamics 365 Business Central

## <a name="versions"></a>版本

Microsoft Graph 目前支持两个版本：v1.0 和 beta。 v1.0 版本包括一般可用的 API。 对所有生产应用使用 v1.0 版本。 测试版包括当前处于预览状态的 API。 由于我们可能会对 beta API 引入重大更改，因此建议您仅使用测试版来测试正在开发中的应用;请勿在生产应用中使用 beta API。 有关详细信息，请参阅[Microsoft 图形的版本控制、支持和中断更改策略](https://docs.microsoft.com/graph/versioning-and-support)。

要开始使用测试版 API，请参阅[Microsoft 图形测试版终结点参考](https://docs.microsoft.com/graph/api/overview?view=graph-rest-beta)

要开始使用 v1.0 API，请参阅[Microsoft 图形 REST API v1.0 引用](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)

## <a name="get-started"></a>入门

要读取或写入资源（如用户或电子邮件），请构造如下所示的请求：

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

有关构造请求元素的详细信息，请参阅[使用 Microsoft 图形 API](https://docs.microsoft.com/graph/use-the-api)

快速入门示例可用于向您展示如何访问 Microsoft 图形 API 的强大功能。 可用的示例访问两个服务，其中一个身份验证：Microsoft 帐户和 Outlook。 每个快速入门都会从 Microsoft 帐户用户的配置文件中访问信息，并显示其日历中的事件。
快速入门包括四个步骤：
- 选择您的平台
- 获取应用 ID（客户端 ID）
- 生成示例
- 登录并查看日历上的事件

完成快速入门后，您有一个可以运行的应用。 有关详细信息，请参阅 Microsoft[图形快速入门常见问题解答](https://docs.microsoft.com/graph/quick-start-faq)。 要开始使用示例，请参阅[Microsoft 图形快速入门](https://developer.microsoft.com/graph/quick-start)。

## <a name="tools"></a>工具

Microsoft 图形资源管理器是一个基于 Web 的工具，您可以使用它使用 Microsoft 图形 API 生成和测试请求。 您可以在以下处访问 Microsoft 图形`https://developer.microsoft.com/graph/graph-explorer`资源管理器。

Postman 是一种工具，您还可以使用 Microsoft 图形 API 生成和测试请求。 您可以在以下位置下载邮递员`https://www.getpostman.com/`。 要在 Postman 中与 Microsoft 图形进行交互，请使用 Postman 中的 Microsoft 图形集合。 有关详细信息，请参阅使用[Postman 与 Microsoft 图形 API](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)。
