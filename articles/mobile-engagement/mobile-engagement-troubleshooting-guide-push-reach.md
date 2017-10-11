---
title: "Azure Mobile Engagement 故障排除指南 - 推送/市场宣传"
description: "Azure Mobile Engagement 中用户交互和通知问题的故障排除"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3f1886b7-1fdd-47f4-b6b0-d79f158d5ef3
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ef6f34404b97a6972fc136262920a1bdbc4117b0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>推送和送达问题故障排除指南
以下是 Azure Mobile Engagement 将信息发送至用户时可能会遇到的问题。

## <a name="push-failures"></a>推送失败
### <a name="issue"></a>问题
* 推送不能在应用中和/或应用外正常工作。

### <a name="causes"></a>原因
* 很多时候，推送失败指示 Azure Mobile Engagement、“市场宣传”或 Azure Mobile Engagement 的其他高级功能未正确集成，或者需要在 SDK 中进行升级以修复新的 OS 或设备平台中的已知问题。
* 只需分别测试一次应用内推送和应用外推送即可确定是应用内的问题还是应用外的问题。
* 在 UI 和 API 中进行测试，将此作为故障排除的步骤来查看可在这两个位置获取的其他错误信息。
* 只有 Azure Mobile Engagement 和“市场宣传”都集成到 SDK 中时，应用外推送才能工作。
* 证书无效或未正确使用 PROD 和DEV（仅限 iOS）版本时，推送无法工作。 （**注意：**在同一台设备上同时安装了应用的开发 (DEV) 和生产 (PROD) 版本时，“应用外”推送通知可能无法传递到 iOS，因为 Apple 可能会使关联证书的安全令牌失效。 要解决此问题，请卸载应用的 DEV 和 PROD 版本，并仅在设备上重新安装其中一个版本。）
* 不同平台中对应用外推送计数的处理不同（在设备上禁用原生推送时，iOS 显示的信息比 Android 要少，API 提供的推送统计信息比 UI 要多）。
* 应用外推送可能会被 OS 级别（iOS 和 Android）的客户阻止。
* 如果应用外推送未正确集成，则其会在 Azure Mobile Engagement UI 中显示为禁用，但在 API 中可能会自动失败。
* Azure Mobile Engagement 和“市场宣传”都集成到 SDK 中时，应用内推送才能工作。
* 在 SDK 中集成 Azure Mobile Engagement 和指定服务器后 GCM 和 ADM 推送才能工作（仅限 Android）。
* 应分别对应用内推送和应用外推送进行测试，以确定是“推送”问题还是“市场宣传”问题。
* 应用内推送要求应用程序处于打开状态以进行接收。
* 应用内推送经常设置为通过选择加入或选择退出应用程序信息标记进行筛选。
* 如果在“市场宣传”中使用自定义类别来显示应用内通知，则需要遵循通知的正确生命周期，否则用户取消通知时可能无法将其清除。
* 如果启动了一个无结束日期的市场活动，且设备接收了应用内通知但尚未显示该通知，则即使手动结束了市场活动，用户下次登录到应用程序时仍将收到此通知。
* 对于推送 API 中的问题，请确认确实要使用推送 API 而非市场宣传 API（因为市场宣传 API 的使用频率更高）且未混淆“payload”和“notifier”参数。
* 使用通过 WIFI 和 3G 连接的设备测试推送市场活动，以消除网络连接成为问题来源的可能性。

## <a name="push-testing"></a>推送测试
### <a name="issue"></a>问题
* 推送可以基于设备 ID 发送至特定设备。

### <a name="causes"></a>原因
* 对各个平台设置的测试设备不同，但在测试设备上引发应用程序中的事件并在门户中查找设备 ID 应该可以有效地找到所有平台的设备 ID。
* 使用 IDFA 和IDFV（仅限 iOS）版本的测试设备工作方式存在差异。

## <a name="push-customization"></a>推送自定义
### <a name="issue"></a>问题
* 高级推送内容项目无法工作（锁屏提醒、铃声、震动、图片等）。
* 推送中的链接在应用外、应用内、指向网站时和指向应用中的位置时无法工作。
* 推送统计信息显示推送发送至的人员数量与预期不符（过多或不足）。
* 推送重复，接收了两次。
* 无法注册用于 Azure Mobile Engagement 推送的测试设备（使用自己的 Prod 或 DEV 版应用程序）。

