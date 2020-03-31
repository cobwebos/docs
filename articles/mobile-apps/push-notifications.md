---
title: 使用可视化工作室应用中心和 Azure 通知中心移动应用中的推送通知的重要性
description: 了解可用于与移动应用程序用户互动的可视化工作室应用中心等服务。
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235331"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>通过发送推送通知与应用程序用户互动

无论您是在构建使用者应用程序还是企业应用程序，都希望用户主动使用您的应用程序。 有时，您希望与用户共享突发新闻、游戏更新、令人兴奋的优惠、产品更新或任何其他相关信息。 要增加与用户的参与度，并让他们返回到您的应用程序，您需要一种与用户进行实时通信的方法。

推送通知提供了一种与应用程序用户进行通信的快速高效方式。 您可以在正确的时间联系用户并通知用户所需的信息，通常使用移动设备上的弹出式项目或对话框，无论他们在做什么。

## <a name="value-of-push-notifications"></a>推送通知的值
推送通知为用户和企业提供价值。

企业可以：
- 通过在受支持的平台上在正确的时间发送消息，直接与用户通信。
- 通过向用户发送实时更新和提醒，鼓励他们定期与应用程序互动，提高用户参与度。
- 保留用户并重新与下载应用程序的非活动用户重新互动，但不要使用它再次处于活动状态。
- 通过分析用户行为、细分用户和利用基于移动推送通知的广告系列来提高转化率。
- 通过向用户发送推送消息和及时更新来推广产品和服务。
- 通过发送个性化的推送消息来定位用户。

对于应用程序用户，推送通知：
- 实时提供价值和信息。
- 提醒用户使用该应用程序。

使用以下服务在移动应用中启用推送通知。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
使用[应用中心推送](/appcenter/push/)，您可以将目标消息发送给 iOS、Android 和 Windows 用户，而无需使用推送通知服务 （PNS） 管理向设备发送通知的过程。 此服务构建在 Azure 通知中心之上，通过提供功能强大的仪表板，消除了手动推送通知的复杂性。

**主要功能**
- 跨各种平台向移动设备发送推送通知。
- 使用通知将数据发送到应用程序、向用户显示消息或触发应用程序的操作。
- 使用通知目标： 
    - 将消息广播到所有已注册设备。
    - 根据设备信息和自定义属性向访问群体发送通知。
    - 向特定用户发送通知。
    - 向特定设备发送通知。
- 使用应用中心门户中可用的推送、设备和错误的丰富遥测数据。
- 获得对 iOS、Android、macOS、Xamarin、反应原生、团结和科尔多瓦的平台支持。

**引用**
- [使用可视化工作室应用中心注册](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [开始应用中心推送](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure 通知中心
[通知中心](/azure/notification-hubs/notification-hubs-push-notification-overview)提供了易于使用和横向扩展的推送引擎。 您可以使用它向任何平台以及云或本地的任何后端发送通知。

**主要功能**
- 从任何后端、云或本地向任何平台发送推送通知。
- 通过单个 API 调用，快速广播推送到数百万个移动设备。
- 按客户、语言和位置定制推送通知。
- 动态定义和通知客户细分。
- 即时扩展到数百万台移动设备。
- 获得 iOS、Android、Windows、Kindle 和百度的平台支持。
        
**引用**
- [Azure 门户](https://portal.azure.com) 
- [Azure 通知中心入门](/azure/notification-hubs/)
- [快速入门](/azure/notification-hubs/create-notification-hub-portal)
- [示例](/azure/notification-hubs/samples)
