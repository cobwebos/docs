---
title: "Azure Mobile Engagement 用户界面 - 市场宣传活动"
description: "了解如何使用 Azure Mobile Engagement 创建和管理推送通知活动"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 2fe124a2-a86f-4136-81ba-a9d298ec798a
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d5110a33e13802fd6e5e0f075c7d3ddd521a1ef9


---
# <a name="how-to-create-and-manage-push-notification-campaigns"></a>如何创建和管理推送通知活动
通过提供发送推送通知所需的所有信息，你可以使用 UI 的“市场宣传”部分来创建新的包含复杂方案的推送活动。 根据以下四种活动类型，推送活动的选项会略有不同：公告、投票、数据推送和磁贴（仅 Windows Phone）。

### <a name="option-applies-to"></a>“选项”的适用情况：
* 语言︰所有（公告、投票、数据推送、磁贴）
* 活动︰所有（公告、投票、数据推送、磁贴）
* 通知：公告、投票
* 内容︰对于每种活动类型都是唯一的
* 受众︰所有（公告、投票、数据推送、磁贴）
* 期限︰公告、投票、磁贴
* 测试︰所有（公告、投票、数据推送、磁贴）

![市场宣传-活动 1][20]

## <a name="languages"></a>语言
你可以使用“语言”下拉菜单将一个不同的推送版本发送到已设置为使用不同语言的设备。 默认情况下，所有设备将收到同一个推送，不论将它们设置为使用何种语言。 将其设备设置为一种不同语言的用户将收到推送的默认语言版本。 许多推送活动选项允许你为所选的每种其他语言指定备用内容。 

![市场宣传-活动 2][21]

### <a name="language-differences-apply-to"></a>语言差异的适用情况︰
* 语言：除了默认语言之外，可以选择独特语言
* 活动︰对于所有语言都相同
* 通知：除了默认语言之外，对每种语言都是唯一的
* 内容：除了默认语言之外，对每种语言都是唯一的
* 受众：可按一种单独的语言标准筛选
* 期限︰对于所有语言都相同
* 测试︰可以一次发送到每种语言

### <a name="supported-languages"></a>支持的语言：
* 阿拉伯语 (ar) 
* 保加利亚语 (bg) 
* 加泰罗尼亚语 (ca) 
* 中文 (zh) 
* 克罗地亚语 (hr) 
* 捷克语 (cs) 
* 丹麦语 (da) 
* 荷兰语 (nl) 
* 英语 (en) 
* 芬兰语 (fi) 
* 法语 (fr) 
* 德语 (de) 
* 希腊语 (el) 
* 希伯来语 (he) 
* 北印度语 (hi) 
* 匈牙利语 (hu) 
* 印度尼西亚语 (id) 
* 意大利语 (it) 
* 日语 (ja) 
* 朝鲜语 (ko) 
* 拉脱维亚语 (lv) 
* 立陶宛语 (lt) 
* 马来语 (ms) 
* 挪威博克马尔语 (nb) 
* 波兰语 (pl) 
* 葡萄牙语 (pt) 
* 罗马尼亚语 (ro) 
* 俄语 (ru) 
* 塞尔维亚语 (sr) 
* 斯洛伐克语 (sk) 
* 斯洛文尼亚语 (sl) 
* 西班牙语 (es) 
* 瑞典语 (sv) 
* 塔加路语 (tl) 
* 泰语 (th) 
* 土耳其语 (tr) 
* 乌克兰语 (uk) 
* 越南语 (vi) 

## <a name="campaign"></a>活动
你可以使用“活动”部分来设置你的活动名称和类别，以及设置是否打算忽略推送活动的受众部分并改为通过 Reach API（和低级别 Push API 的某些元素）发送此活动。 类别可以与自定义通知模板一起使用来控制基于预定义设置的应用内通知。 你可以通过 Reach API 获取一个现有“类别”列表。

> [!WARNING]
> 如果使用市场宣传活动的“市场活动”部分中的“忽略受众，推送将仅通过 API 发送至用户”选项，则市场活动将不会自动发送，而需要通过市场宣传 API 手动发送。

![市场宣传-活动 3][22]

### <a name="option-applies-to"></a>“选项”的适用情况：
* 名称︰所有
* 类别：公告、投票
* 忽略受众，推送将仅通过 API 发送至用户︰所有

## <a name="notification"></a>通知
你可以使用“通知”部分来设置推送的基本设置，包括：推送的标题、消息、应用内映像，或者它是否可不予理会。 许多通知设置是特定于设备平台的。 你可以选择在“应用内”和/或“应用外”发送你的推送。 （请记住用户可以在其设备上在操作系统级别“选择加入”或“选择退出”应用外推送，并且 Azure Mobile Engagement 将不能覆盖此设置。 还要记要 Reach API 可以处理“应用内”和“应用外”推送。 推送 API 也可用于处理“应用外”推送。）推送可以使用图片或 HTML 内容进行自定义，包括用于链接到应用外部或链接到应用中另一个位置的深度链接（需要 Android SDK 2.1.0 或更高版本的意向类别）。 你可以更改图标或 iOS 锁屏提醒，并发送文本或 Web 内容（一个包含 html 内容、指向另一个应用内或应用外位置的 URL 链接的弹出窗口）。 你还可以使 Android 设备在接收到推送时响铃或振动。 （请记住，你将需要 Android 清单文件中正确的 SDK 权限以使设备响铃或振动。）对于 Android“大图”尺寸，目前尚没有行业标准，由于在每个设备上屏幕大小都不同，但 400x100 图片在几乎任何屏幕大小上都能正常显示。

