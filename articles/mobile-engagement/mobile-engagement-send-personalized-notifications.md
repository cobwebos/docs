---
title: "使用 Azure Mobile Engagement 发送个性化通知"
description: "如何通过在通知中包括用户配置文件信息（如姓名）来发送个性化通知"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 51007089-156a-4bac-bb1b-a590633bf2a2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: all
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b6debaa3a27cfc2a24d23a9c586fce9962bd9605


---
# <a name="personalize-notifications-by-including-user-name"></a>通过包括用户名来个性化通知
要想使你的通知对你的应用用户而言更具吸引力，应考虑对其进行个性化设置。 一种有效的方法包括有选择地使用应用的用户名处理通知以使其更具个性化。 这里需要注意的一点 - 你应谨慎采用将用户名添加到通知的策略，因为如果过度使用此策略，可能会使某些应用用户产生反感。 你还应确保你让用户选择这些个人详细信息，并且完全同意之后，才能在移动应用中开始使用此类信息。 

从技术上讲，使用 Azure Mobile Engagement，你可以通过执行以下步骤完成通知的个性化设置，在这些步骤中我们将使用在通知中包括用户名的方案。 你将使用应用信息或标记的概念，它们的值可以由集成在移动应用中的 SDK 传递或通过 API 传递。 然后，这些应用信息或标记可以：

1. 用于基于应用信息的值将通知发送给特定的用户，或 
2. 用作通知中的占位符，将通知发送到该设备时，它将由特定于该设备/用户的值所替换。 

> [!IMPORTANT]
> 请注意，由于这种使用每个通知替换应用信息值的额外处理，发送通知的速度将有所降低。 
> 
> 

## <a name="register-app-info-in-the-mobile-engagement-portal"></a>在 Mobile Engagement 门户中注册应用信息
1) 首先在 Azure 门户中注册应用信息或标记。 为此，请转到“**设置**” -> “**标记(应用信息)**”。  

![][1]    

2) 单击“**新标记(应用信息)**”并提供 *user_name* 作为名称，*string* 作为类型，然后单击“**提交**”。 

![][2]

3) 最后，你将看到此注册的应用信息，如下所示：

![][3]

## <a name="send-app-info-from-the-client-sdk"></a>从客户端 SDK 发送应用信息
此处，我们使用的是 Windows 通用应用示例，但等效方法也可用于我们的其他 SDK。 

假设在对用户进行身份验证后，你有办法在移动应用中获取用户的配置文件信息（可能是他们的名字），你将在此处调用 `SendAppInfo` 方法并将你以前注册的 `user_name` 应用信息的值填充到 Mobile Engagement 服务后端。 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

## <a name="send-personalized-notifications"></a>发送个性化通知
现在你已准备就绪以使用此 **user_name** 发送通知。 

1) 转到 Mobile Engagement 门户的“**市场宣传**”选项卡上以创建一条通知，并且你可以采用以下格式在通知标题或正文中的任意位置使用此占位符。 

![][4]    

> [!NOTE]
> 未设置 user_name 应用信息的所有用户都不会获得任何通知。 如果在测试模式下运行通知市场活动，并且如果没有设置应用信息，那么我们将发送“?”字符来替换该占位符。 
> 
> 

2) 当 Mobile Engagement 将选择要发送此通知的设备时，它将查看此应用信息，并替换占位符中的值。  
例如，如果我们已经为用户设置了 `str = "Scott"`，则设备注册将与此用户的 **user_name = SCOTT** 应用信息相关联，而且此用户将看到采用以下格式的应用外推送通知。 

![][5]    

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png




<!--HONumber=Nov16_HO3-->


