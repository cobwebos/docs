---
title: "Azure Mobile Engagement 用户界面的 Reach 营销活动"
description: "Laern 如何创建和管理推送通知市场活动使用 Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2fe124a2-a86f-4136-81ba-a9d298ec798a
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fc88db8db11d1ed12fa95c2087c9a32b21bf4de5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-and-manage-push-notification-campaigns"></a>如何创建和管理推送通知营销活动
用户界面的 Reach 部分可用于创建新的推送营销活动与复杂公式通过提供你需要发送推送通知的所有信息。 具体取决于四个市场活动类型的略有不同的推送营销活动的选项： 公告、 轮询、 数据推送，和磁贴 (仅适用于 Windows Phone)。

### <a name="option-applies-to"></a>选项适用于：
* 语言： 所有 （公告，轮询，数据推送，磁贴）
* 市场活动： 所有 （公告，轮询，数据推送，磁贴）
* 通知： 公告轮询
* 内容： 唯一的每个市场活动类型
* 受众： 所有 （公告，轮询，数据推送，磁贴）
* 时间范围： 公告，轮询，磁贴
* 测试： 所有 （公告，轮询，数据推送，磁贴）

![Reach Campaign1][20]

## <a name="languages"></a>语言
可以使用语言下拉列表菜单向都设置为使用不同语言的设备发送你的推送的不同版本。 默认情况下，所有设备将都接收同一个推送而不考虑它们都设置为使用何种语言。 设备设置为不同语言的用户将收到推送的默认语言的版本。 许多推送营销活动选项，可以为每个你选择的其他语言指定备用内容。 

![Reach Campaign2][21]

### <a name="language-differences-apply-to"></a>语言差异适用于：
* 唯一的语言可以选择除了默认语言的语言：
* 市场活动： 相同的所有语言
* 通知： 唯一的每种语言除了默认语言
* 内容： 唯一的每种语言除了默认语言
* 还可按不同的语言条件筛选受众：
* 时间范围： 适用于所有语言
* 一次，可能会测试： 发送到每种语言

### <a name="supported-languages"></a>支持的语言：
* 阿拉伯语 (ar) 
* 保加利亚语 (bg) 
* 加泰罗尼亚语 (ca) 
* 中文 （中文） 
* 克罗地亚语 (hr) 
* Czech (cs) 
* 丹麦语 (da) 
* 荷兰语 (nl) 
* 英语 (en) 
* 芬兰语 (fi) 
* 法语 (fr) 
* 德语 (de) 
* 希腊语 (el) 
* 希伯来语 （他） 
* 印地语 （高） 
* 匈牙利语 (hu) 
* 印度尼西亚语 (id) 
* 意大利语 （它） 
* 日语 （日本） 
* 朝鲜语 (ko) 
* 拉脱维亚语 (lv) 
* 立陶宛语 (lt) 
* 马来语 (macrolanguage) （毫秒） 
* 挪威语博克马尔语 (nb) 
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
* 乌克兰语 （英国） 
* 越南语 (vi) 

## <a name="campaign"></a>营销活动
营销活动部分可用于设置的名称和你的营销活动的类别，如同你打算忽略推送营销活动的受众部分并改为发送此营销活动通过 Reach API （和较低级别推送 API 使用的某些元素）。 类别可以用于控制应用内通知根据预定义设置的自定义通知模板。 你可以通过 Reach API 你现有"类别"的列表。

> [!WARNING]
> 如果你使用"忽略受众，推送将发送到通过 API 的用户"选项"活动"部分中的 Reach 营销活动，营销活动不会自动发送的你将需要手动将其发送通过 Reach API。

![Reach Campaign3][22]

### <a name="option-applies-to"></a>选项适用于：
* 名称： 所有
* 类别： 公告轮询
* 忽略受众，推送将发送到通过 API 的用户： 所有

## <a name="notification"></a>通知
你可以使用通知部分设置基本设置推送其中包括： 的标题推送、 消息、 在应用程序映像，或如果它是不重要。 许多通知设置都特定于你的设备平台。 您可以选择"在应用程序"或"应用程序"外，则将发送你推送和/或文件名。 （请记住，用户可以"选择加入"或"退出""外的应用程序的"推送在操作系统级别上他们的设备，Azure Mobile Engagement 将不能重写此设置。 另请记住 Reach API 处理"在应用程序"和"扩展的应用程序的"推送。 推送 API 可以用于处理过的"应用程序"外的推送。）使用图片或 HTML 内容，包括用于链接在您的应用程序外部或你的应用 （Android SDK 2.1.0 或更高版本所需的意向类别） 中的其他位置的深层链接，可以自定义推送。 你可以更改图标或 iOS 徽章中，并发送文本或 web 内容 （html 内容，URL 链接到内部或外部应用程序的另一个位置弹出项）。 此外可以将 Android 设备振铃也可以使用推送振动。 (请记住，你需要正确中你的 Android SDK 权限清单文件环或振动设备。)目前尚没有的行业标准有关 Android"大蓝景"的大小，因为的屏幕大小有不同的每个设备，但 400 x 100 图片从事几乎任何屏幕大小。

