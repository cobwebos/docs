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
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987400"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>适用于 Windows 上的语音助手的隐私准则

向用户提供有关其语音数据的收集和使用方式的清楚信息，以及这些信息对其进行控制的重要信息，这一点非常重要。 这些隐私核心层面（ *泄露* 和 *许可* ）对于集成到 Windows 中的语音助手尤其重要，因为他们使用的始终侦听性质。

在 Windows 上创建语音助手的开发人员必须在其应用程序中包含清晰的用户界面元素，以反映助手的侦听功能。

> [!NOTE]
> 如果对助手应用程序提供适当的披露和同意（包括应用程序更新之后），则可能会导致在隐私问题得到解决之前，助手变为不可用声音激活。

## <a name="minimum-requirements-for-feature-inclusion"></a>功能包含的最低要求

Windows 用户可以在中查看和控制其助手应用程序的可用性 **`Settings > Privacy > Voice activation`** 。

 > [!div class="mx-imgBorder"]
 > [![屏幕截图显示了控制 Cortana 可用性的选项。 ](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "助手应用程序的 Windows 语音激活隐私设置条目")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

若要使其包含在此列表中，请联系上的 Microsoft winvoiceassistants@microsoft.com 开始。 默认情况下，用户将需要为中的新助手显式启用语音激活 **`Settings > Privacy > Voice Activation`** ，应用程序可以通过协议链接到 `ms-settings:privacy-voiceactivation` 。 允许的应用程序在运行并使用 api 后会出现在列表中 `Windows.ApplicationModel.ConversationalAgent` 。 当应用程序获取用户的麦克风同意后，其语音激活设置将是可修改的。

由于 Windows 隐私设置包含有关语音激活的工作方式的信息，并且具有控制权限的标准 UI，因此均可实现公开和同意。 助手将保留在此允许列表中，但前提是它不会：

* 通过助手误导或 misinform 用户了解语音激活或语音数据处理
* 会过度干扰另一助手
* 打破任何其他相关的 Microsoft 策略

如果发现了上述任何一个，则 Microsoft 可能会从允许列表中删除助手，直到问题得到解决。

> [!NOTE]
> 在所有情况下，语音激活权限都需要麦克风权限。 如果助手应用程序没有麦克风访问权限，它将不能进行语音激活，并将在语音激活隐私设置中显示为禁用状态。

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>包含在麦克风许可中的其他要求

希望使用户能够更轻松、更流畅地选择使用语音激活的助手作者可以通过满足其他要求来满足公开和同意的要求，而无需额外进入设置页面。 一旦获得批准，一旦用户将麦克风权限授予助手应用程序，语音激活就会立即可用。 为了满足此要求，助手应用程序必须执行以下操作， **然后再** 提示输入麦克风许可 (例如，通过使用 `AppCapability.RequestAccessAsync` API) ：

1. 为用户提供清晰且明显的指示，应用程序想要倾听用户对关键字的声音， *即使应用程序未在运行*，并且与用户同意
1. 包括有关数据使用情况和隐私策略的相关信息，例如官方隐私声明的链接
1. 避免任何指令或前导语 (例如，在捕获音频捕获行为的体验流中 "单击以下提示符下的" ) "

如果某个应用程序完成上述所有工作，则有资格启用语音激活功能以及麦克风同意。 winvoiceassistants@microsoft.com有关详细信息，请联系以了解首次使用体验。

> [!NOTE]
> 超出锁定的语音激活不符合麦克风访问的自动启用要求，仍要求用户访问语音激活隐私页，以便对助手启用上述锁定访问权限。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关 Windows 上的语音助手的最佳实践](windows-voice-assistants-best-practices.md)