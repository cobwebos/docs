---
title: Visual Studio App Center 和 Azure 通知中心的移动应用中推送通知的重要性
description: 了解 App Center 的服务，这些服务使你能够与移动应用程序用户进行联系。
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 05a9bc6ccd26aaecd3e0bf615880e0543b8604f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795908"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>通过发送推送通知与应用程序用户联系 

无论您是要生成使用者还是企业应用程序，都希望用户主动使用您的应用程序。 经常需要与用户分享突发新闻、游戏更新、令人兴奋的产品更新或任何相关信息。 为了增加用户与用户的参与，并使其返回到应用程序，你需要一种方式来实时与用户进行通信。

推送通知提供了一种快速而有效的方式来与应用程序用户进行通信。 你可以在适当的时间联系到你的用户，并且可以通知他们所需的信息（通常是在移动设备上的弹出窗口或对话框中），而不考虑他们正在执行的操作。

## <a name="value-of-push-notifications"></a>推送通知的值
推送通知为最终用户和业务提供价值。

企业可以：
- 在正确的时间将消息发送到受支持的平台上，**直接与用户通信**。
- 通过向用户发送实时更新和提醒来**提高用户参与**度，鼓励他们定期与您的应用程序进行联系。
- **保留用户**并重新与已下载应用程序但不使用它再次成为活动状态的非活动用户进行沟通。
- 通过分析最终用户行为、分段最终用户和利用基于移动推送通知的市场活动，**提高转换率**。
- 通过向用户发送推送消息和及时更新来**升级产品和服务**。
- 通过向用户发送个性化的推送消息来**定位用户**。

对于应用程序用户，推送通知：
- 实时**提供值和信息**。
- **提醒用户**使用应用程序。

使用以下服务在移动应用中启用推送通知。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 推送](/appcenter/push/)服务使你可以通过将目标消息发送到 IOS、Android 和 Windows 用户来与用户进行交流，无需管理使用推送 NOTIFICATION SERVICES （PNS）将通知发送到设备的过程。 此服务构建在 Azure 通知中心之上，消除了通过提供强大的仪表板手动推送通知所带来的复杂性。

**关键功能**
- 跨各种平台向**移动设备发送推送通知**。
- 使用通知向应用程序发送数据、向用户显示消息或由应用程序触发操作。
- 通知目标： 
    - 向**所有已注册的设备**发送广播消息。
    - 根据设备信息和自定义属性将通知发送给生成的**受众**。
    - 向**特定用户**发送通知。
    - 将通知发送到**特定设备**。
- 在 App Center 门户中提供有关推送、设备、错误的**丰富遥测**。
- **平台支持**-IOS、Android、MacOS、Xamarin、响应本机、Unity 和 Cordova。

**参考**
- [注册 App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [开始 App Center 推送](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure 通知中心
[通知中心](/azure/notification-hubs/notification-hubs-push-notification-overview)提供了易于使用且向外扩展的推送引擎，可用于将通知发送到任何平台和任何后端（云或本地）。

**关键功能**
- 将**推送通知**从任何后端（云中或本地）发送到任何平台。
- 通过单个 API 调用**快速广播**到数百万台移动设备。
- 按照客户、语言和位置定制推送通知。
- 动态定义并通知**客户群**。
- 可**立即扩展**到数百万个移动设备。
- **平台支持**-IOS、Android、Windows、Kindle、百度。
        
**参考**
- [Azure 门户](https://portal.azure.com) 
- [Azure 通知中心入门](/azure/notification-hubs/)   
- [快速入门](/azure/notification-hubs/create-notification-hub-portal)
- [示例](/azure/notification-hubs/samples)