### <a name="delivery-types"></a>传递类型：
* 仅应用程序外： 用户不使用应用程序时，将传递通知。
* 应用程序仅通知外需要从 Apple 或 Google （APNS 或 GCM 证书） 的证书。
* 应用程序内仅： 仅在运行应用程序时将出现通知。
* 通知使用 Capptain 传递系统来将到达用户。 你可以完全自定义你推送可视布局/显示。
* 随时： 此选项可确保你发送的通知到应用程序正在运行。

![Reach Campaign4][23]

### <a name="option-applies-to"></a>选项适用于：
* 通知： 公告轮询

## <a name="content"></a>内容
内容部分可用于修改公告、 轮询、 数据推送，和磁贴 (仅适用于 Windows Phone) 的内容。 推送营销活动内容设置为特定于市场活动的类型。 

### <a name="see-also"></a>另请参阅
* [UI 文档的访问-将内容推送][Link 29]

![Reach Campaign5][24]

## <a name="audience"></a>受众
受众部分可以用于定义标准的项来限制你的营销活动或自定义的条件的活动的限制列表。 标准的选项来限制你的受众集，可以将推送到新的或旧用户还是仅对本机推送用户。 你还可以设置要限制的用户接收推送数的配额。 你可以手动编辑如何筛选活动以包含向目标用户的一个或多个条件的表达式。 您可以手动键入受众表达式。 此类表达式必须显式定义条件之间的关系。 条件描述必须以大写字母开头且不能包含空格的标识符。 可以使用来描述条件之间的关系 '和'，'或'，not 运算符，以及 '（'，'）。 示例:"Criterion1 或 （Criterion1 和不 Criterion2）"。

> [!NOTE]
> 与在活动中包含大受众，服务器端目标扫描可能会很慢，尤其是如果你尝试同时启动多个活动。

* 如果可能，一次仅启动一个营销活动。
* 在最，一次仅启动四个活动。
* 推送到你的活动用户 (复选框"面向仅可以使用本机推送访问的用户"和"仅面向活动用户")，以便只有你仍已安装的应用程序并使用它的用户才需要扫描。
  你的受众定义后，可用于模拟按钮找出多少用户将收到此请求。 这将计算此受众 （此估算值基于对用户的随机采样） 可能针对的已知用户的数量。 请注意，已卸载应用程序的用户也属于此受众，但无法访问。

### <a name="see-also"></a>另请参阅
* [UI 文档的访问的新推送条件][Link 28]

![Reach Campaign6][25]

### <a name="edit-expression"></a>编辑表达式
![Reach Campaign7][26]

### <a name="limit-your-audience-option-applies-to"></a>受众选项适用于的限制：
* 与仅部分用户接洽： 所有公告、 轮询、 数据推送 (磁贴）
* 仅面向旧用户： 所有公告、 轮询、 数据推送 (磁贴）
* 仅面向新用户： 所有公告、 轮询、 数据推送 (磁贴）
* 仅面向空闲用户： 公告，轮询，磁贴
* 仅面向活动的用户： 所有公告、 轮询、 数据推送 (磁贴）
* 与仅可以使用原生推送访问的用户接洽： 公告、 轮询

## <a name="time-frame"></a>时间范围
时间范围部分可用于发送推送或可以将时间范围留空以立即启动营销活动时设置。 请记住，使用最终用户的时区可能会启动营销活动早于你在亚洲的最终用户期望和发送的推送一次之前世界上的所有时区都匹配为你的活动设置时间范围小的批处理一天。 使用最终用户的时区也会导致延迟在市场活动中由于它具有在启动推送之前通过电话请求的时间。

> [!NOTE]
> 市场活动无结束日期可以缓存推送本地和仍显示它们后手动完成的活动。 若要避免此行为，特定市场活动结束时间。

### <a name="see-also"></a>另请参阅
* [访问-如何 Tos – 计划][Link 3] 

![Reach Campaign8][27]

### <a name="settings-apply-to"></a>设置适用于：
* 时间范围： 公告，轮询，磁贴

## <a name="test"></a>测试
测试部分可用于在保存营销活动之前将此请求发送到你自己的测试设备。 如果配置了此营销活动的任何自定义语言，你可以在每种语言中测试推送。 你可以设置从"我的帐户"测试设备。

> [!NOTE]
> 当您使用按钮以"test"时，将记录数据没有服务器端推送，数据时才会记录有关实际推送营销活动。

### <a name="see-also"></a>另请参阅
* [UI 文档-我的帐户][Link 14]

![Reach Campaign9][28]

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

