---
title: "故障排除指南-Api 的 azure Mobile Engagement"
description: "故障诊断 Azure Mobile Engagement 的 Api 的指南"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3efc8a52-2b74-4917-b887-815ae8277474
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: piyushjo
ms.openlocfilehash: a7ae0a83046f2d67b790f672dcd3ae261987357a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-guide-for-api-issues"></a>针对 API 问题的疑难解答指南
以下是可能存在的问题，你可能会遇到管理员如何与通过 Api 的 Azure Mobile Engagement 交互。

## <a name="syntax-issues"></a>语法问题
### <a name="issue"></a>问题
* 使用的 API （或意外的行为） 的语法错误。

### <a name="causes"></a>原因
* 语法问题：
  * 请务必检查你用于确认的选项可用的特定 api 的语法。
  * 使用 API 使用情况的一个常见问题是混淆 Reach API 和推送 API （应使用 Reach API 而不是推送 API 执行大多数任务）。 
  * 使用 SDK 集成和 API 使用的另一个常见问题是混淆 SDK 密钥和 API 密钥。
  * 连接到 Api 的脚本需要发送至少每隔 10 分钟的数据或连接将超时 （尤其是常见侦听数据的监视器 API 脚本中）。 若要防止超时，让您发送符合 XMPP ping 每隔 10 分钟保持会话存在与服务器的脚本。

### <a name="see-also"></a>另请参阅
* [API 文档][Link 4]
* [符合 XMPP 协议信息](http://xmpp.org/extensions/xep-0199.html)

## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>无法使用 API 来执行 Azure Mobile Engagement UI 中提供的相同操作
### <a name="issue"></a>问题
* 从 Azure Mobile Engagement UI 中工作的操作相关的 Azure Mobile Engagement API 从不起作用。

### <a name="causes"></a>原因
* 确认你可以从 Azure Mobile Engagement UI 执行的相同操作显示，已正确使用 SDK 集成的 Azure Mobile Engagement 这一功能。

### <a name="see-also"></a>另请参阅
* [UI 文档][Link 1]

## <a name="error-messages"></a>错误消息
### <a name="issue"></a>问题
* 使用 API 在运行时或在日志中显示的错误代码。

### <a name="causes"></a>原因
* 下面是一个复合的参考和基本故障排除的公共 API 状态代码数字列表：
  
        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>另请参阅
* [API 文档的每个具体的 api 的详细错误][Link 4]

## <a name="silent-failures"></a>无提示故障
### <a name="issue"></a>问题
* API 操作失败并显示在运行时或在日志中没有错误消息。

### <a name="causes"></a>原因
* 多个项将被禁用 Azure Mobile Engagement UI 中，如果它们不正确，集成，但将从 API 以静默方式失败，因此请务必先测试以查看其是否工作正常的 UI 中的相同功能。
* Azure Mobile Engagement，并具有许多高级的功能的 Azure Mobile Engagement 你尝试使用，需要单独集成到你的应用使用 SDK 作为单独的步骤才能使用它们。

### <a name="see-also"></a>另请参阅
* [故障排除指南-SDK][Link 25]

<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