### <a name="causes"></a>原因
* 链接到应用程序中的指定位置需要指定“类别”（仅限 Android）。
* 需要在应用内创建单击推送通知后将用户重定向至备用位置的深层链接方案，并由应用程序和设备 OS 管理，而非直接由 Mobile Engagement 管理。 （**注意：**应用外通知不能和在 Android 中一样直接链接到 iOS 上的应用内位置。）
* 要使大图片推送正常工作，外部图像服务器必须能够使用 HTTP “GET”和“HEAD”（仅限 Android）。
* 在代码中，可以在键盘处于打开状态时禁用 Azure Mobile Engagement 代理，并在键盘关闭后立即让代码重新激活 Azure Mobile Engagement，使键盘不会影响通知的外观（仅限 iOS）。
* 一些项目不能在测试模拟中工作，只能在真实的市场活动（锁屏提醒、铃声、震动、图片等）中工作。
* 使用“测试”推送的按钮时，不会记录任何服务器端数据。 仅记录实际推送市场活动的数据。
* 为了便于隔离问题，请通过测试、模拟和实际的市场活动进行排查，因为它们各自的工作方式略有差异。
* 你"在应用程序"和"任何时间"活动的计划运行的时间的长度可以影响传递数字，因为仅将位于"应用"营销活动运行时的用户 （和其设置为接收通知"应用程序"外的设备设置的用户） 传送营销活动。
* 由于 Android 和 iOS 对应用外通知的处理方式存在差异，Android 和 iOS 版本应用程序的推送统计信息很难进行直接比较。 Android 提供的 OS 级别的通知信息多于 iOS。 在通知中心中接收、单击或删除原生通知时，Android 会进行报告，但 iOS 只会在单击通知时报告此信息。 
* 市场宣传活动的“推送”数量不同于“传递”数量，其主要原因是“应用内”和“应用外”通知的计数方式不同。 “应用内”通知由 Mobile Engagement 处理，而“应用外”通知由设备 OS 中的通知中心处理。

## <a name="push-targeting"></a>推送定位
### <a name="issue"></a>问题
* 内置定位未按预期工作。
* 应用信息标记定位未按预期工作。
* 地理位置定位未按预期工作。
* 语言选项未按预期工作。

### <a name="causes"></a>原因
* 请确保已通过 Azure Mobile Engagement UI 或 API 下载应用信息标识。
* 在应用程序级别限制推送速度或推送配额，或者在市场活动级别限制受众会阻止人员接收特定推送，即使其符合其他定位条件。 
* 设置“语言”不同于基于国家或地区定位，也不同于基于根据手机位置或 GPS 位置的地理位置定位。
* “默认语言”中的消息发送至所有未将设备设置为指定的备用语言之一的客户。

## <a name="push-scheduling"></a>推送计划
### <a name="issue"></a>问题
* 推送计划未按预期工作（发送过早或滞后）。

### <a name="causes"></a>原因
* 时区可能导致计划问题，尤其是在使用最终用户的时区时。
* 高级推送功能可能使推送滞后。
* 基于手机设置（而非应用信息标记）定位可能使推送滞后，因为 Azure Mobile Engagement 在发送推送前可能需要实时请求手机中的数据。
* 创建的市场活动无结束日期时，该活动会将推送存储在设备本机，即使该市场活动被手动中止，在下次打开应用程序时也会显示此推送。
* 如果同时启动多个市场活动，扫描用户群花费的时间会更长（尝试一次仅启动一个市场活动，最多四个，且仅针对活动用户，这样就无需扫描老用户）。
* 如果你使用"忽略受众，推送将发送到通过 API 的用户"选项"活动"部分中的 Reach 营销活动，营销活动不会自动发送的你将需要手动将其发送通过 Reach API。
* 如果在“市场宣传”中使用自定义类别来显示应用内通知，则需要遵循通知的正确生命周期，否则用户取消通知时可能无法将其清除。

