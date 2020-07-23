---
title: Windows 语音助手-概述
titleSuffix: Azure Cognitive Services
description: Windows 语音助手概述，包括可用的功能和开发资源。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: fbcb262fee6a2cc62bfe64e8a8589c92b4fe2b17
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997443"
---
# <a name="voice-assistants-on-windows"></a>Windows 上的语音助手

在 Windows 10 版本2004和更高版本上，语音助手应用程序可以利用 Windows ConversationalAgent Api 来实现完全支持语音的助手体验。

## <a name="voice-assistant-features"></a>语音助手功能

语音代理应用程序可以通过口述关键字激活，以启用无人参与的语音驱动体验。 当应用程序关闭并且屏幕被锁定时，语音激活就会正常运行。

此外，Windows 还提供了一组语音激活隐私设置，这些设置使用户可以在每个应用基础上控制语音激活和更高版本的锁定激活。

激活语音后，Windows 将正确管理多个活动代理，并通知每个语音助手是否已中断或停用。 这样，应用程序便可以正确管理中断和其他代理间事件。

## <a name="how-does-voice-activation-work"></a>语音激活是如何工作的？

代理激活运行时（AAR）是 Windows 中正在进行的进程，它按口述关键字或按下按钮来管理应用程序激活。 只要系统上至少有一个应用程序注册到系统，它就会从 Windows 开始。 应用程序通过 Windows SDK 中的 ConversationalAgent Api 与 AAR 交互。

当用户使用关键字时，系统上的软件或硬件关键字 spotter 将通知 AAR 已检测到关键字，同时提供关键字 ID。 AAR 反过来会将请求发送到 BackgroundService，以使用相应的应用程序 ID 启动应用程序。

### <a name="registration"></a>注册

首次运行语音激活的应用程序时，它会通过 ConversationalAgent Api 注册其应用程序 ID 和关键字信息。 AAR 在系统上注册具有硬件或软件关键字 spotter 的全局映射中的所有配置，使其能够检测应用程序的关键字。 该应用程序还[向后台服务注册](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task)。

请注意，这意味着，应用程序在运行一次且允许注册完成之前，不能通过语音激活。

### <a name="receiving-an-activation"></a>接收激活

收到来自 AAR 的请求后，后台服务将启动该应用程序。 应用程序通过中`App.xaml.cs`的 OnBackgroundActivated 生命周期方法接收信号，其中包含唯一的事件参数。 此参数通知应用程序它已被 AAR 激活，并应启动关键字验证。

如果应用程序成功验证关键字，则它可以发出在前台显示的请求。 如果此请求成功，应用程序会显示 UI，并继续与用户交互。

在口述活动应用程序的关键字时，AAR 仍会发出信号。 不过，它不是通过中`App.xaml.cs`的生命周期方法发出信号，而是通过 ConversationalAgent api 中的事件发出信号。

### <a name="keyword-verification"></a>关键字验证

触发应用程序启动的关键字 spotter 通过简化关键字模型来实现低功率消耗。 这允许关键字 spotter 为 "always on" 而不会对电源产生很大影响，但这也意味着关键字 spotter 可能会有很高的 "false 接受"，即使未说出关键字也是如此。 这就是语音激活系统在后台启动应用程序的原因：使应用程序有机会在中断用户的当前会话之前验证关键字是否已被读。 AAR 会在关键字之前的几秒钟后保存音频，并使应用程序可以对其进行访问。 应用程序可以使用它在同一音频上运行更可靠的关键字 spotter。

## <a name="next-steps"></a>后续步骤

- **查看设计准则：** 我们的[设计指南规定](windows-voice-assistants-best-practices.md)了在 Windows 10 上提供语音激活的最佳体验所需的关键工作。
- **访问入门页面：** 从[这里](how-to-windows-voice-assistants-get-started.md)开始，开始在 Windows 上实现语音助手的步骤，从设置开发环境到实现指南介绍。
- **试用示例应用**：若要体验这些功能，请访问[UWP Voice 助手示例](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)页，然后按照步骤进行操作以获取运行的示例客户端。
