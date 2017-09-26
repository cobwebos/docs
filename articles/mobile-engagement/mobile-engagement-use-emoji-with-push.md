---
title: "在 Azure Mobile Engagement 中使用表情符号图标"
description: "如何在推送通知中使用表情符号图标"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 663317d7-3c93-4e8f-b13d-c6fb342124ee
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: bbb7ce5e95b229a7505c5e97b6866d5a302a1d27
ms.contentlocale: zh-cn
ms.lasthandoff: 03/01/2017

---
# <a name="use-emoji-emoticon-within-push-notifications"></a>如何在推送通知中使用表情符号图标
可以通过几个简单的步骤将表情符号图标包含在推送通知中： 

1. 首先，需要找到想在消息中发送的表情符号。 请确保选择的表情符号受目标设备支持，因为设备制造商需要一些时间才能将新批准的表情符号添加到设备平台。 
2. 在 **Windows** 上 - 可以导航到此[链接](http://apps.timwhitlock.info/emoji/tables/unicode)并复制“本机”图标。
   
    ![][7] 
3. 在 **Mac** 上 - 可以在“字典”应用程序中的“编辑”->“表情符号及符号”下找到表情符号。
   
    ![][6] 
4. 现在，转到 Azure Mobile Engagement 门户上的“**市场宣传**”选项卡。 选择推送通知的类型（公告、轮询等）。 本示例选择的是公告推送。
5. 指定通知的不同字段，直到囊括通知的文本。 这是将要添加表情符号图示的位置。 可以选择将其放入标题和/或消息。 将需要拖放或复制从上述位置找到的表情符号。 
   
    ![][1]
6. 完成通知的其他字段并保存。 
7. 在运行测试或激活公告时，将看到包含特定表情图示的通知。   
   
    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png


