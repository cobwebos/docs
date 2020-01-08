---
title: Visual Studio App Center 和 Azure 通知中心的移动应用中推送通知的重要性
description: 了解可用于与移动应用程序用户合作的服务，如 Visual Studio App Center。
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 1717bf6da849218f60bae7dafca20cb304286976
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453131"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>通过发送推送通知与应用程序用户联系

无论你是要生成使用者还是企业应用程序，你都希望用户主动使用你的应用程序。 经常需要与用户分享突发新闻、游戏更新、令人兴奋的产品更新或任何其他相关信息。 若要提高用户的参与并使其返回到应用程序，你需要通过某种方式实时与用户进行通信。

推送通知提供了一种快速而有效的方式来与应用程序用户进行通信。 你可以在正确的时间与用户联系，并通知他们所需的信息（通常是在移动设备上的弹出项或对话框中），而不考虑它们的作用。

## <a name="value-of-push-notifications"></a>推送通知的值
推送通知可为用户和企业提供价值。

企业可以：
- 在正确的时间通过支持的平台发送消息，直接与用户通信。
- 通过向用户发送实时更新和提醒来提高用户参与度，鼓励他们定期与应用程序进行合作。
- 保留已下载应用程序的不活动用户的用户和 reengage，但不要使用它再次成为活动状态。
- 通过分析用户行为、分段用户和利用基于移动推送通知的市场活动，提高转换率。
- 通过向用户发送推送消息和及时更新来升级产品和服务。
- 通过发送个性化的推送消息来设定用户的目标。

对于应用程序用户，推送通知：
- 实时提供值和信息。
- 提醒用户使用应用程序。

使用以下服务在移动应用中启用推送通知。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
通过[App Center 推送](/appcenter/push/)，你可以向 IOS、Android 和 Windows 用户发送有针对性的消息，而无需管理使用推送通知服务（PNS）将通知发送到设备的过程。 此服务构建在 Azure 通知中心之上，消除了通过提供强大的仪表板手动推送通知所带来的复杂性。

**主要功能**
- 跨各种平台向移动设备发送推送通知。
- 使用通知向应用程序发送数据、向用户显示消息或由应用程序触发操作。
- 使用通知目标可以： 
    - 将消息广播到所有已注册的设备。
    - 根据设备信息和自定义属性向受众发送通知。
    - 向特定用户发送通知。
    - 将通知发送到特定设备。
- 在 App Center 门户中提供的推送、设备和错误上使用丰富的遥测。
- 获得适用于 iOS、Android、macOS、Xamarin、响应本机、Unity 和 Cordova 的平台支持。

**参考**
- [注册 Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [开始 App Center 推送](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure 通知中心
[通知中心](/azure/notification-hubs/notification-hubs-push-notification-overview)提供易于使用且向外扩展的推送引擎。 可以使用它将通知发送到任何平台，以及从云中或本地的任何后端发送通知。

**主要功能**
- 从云中或本地的任何后端向任何平台发送推送通知。
- 使用单个 API 调用快速广播到数百万台移动设备。
- 按照客户、语言和位置定制推送通知。
- 动态定义并通知客户群。
- 可立即扩展到数百万个移动设备。
- 获得适用于 iOS、Android、Windows、Kindle 和百度的平台支持。
        
**参考**
- [Azure 门户](https://portal.azure.com) 
- [Azure 通知中心入门](/azure/notification-hubs/)
- [快速入门](/azure/notification-hubs/create-notification-hub-portal)
- [示例](/azure/notification-hubs/samples)
