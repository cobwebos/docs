---
title: 泄露设计模式
titleSuffix: Azure Cognitive Services
description: 公开的设计模式和最佳实践。
services: cognitive-services
author: angle
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: angle
ms.openlocfilehash: fde4a73dd4a95028f23563fb6a8ae36c0142fd39
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507315"
---
# <a name="disclosure-design-patterns"></a>泄露设计模式
现在，你&#39;已经确定了适合你的综合语音体验的正确[级别的披露](concepts-disclosure-guidelines.md#disclosure-assessment)，&#39;接下来可以探索潜在的设计模式。
## <a name="overview"></a>概述
你可以将各种公开的设计模式应用于综合语音体验。 如果披露评估的结果是 "高泄漏"，我们建议[**显式公开**](#explicit-disclosure)，这意味着完全传达合成语音的起源。 [**隐式泄漏**](#implicit-disclosure)包括一些提示和交互模式，这些模式可受益于语音体验，无论所需的公开级别是高还是低。
![种类的泄露模式](media/responsible-ai/disclosure-patterns/affordances.png)






| 显式泄露模式                                                                                                                                                                                    | 隐式泄漏模式                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[透明介绍](#transparent-introduction)<br> [语言透明介绍](#verbal-transparent-introduction)<br>  [显式 Byline](#explicit-byline)<br>  [自定义和校准](#customization-and-calibration)<br> [家长泄露](#parental-disclosure)<br> [提供机会来了解有关如何进行语音的详细信息](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [功能公开](#capability-disclosure)<br>[隐式提示和反馈](#implicit-cues--feedback)<br> [对话透明度](#conversational-transparency) |



使用下图直接引用适用于综合语音的模式。 此图表中的其他一些情况还适用于你的方案：<br/>



| 如果你的综合语音体验 。 | 建议 | 设计模式 |
| --- | --- | --- |
| 需要高度泄露  | 至少使用一个显式模式和隐式提示，以帮助用户生成关联。 |[显式公开](#explicit-disclosure)<br>[隐式公开](#implicit-disclosure)  |
| 需要低披露 | 泄露可能很小，也可能不必要，但可以从某些隐式模式中受益。 | [功能公开](#capability-disclosure)<br>[对话透明度](#conversational-transparency)  |
| 具有很高的参与度 | 长期构建并提供多个入口点以在用户旅程中进行披露。 强烈建议使用载入体验。 | [透明介绍](#transparent-introduction)<br>[自定义和校准](#customization-and-calibration)<br>[功能公开](#capability-disclosure) |
| 包括作为主要目标受众的子项 | 将家长作为主要的披露受众，并确保他们能够有效地将披露传达给孩子。  | [家长泄露](#parental-disclosure)<br>[语言透明介绍](#verbal-transparent-introduction)<br> [隐式公开](#implicit-disclosure)<br> [对话透明度](#conversational-transparency)  |
| 包括盲人用户或具有弱视目标的用户  | 包含所有用户，并确保任何形式的视觉对象披露都具有关联的可选文本或声音效果。 遵循对比度和显示大小的辅助功能标准。 使用听觉提示传达公开。  | [语言透明介绍](#verbal-transparent-introduction) <br>[听觉提示](#implicit-cues--feedback)<br>[Haptic 提示](#implicit-cues--feedback)<br>[对话透明度](#conversational-transparency)<br>[辅助功能标准](https://www.microsoft.com/accessibility) |
| 无屏幕，无设备，或使用语音作为主要或仅交互模式 | 使用听觉提示传达公开。 | [语言透明介绍](#verbal-transparent-introduction) <br> [听觉提示](#implicit-cues--feedback)  |
| 可能包括多个用户/侦听器（例如，多家家庭的个人助手）  | 注意各种用户上下文和理解级别，并为用户旅程提供多种机会。  | [透明简介（返回用户）](#transparent-introduction)<br> [提供机会来了解有关如何进行语音的详细信息](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [对话透明度](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>显式公开
如果你的综合语音体验需要较高的披露，则最好使用以下显式模式中的至少一种来清楚地声明综合性质。
### <a name="transparent-introduction"></a>透明介绍

在语音体验开始之前，请通过完全透明地介绍数字助理的语音及其功能。 使用此模式的最佳时间是在加入新用户或向返回的用户引入新功能时使用。 在简介期间实现隐式提示有助于用户形成有关数字代理的综合性质的心理模型。

#### <a name="first-time-user-experience"></a>首次用户体验

首次运行体验期间 ![透明介绍](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*当加入新用户时，会引入综合语音。*

建议
- 说明语音为人工（例如 &quot;数字&quot;）
- 描述代理可以执行的操作
- 明确陈述语音&#39;来源
- 提供一个入口点来了解有关综合语音的详细信息

#### <a name="returning-user-experience"></a>返回用户体验

如果用户跳过载入体验，请继续提供透明引入体验的入口点，直到用户第一次触发声音。
<br/>

返回用户体验期间 ![透明简介](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*为综合语音体验提供一致的入口点。允许用户在用户旅程中的任何时间点首次触发语音时返回载入体验。*


### <a name="verbal-transparent-introduction"></a>语言透明介绍

指示数字助理&#39;的源的一种口述提示，它本身就足以实现泄露了。 此模式最适用于高泄漏方案，其中语音是唯一可用的交互模式。
<br/>

![口头方式传达口头透明介绍](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*如果用户体验中有时间出现，你可能已在其中引入或属性&#39;，请使用透明简介。*


第一人称 ![口头方式传达口头透明介绍](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*对于其他透明度，语音参与者可以在第一个用户公开合成语音的来源。*

### <a name="explicit-byline"></a>显式 Byline

如果用户将与音频播放器或交互式组件进行交互以触发声音，请使用此模式。


新闻媒体方案中 ![显式 byline](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*明确的 byline 是指语音来源的归属。*

建议

- 提供入门点来了解有关合成语音的详细信息

### <a name="customization-and-calibration"></a>自定义和校准

为用户提供对数字助理如何响应的方式（即语音声音的方式）的控制。  如果用户在自己的条件下与系统交互，并考虑到特定目标，则根据定义，他们已经认识到它&#39;不是真正的人。

#### <a name="user-control"></a>用户控件

提供有意义且明显影响综合语音体验的选择。

![用户首选项](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*用户首选项允许用户自定义和改进其体验。*

建议

- 允许用户自定义语音（例如，选择语言和语音类型）
- 向用户提供一种方法来使系统响应其唯一语音（例如，语音校准、自定义命令）
- 优化用户生成或上下文交互（例如提醒）

#### <a name="persona-customization"></a>角色自定义

提供自定义数字助手&#39;语音的方式。 如果语音基于名人或广泛识别的用户，请考虑在用户预览语音时使用视觉和讲述的简介。

![语音自定义](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*提供从一组声音中进行选择的功能有助于表达人工性质。*

建议
- 允许用户预览每个语音的声音
- 为每个语音使用可信简介
- 提供入口点以了解有关合成语音的详细信息

### <a name="parental-disclosure"></a>家长泄露

除了遵守 COPPA 法规之外，如果您的主要目标受众是年轻人，并且您的暴露程度很高，则可以向家长提供公开。 对于敏感用途，请考虑在成人确认使用综合语音之前对体验进行了概述。 鼓励父级向其子级传达消息。

父](media/responsible-ai/disclosure-patterns/parental-disclosure.png) ![公开<br/>
*针对父项进行了优化的透明简介，可确保成人在与孩子交互之前，了解语音的综合性质。*

建议

- 作为主要用户进行公开的目标父级
- 鼓励家长向其孩子传达披露
- 提供入口点以了解有关合成语音的详细信息
- 通过向家长询问一个简单的 &quot;保护&quot; 问题，让他们了解他们是否阅读了公开

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>提供机会来了解有关如何进行语音的详细信息

为提供有关综合语音技术的详细信息的页面、弹出窗口或外部站点提供上下文相关的入口点。 例如，你可以在载入过程中或在用户在会话期间提示输入详细信息时，介绍一个链接以了解详细信息。

![入口点以了解详细](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*输入点的示例，提供详细了解合成语音的机会。*

用户请求有关综合语音的详细信息后，主要目标是向他们讲解综合语音的起源，并对该技术进行透明处理。

![向用户提供有关综合语音](media/responsible-ai/disclosure-patterns/learn-more.png) 的详细信息<br/>
*可在外部站点帮助站点中提供详细信息。*

建议

- 简化复杂概念，避免使用 legalese 和技术术语
- 请勿在隐私和使用条款声明中埋葬此内容
- 使内容简洁，并在可用时使用图像

## <a name="implicit-disclosure"></a>隐式公开

一致性是在整个用户旅程内隐式实现泄露的关键。 跨设备和交互模式一致地使用视觉对象和听觉提示有助于生成隐式模式和显式公开之间的关联。

![隐式提示的一致性](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues-amp-feedback"></a>&amp; 反馈的隐式提示

Anthropomorphism 可以采用不同的方式，从代理的实际视觉对象表示形式到声音、声音、光模式、弹跳形状甚至设备振动。 定义角色时，请利用隐式提示和反馈模式，而不是瞄准非常类似的头像。 这是最大程度地减少对更显式泄露的需求的一种方法。

![视觉提示和反馈](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*这些提示可帮助 anthropomorphize 代理，而无需太人赞。随着时间的推移，它们也可能会成为有效的公开机制。*

结合以下类型的提示时，请考虑体验的不同交互模式：

| 视觉提示                                                                                                                                                               | 听觉提示                                                      | Haptic 提示 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>响应实时提示（例如，动画）<br> 非屏幕提示（例如设备上的灯和图案）<br>  | Sonicon （例如，一种简短的独特声音，一系列音乐笔记） | 振动   |

### <a name="capability-disclosure"></a>功能公开

可以通过为数字助理的功能设置准确的期望值来隐式地获取泄露。 提供示例命令，以便用户可以了解如何与数字助理交互，并提供上下文帮助，以便在体验的早期阶段详细了解综合声音。

![视觉提示和反馈](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>对话透明度

当会话处于意外路径时，请考虑手工构建默认响应，以帮助重置预期、强化透明度，并使用户能够成功完成路径。 也有机会在会话中使用显式公开。

![处理意外路径](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
对于已定向到代理的 "非任务" 或 "&quot;个人&quot; 问题，需要提醒用户了解代理的综合性质，并使用户能够适当地进行沟通或将他们重定向到真实人员。

![处理任务问题](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>何时泄露

在整个用户旅程中，有很多机会要公开。 设计为第一次使用、第二次使用、第 n 次使用 ...，但同时会使用 &quot;故障&quot; 来突出显示透明度，如系统出错或用户发现代理&#39;功能的限制。

![整个用户旅程中的公开机会](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

标准数字助理用户旅程的示例突出显示了各种泄露机会。

### <a name="up-front"></a>前期

最佳的泄露时间是用户第一次与综合语音交互。  在个人语音助手场景中，这会在载入过程中或用户第一次取消装箱体验。 在其他情况下，这可能是首次在网站上或用户第一次与虚拟字符交互时，合成语音读取内容。

- [透明介绍](#transparent-introduction)
- [功能公开](#capability-disclosure)
- [自定义和校准](#customization-and-calibration)
- [隐式提示](#implicit-cues--feedback)

### <a name="upon-request"></a>根据请求

用户在用户旅程期间，用户应该能够轻松地访问附加信息、控制首选项，并在用户旅程期间接收透明通信。

- [提供机会来了解有关如何进行语音的详细信息](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [自定义和校准](#customization-and-calibration)
- [对话透明度](#conversational-transparency)

### <a name="continuously"></a>播放

使用隐式设计模式，以持续增强用户体验。

- [功能公开](#capability-disclosure)
- [隐式提示](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>当系统失败时

使用披露作为合理的故障发生机会。

- [对话透明度](#conversational-transparency)
- [提供机会来了解有关如何进行语音的详细信息](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [提交给人](#conversational-transparency)



## <a name="additional-resources"></a>其他资源
- [Microsoft 机器人指导原则](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana 设计准则](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Microsoft Windows UWP 语音设计指南](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Microsoft Windows Mixed Reality 语音命令准则](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>参考文档

* [透明度备注](https://aka.ms/neural-tts-transparency-note)
* [综合语音技术的责任部署指南](concepts-guidelines-responsible-deployment-synthetic.md)
* [概述概述](concepts-gating-overview.md)
* [如何公开](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>后续步骤

* [透明度备注](https://aka.ms/neural-tts-transparency-note)
