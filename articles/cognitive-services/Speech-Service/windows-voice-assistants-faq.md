---
title: Windows 语音助手-常见问题
titleSuffix: Azure Cognitive Services
description: Windows 语音代理开发期间经常遇到的常见问题。
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: aa71057d3819acb335153ee5b4b65960320405be
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997410"
---
# <a name="samples-and-faqs"></a>示例和常见问题解答

## <a name="the-uwp-voice-assistant-sample"></a>UWP 声音助手示例

UWP 语音助手示例是 Windows 上的语音助手，适用于

- 演示如何使用 Windows ConversationalAgent Api
- 显示 Windows 上语音代理的最佳实践
- 为你的 MVP 代理应用程序提供可适应的应用和可重用组件
- 显示如何将直接线路语音与机器人框架或自定义命令结合使用，以实现端到端语音代理体验

随示例应用一起提供的文档通过正确清理的先决条件，演练语音激活和代理管理的代码路径。

> [!div class="nextstepaction"]
> [访问 UWP 示例的 Github 存储库](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>常见问题解答

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>如何实现与 Microsoft 联系以获取有限的访问功能令牌和关键字模型文件等资源吗？

联系winvoiceassistants@microsoft.com以请求这些资源。

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>我的应用程序在通过语音激活时显示在一个小窗口中。 如何从 compact 视图转换到完整应用程序窗口？

当应用程序第一次通过语音激活时，它会在精简视图中启动。 请阅读[语音激活预览版的设计指南](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview)，以获取有关 Windows 上的语音助手的不同视图和它们之间的转换。

若要将精简视图转换为完整应用视图，请使用 appView API `TryEnterViewModeAsync`：

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>为什么 Windows 上的语音助手功能只能为 UWP 应用程序启用？

由于与语音激活等功能相关的隐私风险，因此，UWP 平台的功能是必需的，以便 Windows 上的语音助手功能足够安全。

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>UWP 语音助手示例使用了直接行语音。 我是否必须在 Windows 上为语音助手使用直接行语音？

UWP 示例应用程序是使用直行语音和语音服务 SDK 开发的，演示了如何使用带有 Windows 会话代理功能的对话服务。 但是，可以使用任何服务进行本地和云关键字验证、语音到文本转换、bot 对话框和文本到语音转换。