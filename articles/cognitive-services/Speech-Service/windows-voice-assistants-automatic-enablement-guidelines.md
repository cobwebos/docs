---
title: 适用于 Windows 上的语音助手的隐私准则
titleSuffix: Azure Cognitive Services
description: 默认情况下，为语音代理启用语音激活的说明。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997509"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>适用于 Windows 上的语音助手的隐私准则

向用户提供有关其语音数据的收集和使用方式的清楚信息，以及这些信息对其进行控制的重要信息，这一点非常重要。 这些隐私核心层面（*泄露*和*许可*）对于集成到 Windows 中的语音助手尤其重要，因为他们使用的始终侦听性质。

在 Windows 上创建语音助手的开发人员必须在其应用程序中包含清晰的用户界面元素，以反映助手的侦听功能。

> [!NOTE]
> 如果对助手应用程序提供适当的披露和同意（包括应用程序更新之后），则可能会导致在隐私问题得到解决之前，助手变为不可用声音激活。 

## <a name="minimum-requirements-for-feature-inclusion"></a>功能包含的最低要求

Windows 用户可以在中**`Settings > Privacy > Voice activation`** 查看和控制其助手应用程序的可用性。

 > [!div class="mx-imgBorder"]
 > [![隐私-应用-列出](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "助手应用程序的 Windows 语音激活隐私设置条目")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

若要使其包含在此列表中，应用程序必须：

1. 突出告诉其用户将侦听关键字，即使应用程序未运行，以及该关键字的内容
1. 包括有关如何使用用户语音数据的说明，包括链接或对相关隐私策略的引用
1. 通知用户除应用程序内设置外，用户还可以在中**`Settings > Privacy > Voice activation`** 查看和修改其隐私选项，还可以选择包括一个协议链接以`ms-settings:privacy-voiceactivation`进行直接访问

满足这些要求并获得 Microsoft 的批准后，助手应用程序将在语音激活应用列表中显示，一旦向`Windows.ApplicationModel.ConversationalAgent` api 注册后，用户将能够向应用程序授予关键字激活许可。 默认情况下，这两个设置`Off`都是，并要求用户手动访问 "设置" 页面以启用。

> [!NOTE]
> 在所有情况下，语音激活权限都需要麦克风权限。 如果助手应用程序没有麦克风访问权限，它将不能进行语音激活，并将在语音激活隐私设置中显示为禁用状态。

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>包含在麦克风许可中的其他要求

希望使用户能够更轻松、更流畅地选择使用语音激活的助手作者可以通过满足上述一些其他要求来实现此目的。 满足这些要求后，助手应用程序的标准、设备解锁语音激活设置将默认为`On`一次（仅一次），将麦克风访问权限授予该应用程序。 这样就无需在语音激活助手之前对设置进行额外的行程。

其他要求是助手应用程序必须：

1. **在**提示输入麦克风许可（例如，使用`AppCapability.RequestAccessAsync` API）之前，请向用户提供明显的指示，告诉应用程序要听用户使用的关键字，即使应用程序未运行，以及是否希望用户同意
2. 在请求麦克风访问或使用`Windows.ApplicationModel.ConversationalAgent` api**之前**，包含数据使用和隐私策略的所有相关信息
3. 在体验流公开音频捕获行为并请求权限时，请避免任何指令或前导语（例如，"在以下提示符下单击" 是 "）

满足这些要求后，合格的助手应用程序将出现在一系列符合语音激活的应用程序列表中`enabled` ，这是在授予麦克风访问权限后的状态。

> [!NOTE]
> 超出锁定的语音激活不符合麦克风访问的自动启用要求，仍要求用户访问语音激活隐私页，以便对助手启用上述锁定访问权限。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关 Windows 上的语音助手的最佳实践](windows-voice-assistants-best-practices.md)