### <a name="delivery-types"></a>传送类型：
* 仅应用外︰通知将在用户不使用应用程序时传送。
* 仅应用外通知需要来自 Apple 或 Google 的证书（APNS 或 GCM 证书）。
* 仅应用内︰仅当应用程序正在运行时通知才会显示。
* 通知使用 Capptain 传送系统送达用户。 你可以完全自定义推送的可视布局/显示。
* 随时︰此选项确保你无论应用程序是否在运行都发送通知。

![市场宣传-活动 4][23]

### <a name="option-applies-to"></a>“选项”的适用情况：
* 通知：公告、投票

## <a name="content"></a>内容
你可以使用“内容”部分修改公告、投票、数据推送和磁贴（仅 Windows Phone）的内容。 推送活动的内容设置是与活动类型相关的。 

### <a name="see-also"></a>另请参阅
* [UI 文档 - 市场宣传 - 推送内容][Link 29]

![市场宣传-活动 5][24]

## <a name="audience"></a>目标受众
你可以使用“受众”部分来定义一个项目标准列表以限制你的活动，或者基于自定义条件限制你的活动。 使用限制你受众的标准选项集，你可以向新用户推送，向老用户推送，或者仅向原生推送用户推送。 你还可以设置一个配额来限制接收推送的用户数量。 你可以手动编辑关于“如何筛选你的活动以包含目标用户的一个或多个条件”的表达式。 你可以手动键入受众表达式。 此类表达式必须显式定义条件之间的关系。 条件是用一个标识符来描述的，必须以大写字母开头且不能包含空格。 条件之间的关系可以使用 'and'、'or'、'not' 运算符以及 '('、')' 来描述。 示例：Criterion1 or (Criterion1 and not Criterion2)。

> [!NOTE]
> 在活动中包含大量受众的情况下，服务器端目标扫描可能会很慢，尤其是在你尝试同时启动多个活动时。

* 如果可能，请一次仅启动一个活动。
* 一次最多启动四个活动。
* 仅向你的活动用户推送（复选框“使用原生推送仅与可联系上的用户接洽”和“仅与活动用户接洽”），这样将仅需要扫描仍安装并使用应用的用户。
  定义受众之后，你可以使用模拟按钮来查明有多少用户将收到此推送。 这将计算此受众可能面向的已知用户数量（这是基于用户的随机样本的一个估计值）。 请注意，已卸载应用程序的用户也是此受众的一部分，但无法向其推送。

### <a name="see-also"></a>另请参阅
* [UI 文档 - 市场宣传 - 新推送条件][Link 28]

![市场宣传-活动 6][25]

### <a name="edit-expression"></a>编辑表达式
![市场宣传-活动 7][26]

### <a name="limit-your-audience-option-applies-to"></a>“限制受众”选项的适用情况︰
* 仅与部分用户接洽︰所有（公告、投票、数据推送、磁贴）
* 仅与老用户接洽︰所有（公告、投票、数据推送、磁贴）
* 仅与新用户接洽︰所有（公告、投票、数据推送、磁贴）
* 仅与空闲的用户接洽︰公告、投票、磁贴
* 仅与活动用户接洽︰所有（公告、投票、数据推送、磁贴）
* 使用原生推送仅与可联系上的用户接洽︰公告、投票

## <a name="time-frame"></a>期限
你可以使用“期限”部分设置何时将发送推送，或者可以保留期限为空白，立即启动活动。 请记住，使用最终用户的时区启动活动可能比你在亚洲的最终用户预期的启动时间早一天，在世界上的所有时区都与为你的活动设置的期限匹配之前，请一次仅发送小批量的推送。 使用最终用户的时区还可能会导致活动中的延迟，因为在启动推送之前必须从手机请求时间。

> [!NOTE]
> 没有结束日期的活动可能会将推送缓存在本地，并在你手动完成活动之后仍显示它们。 要避免此行为，请为活动指定一个具体的结束时间。

### <a name="see-also"></a>另请参阅
* [市场宣传 - 操作说明 - 计划][Link 3] 

![市场宣传-活动 8][27]

### <a name="settings-apply-to"></a>“设置”的适用情况︰
* 期限︰公告、投票、磁贴

## <a name="test"></a>测试
你可以使用“测试”部分在保存活动之前将此推送发送到你自己的测试设备。 如果你已经为此活动配置了任何自定义语言，那么就可以使用每种语言测试推送。 你可以从“我的帐户”设置测试设备。

> [!NOTE]
> 当你使用按钮“测试”推送时，不会记录任何服务器端数据，仅对于实际的推送活动，才会记录数据。

### <a name="see-also"></a>另请参阅
* [UI 文档 - 我的帐户][Link 14]

![市场宣传-活动 9][28]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
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




<!--HONumber=Dec16_HO2-->


