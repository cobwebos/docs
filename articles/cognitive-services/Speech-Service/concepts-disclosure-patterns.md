---
title: 披露设计模式
titleSuffix: Azure Cognitive Services
description: 公开设计模式和最佳实践。
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776616"
---
# <a name="disclosure-design-patterns"></a>披露设计模式
现在，您&#39;为您的合成语音体验确定了正确的[披露级别](concepts-disclosure-guidelines.md#disclosure-assessment)，&#39;探索潜在设计模式的好时机。
## <a name="overview"></a>概述
有一系列披露设计模式，您可以应用于您的合成语音体验。 如果您的披露评估结果是"高度披露"，我们建议[**明确披露**](#explicit-disclosure)，这意味着直接传达合成声音的起源。 [**隐式披露**](#implicit-disclosure)包括提示和交互模式，无论所需的披露级别是高还是低，都有利于语音体验。
![披露模式的范围](media/responsible-ai/disclosure-patterns/affordances.png)






| 显式披露模式                                                                                                                                                                                    | 隐式披露模式                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[透明介绍](#transparent-introduction)<br> [口头透明介绍](#verbal-transparent-introduction)<br>  [显式分行](#explicit-byline)<br>  [定制和校准](#customization-and-calibration)<br> [家长披露](#parental-disclosure)<br> [提供更多了解语音制作方式的机会](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [能力披露](#capability-disclosure)<br>[隐式提示和反馈](#implicit-cues--feedback)<br> [对话透明度](#conversational-transparency) |



使用下图直接引用适用于合成语音的模式。 此图表中的其他一些条件也可能适用于您的方案：<br/>



| 如果你的合成语音体验... | 建议 | 设计模式 |
| --- | --- | --- |
| 要求高度披露  | 在前面至少使用一个显式模式和隐式提示，以帮助用户构建关联。 |[明确披露](#explicit-disclosure)<br>[隐式披露](#implicit-disclosure)  |
| 要求低披露 | 披露可能很少或没有必要，但可能会受益于一些隐含的模式。 | [能力披露](#capability-disclosure)<br>[对话透明度](#conversational-transparency)  |
| 具有高度的参与性 | 长期构建，并提供多个入口点，以便用户旅程中披露。 强烈建议您有入职体验。 | [透明介绍](#transparent-introduction)<br>[定制和校准](#customization-and-calibration)<br>[能力披露](#capability-disclosure) |
| 将儿童作为主要目标受众 | 将家长作为主要披露受众，并确保他们能够有效地向儿童传达信息公开。  | [家长披露](#parental-disclosure)<br>[口头透明介绍](#verbal-transparent-introduction)<br> [隐式披露](#implicit-disclosure)<br> [对话透明度](#conversational-transparency)  |
| 包括盲人用户或视力低下的人作为主要目标受众  | 包容所有用户，并确保任何形式的视觉披露具有相关的替代文本或声音效果。 遵守对比度和显示尺寸的辅助功能标准。 使用听觉提示传达披露信息。  | [口头透明介绍](#verbal-transparent-introduction) <br>[听觉提示](#implicit-cues--feedback)<br>[触觉提示](#implicit-cues--feedback)<br>[对话透明度](#conversational-transparency)<br>[辅助功能标准](https://www.microsoft.com/accessibility) |
| 是无屏幕、无设备还是使用语音作为主要或唯一的交互模式 | 使用听觉提示传达披露信息。 | [口头透明介绍](#verbal-transparent-introduction) <br> [听觉提示](#implicit-cues--feedback)  |
| 可能包括多个用户/倾听者（例如，多个家庭的个人助理）  | 注意各种用户背景和理解水平，并提供多种机会在用户旅程中披露。  | [透明介绍（返回用户）](#transparent-introduction)<br> [提供更多了解语音制作方式的机会](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [对话透明度](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>明确披露
如果您的合成语音体验需要高度公开，最好使用以下至少一种显式模式来清楚地说明合成特性。
### <a name="transparent-introduction"></a>透明介绍

在语音体验开始之前，通过完全透明地介绍数字助手的语音来源及其功能。 使用此模式的最佳时刻是加入新用户或向返回的用户引入新功能时。 在介绍期间实现隐式提示可帮助用户形成有关数字代理合成性质的心理模型。

#### <a name="first-time-user-experience"></a>首次用户体验

![首次运行体验期间的透明介绍](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*在输入新用户时引入了合成语音。*

建议
- 描述声音是人造的（例如&quot;数字）&quot;
- 描述代理能够执行的操作
- 显式说明语音&#39;其来源
- 提供一个入口点，了解有关合成语音的更多

#### <a name="returning-user-experience"></a>返回用户体验

如果用户跳过载入体验，请继续提供透明介绍体验的入口点，直到用户首次触发语音。
<br/>

![返回用户体验期间的透明介绍](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*为合成语音体验提供一致的切入点。允许用户在用户旅程中的任何时刻首次触发语音时返回到载入体验。*


### <a name="verbal-transparent-introduction"></a>口头透明介绍

说明数字助理&#39;声音起源的语音语音的语音语音本身非常明确，足以实现披露。 此模式最适合在语音是唯一可用的交互模式的高披露方案中。
<br/>

![口头发言的透明介绍](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*在用户体验中可能已介绍或属性某人&#39;语音时，请使用透明介绍。*


![第一人称口头透明介绍](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*为了增加透明度，语音执行者可以在第一人称中披露合成语音的来源。*

### <a name="explicit-byline"></a>显式分行

如果用户将与音频播放器或交互式组件交互以触发语音，则使用此模式。


![新闻媒体场景中的显式分线](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*一个明确的旁线是声音来自何处的归属。*

建议

- 提供入口点，了解有关合成语音的更多信息

### <a name="customization-and-calibration"></a>定制和校准

为用户提供数字助理如何响应他们（即语音声音）。  当用户按照自己的术语和特定目标与系统交互时，根据定义，他们已经明白&#39;它不是真人。

#### <a name="user-control"></a>用户控件

提供对合成语音体验有有意义和显著影响的选择。

![用户首选项](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*用户首选项允许用户自定义和改进其体验。*

建议

- 允许用户自定义语音（例如，选择语言和语音类型）
- 为用户提供一种教系统响应他/她独特声音（例如语音校准、自定义命令）的方法
- 优化用户生成的或上下文交互（例如提醒）

#### <a name="persona-customization"></a>角色自定义

提供自定义数字助理&#39;语音的方法。 如果语音基于名人或广为人知的人，请考虑在用户预览语音时同时使用视觉和语音介绍。

![语音自定义](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*提供从一组声音中选择的能力有助于传达人工性质。*

建议
- 允许用户预览每个声音的声音
- 为每个语音使用真实的介绍
- 提供入口点，了解有关合成语音的更多信息

### <a name="parental-disclosure"></a>家长披露

除了遵守 COPPA 法规外，如果您的主要受众是幼儿，且您的曝光率较高，请向家长提供披露信息。 对于敏感用途，请考虑在成人确认使用合成语音之前对体验进行门控。 鼓励家长向孩子传达信息。

![家长披露](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*针对父母的透明介绍可确保成人在孩子与声音互动之前了解声音的合成性。*

建议

- 将父母作为披露的主要受众
- 鼓励家长向子女传达信息
- 提供入口点，了解有关合成语音的更多信息
- 通过向家长提出一个简单的&quot;保障&quot;问题来证明他们已经阅读了披露，从而将体验蒙上大门

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>提供更多了解语音制作方式的机会

向页面、弹出窗口或外部网站提供上下文相关的入口点，这些网站提供有关合成语音技术的详细信息。 例如，您可以在载入过程中或当用户在对话过程中提示详细信息时显示一个链接以了解更多信息。

![入门点以了解更多信息](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*入口点的示例，以提供了解有关合成语音的更多信息的机会。*

一旦用户要求有关合成语音的更多信息，主要目标是教育他们有关合成语音的起源，并透明地了解该技术。

![为用户提供有关合成语音的更多信息](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*可以在外部站点帮助站点中提供更多信息。*

建议

- 简化复杂概念，避免使用法律术语和技术术语
- 不要将此内容埋在隐私和使用条款声明中
- 保持内容简洁，并在可用时使用影像

## <a name="implicit-disclosure"></a>隐式披露

一致性是在整个用户旅程中隐式实现披露的关键。 跨设备和交互模式一致地使用视觉和听觉提示有助于在隐式模式和显式披露之间建立关联。

![隐式提示的一致性](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>隐含提示&反馈

拟人化可以以不同的方式表现出来，从代理的实际视觉表现，到声音、声音、光模式、弹跳形状，甚至设备的振动。 定义角色时，利用隐含的提示和反馈模式，而不是瞄准一个非常人性化的化身。 这是尽量减少更明确披露需求的一种方式。

![视觉提示和反馈](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*这些线索有助于将代理拟化，而不会过于像人一样。随着时间的推移，当使用一致时，它们也可以自行成为有效的披露机制。*

在合并以下类型提示时，请考虑体验的不同交互模式：

| 视觉提示                                                                                                                                                               | 听觉提示                                                      | 触觉提示 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>响应式实时提示（例如动画）<br> 非屏幕提示（例如，设备上的灯光和图案）<br>  | 声波（例如，一个简短的独特声音，一系列音符） | 振动   |

### <a name="capability-disclosure"></a>能力披露

通过对数字助理的能力设定准确的期望，可以隐式地实现披露。 提供示例命令，以便用户可以了解如何与数字助理交互，并提供上下文帮助，以便在体验的早期阶段了解有关合成语音的更多内容。

![视觉提示和反馈](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>对话透明度

当对话处于意外路径时，请考虑制定默认响应，以帮助重置期望值、增强透明度并引导用户走向成功路径。 在谈话中也有机会使用显式披露。

![处理意外路径](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
向座席提出的&quot;非&quot;任务或个人问题是提醒用户代理的合成性并引导他们适当地参与或将他们重定向到真实人物的好时机。

![处理任务问题](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>何时披露

在整个用户旅程中，有许多机会可供披露。 首次使用、第二次使用、第 n 次使用的设计...，但也包含&quot;未能&quot;突出显示透明度的时刻，例如当系统出错或用户发现代理&#39;功能的限制时。

![在整个用户旅程中披露机会](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

标准数字助理用户旅程示例，突出各种披露机会。

### <a name="up-front"></a>前期

披露的最佳时机是一个人第一次与合成的声音互动。在个人语音助理方案中，这将是在载入期间，或用户第一次几乎取消包装体验。 在其他方案中，可能是合成语音第一次读取网站上的内容，或者用户第一次与虚拟角色交互。

- [透明介绍](#transparent-introduction)
- [能力披露](#capability-disclosure)
- [定制和校准](#customization-and-calibration)
- [隐式提示](#implicit-cues--feedback)

### <a name="upon-request"></a>应要求

用户应该能够在需要时轻松访问其他信息、控制首选项和在用户旅程中的任何时间接收透明通信。

- [提供更多了解语音制作方式的机会](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [定制和校准](#customization-and-calibration)
- [对话透明度](#conversational-transparency)

### <a name="continuously"></a>不断

使用隐式设计模式，持续增强用户体验。

- [能力披露](#capability-disclosure)
- [隐式提示](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>当系统出现故障时

以披露为契机，优雅地失败。

- [对话透明度](#conversational-transparency)
- [提供更多了解语音制作方式的机会](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [提交给人](#conversational-transparency)



## <a name="additional-resources"></a>其他资源
- [微软机器人指南](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana 设计指南](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [微软视窗UWP语音设计指南](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [微软视窗混合现实语音指挥指南](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>参考文档

* [语音人才披露](https://aka.ms/disclosure-voice-talent)
* [负责部署合成语音技术的指导方针](concepts-guidelines-responsible-deployment-synthetic.md)
* [门控概述](concepts-gating-overview.md)
* [如何披露](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>后续步骤

* [语音人才披露](https://aka.ms/disclosure-voice-talent)
