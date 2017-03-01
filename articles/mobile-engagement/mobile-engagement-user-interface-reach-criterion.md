---
title: "Azure Mobile Engagement 用户界面 - 市场宣传条件"
description: "了解如何通过 Azure Mobile Engagement 使用目标条件将推送活动发送到选定的部分用户"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: a4ed03a0-55b1-4dd8-b0bd-c475005afb66
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: afa6625e4c3ebe2a408b4bd956c8d2eb9ac6a34b
ms.lasthandoff: 12/08/2016


---
# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>如何使用目标条件将推送活动发送到选定的部分用户
使用“新条件”按钮，按特定条件确定访问群体是 Azure Mobile Engagement 中最重要的概念之一，可帮助你发送客户将作出响应的相关推送通知，而不是向每个人发送垃圾邮件。 你可以基于标准条件限制访问群体，并模拟推送以确定有多少人将接收通知。

**另请参阅：**

* [UI 文档 - 市场宣传 - 新推送活动][Link 27]

## <a name="audience-criteria-can-include"></a>访问群体条件可以包括：
* **技术人员：**可以基于可在“分析和监视器”部分中看到的相同技术信息确定目标。 **另请参阅：**[UI 文档 - 分析][Link 15]、[UI 文档 - 监视器][Link 16]
* **位置：**将“实时位置报告”与地理围栏结合使用的应用程序可将地理位置用作从 GPS 位置确定访问群体的条件。 “简易区域位置报告”调用也可用于从手机位置确定访问群体（“实时位置报告”和“简易区域位置报告”必须从 SDK 中激活）。 **另请参阅：**[SDK 文档 - iOS - 集成][Link 5]、[SDK 文档 - Android - 集成][Link 5]
* **市场宣传反馈：**可以基于其反馈确定访问群体，这些反馈包括以前市场宣传通知的反馈乃至公告、投票和数据推送的市场宣传反馈。 在参与两个或三个市场宣传活动后，与第一个相比你能够更好地确定访问群体。 通过将活动设置为不发送到已接收到特定的上一个活动的用户，还可用于筛选出已接收到含有类似内容的通知的用户。 你甚至可以禁止仍处于活动状态的特定活动包含的用户接收新推送。 **另请参阅：**[UI 文档 - 市场宣传 - 推送内容][Link 29]
* **安装跟踪：**可以根据用户安装应用的位置跟踪信息。 **另请参阅：**[UI 文档 - 设置][Link 20]
* **用户配置文件：**可以基于标准的用户信息确定目标，并且可以基于你已创建的自定义应用信息确定目标。 这包括当前登录的用户和已回答特定问题的用户，你已要求他们在应用自身中进行设置，而不是只是如何对以前的活动进行响应。 为你的应用定义的所有应用信息将显示在此列表上。
* 段：你还可以根据你已基于包含多个条件的特定用户行为创建的段确定目标。 为你的应用定义的所有段将显示在此列表上。 **另请参阅：**[UI 文档 - 细分市场][Link 18]
* **应用信息：**自定义应用信息标记可以从“设置”中进行创建以跟踪用户行为。 **另请参阅：**[UI 文档 - 设置][Link 20]

## <a name="example"></a>示例：
如果你想将公告仅推送到已执行应用内购买操作的部分用户。

1. 请转到你的应用程序设置页面，选择“应用信息”菜单，然后选择“新应用信息”
2. 注册称为“inAppPurchase”的新布尔应用信息
3. 用户成功地执行应用内购买后（通过使用 sendAppInfo("inAppPurchase", ...) 函数），使应用程序将此应用信息设置为“true”
4. 如果你不想从你的应用程序中执行此操作，则可以通过使用设备 API 从后端执行此操作
5. 然后，你只需使用将你的访问群体限制为将“inAppPurchase”设置为“true”的用户的条件来创建自己的公告

> [!NOTE]
> 基于应用信息标记以外的条件确定目标要求 Azure Mobile Engagement 在发送推送之前从用户的设备中收集信息，因此可能会导致延迟。 复杂的推送配置选项（如更新徽章）也可能延迟推送。 使用推送 API 中的“单稳”活动是 Azure Mobile Engagement 中绝对最快的推送方法。 仅将应用信息标记用作市场宣传活动（来自市场宣传 API 或 UI）的推送条件是第二快的方法，因为应用信息标记存储在服务器端。 将其他目标条件用于推送活动是最灵活但速度最慢的推送方法，因为 Azure Mobile Engagement 必须查询设备才能发送活动。

![市场宣传条件 1][29] 

## <a name="criterion-options-apply-to"></a>条件选项适用于：
* **技术人员**     
* 固件名称：固件名称
* 固件版本：固件版本
* 设备型号：设备型号
* 设备制造商：设备制造商
* 应用程序版本：应用程序版本
* 运营商名称：运营商名称、未定义
* 运营商国家/地区：运营商国家/地区、未定义
* 网络类型：网络类型
* 区域设置：区域设置
* 屏幕大小：屏幕大小
* **位置**      
* 上次已知区域：国家/地区、区域、地点
* 实时地理围栏：POI 列表（名称、操作）、圆形 POI（名称、纬度、经度、半径(单位: 米)）
* **市场宣传反馈**     
* 通知反馈：通知、反馈
* 投票反馈：投票、反馈
* 投票结果反馈：投票结果反馈、问题、选择
* 数据推送反馈：数据推送、反馈
* **安装跟踪**     
* 存储：存储、未定义
* 源：源、未定义
* **用户配置文件**     
* 性别：男性或女性、未定义
* 出生日期：运算符、日期、未定义
* 选择加入：true 或 false、未定义
* **应用信息**      
* 字符串：字符串、未定义
* 日期：运算符、日期、未定义
* 整数：运算符、数字、未定义
* 布尔：true 或 false、未定义
* **段**    
* 段的名称（来自下拉列表），排除（不属于此段的目标用户）。

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


