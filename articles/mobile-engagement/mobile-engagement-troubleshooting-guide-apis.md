---
title: Azure Mobile Engagement 故障排除指南 - API
description: Azure Mobile Engagement 故障排除指南 - API
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3efc8a52-2b74-4917-b887-815ae8277474
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: piyushjo
ms.openlocfilehash: 8d51aaf5196eabe18d92b34d97e98f9f665f2527
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshooting-guide-for-api-issues"></a>API 问题故障排除指南
> [!IMPORTANT]
> Azure Mobile Engagement 已在 2018 年 3 月 31 日停用。 此页将在不久之后删除。
> 

以下是在管理员如何通过 API 与 Azure Mobile Engagement 进行交互这一方面可能会遇到的问题。

## <a name="syntax-issues"></a>语法问题
### <a name="issue"></a>问题
* 使用 API 的语法错误（或意外行为）。

### <a name="causes"></a>原因
* 语法问题：
  * 请务必检查正在使用的特定 API 的语法，以确认相应的选项可用。
  * API 使用的常见问题是混淆 Reach API 和 Push API（大部分任务应使用 Reach API 而非 Push API 执行）。 
  * SDK 集成和 API 使用方面的其他常见问题是混淆 SDK 密钥和 API 密钥。
  * 连接到 API 的脚本需要至少每 10 分钟发送数据一次，否则连接会超时（在侦听数据的监视器 API 脚本中尤其常见）。 若要避免超时，每 10 分钟让脚本发送一个 XMPP ping，以保持会话在服务器在处于活动状态。

### <a name="see-also"></a>另请参阅
* [API 文档][Link 4]
* [XMPP 协议信息](http://xmpp.org/extensions/xep-0199.html)

## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>无法使用 API 执行在 Azure Mobile Engagement UI 中可用的同一操作
### <a name="issue"></a>问题
* 在 Azure Mobile Engagement UI 中起作用的操作在关联的 Azure Mobile Engagement API 中不起作用。

### <a name="causes"></a>原因
* 确认在 Azure Mobile Engagement UI 中可以执行同一操作，这说明已正确使用 SDK 集成 Azure Mobile Engagement 的此功能。

### <a name="see-also"></a>另请参阅
* [UI 文档][Link 1]

## <a name="error-messages"></a>错误消息
### <a name="issue"></a>问题
* 在运行时或日志中显示的使用 API 的错误代码。

### <a name="causes"></a>原因
* 这是常见 API 状态代码编号的组合列表，可供参考并进行初步的故障排除：
  
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
* [API 文档 - 适用于每个特定 API 上的详细错误][Link 4]

## <a name="silent-failures"></a>无提示故障
### <a name="issue"></a>问题
* API 操作失败后，在运行时或日志中未显示错误消息。

### <a name="causes"></a>原因
* 如果未正确集成项目，则许多项目会在 Azure Mobile Engagement UI 中禁用，但还是会在 API 中无提示地失败，因此请务必在 UI 中测试相同的功能，以检查其是否能正常工作。
* Azure Mobile Engagement 和尝试使用的 Azure Mobile Engagement 的许多高级功能，都需要先作为单独的步骤使用 SDK 分别集成到应用，才能使用这些功能。

### <a name="see-also"></a>另请参阅
* [故障排除指南 - SDK][Link 25]

<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
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

