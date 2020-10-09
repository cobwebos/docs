---
title: 公开设计模式
titleSuffix: Azure Cognitive Services
description: 披露的设计模式和最佳做法。
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: 3e7d8ee2b156a30b11cda79798a8af8a8ecf4f64
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "74776616"
---
# <a name="disclosure-design-patterns"></a>披露设计模式
你已经确定了合成语音体验的正确[披露级别](concepts-disclosure-guidelines.md#disclosure-assessment)，现在正是探索潜在设计模式的好时机。
## <a name="overview"></a>概述
下面是可应用于合成语音体验的披露设计模式范围。 如果披露评估结果为“高披露”，建议[显式披露](#explicit-disclosure)，即直接传达合成语音的来源。 [隐式披露](#implicit-disclosure)包括提示和交互模式，无论所需披露级别是高是低，这些提示和交互模式都会使语音体验受益。
![披露模式范围](media/responsible-ai/disclosure-patterns/affordances.png)






| 显式披露模式                                                                                                                                                                                    | 隐式披露模式                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[透明介绍](#transparent-introduction)<br> [言语透明介绍](#verbal-transparent-introduction)<br>  [显式署名](#explicit-byline)<br>  [自定义和校准](#customization-and-calibration)<br> [向家长披露](#parental-disclosure)<br> [提供详细了解语音发出方式的机会](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [功能披露](#capability-disclosure)<br>[隐式提示和反馈](#implicit-cues--feedback)<br> [对话透明度](#conversational-transparency) |



使用以下图表，直接参考适用于合成语音的模式。 此图表中的其他一些条件可能也适用于你的方案：<br/>



| 如果你的合成语音体验… | 建议 | 设计模式 |
| --- | --- | --- |
| 需要高披露  | 预先使用至少一种显式模式和隐式提示，以帮助用户建立关联。 |[显式披露](#explicit-disclosure)<br>[隐式披露](#implicit-disclosure)  |
| 需要低披露 | 披露可能很少或不必要，但可从某些隐式模式中受益。 | [功能披露](#capability-disclosure)<br>[对话透明度](#conversational-transparency)  |
| 具有高参与度 | 为长期使用而构建，提供多个入口点，以在用户旅程中进行披露。 强烈建议设计载入体验。 | [透明介绍](#transparent-introduction)<br>[自定义和校准](#customization-and-calibration)<br>[功能披露](#capability-disclosure) |
| 包含儿童作为主要目标受众 | 将家长作为主要披露受众，并确保他们能够有效地将披露传达给孩子。  | [向家长披露](#parental-disclosure)<br>[言语透明介绍](#verbal-transparent-introduction)<br> [隐式披露](#implicit-disclosure)<br> [对话透明度](#conversational-transparency)  |
| 包含盲人用户或低视力人群作为主要目标受众  | 包含所有用户，并确保任何形式的视觉披露都具有关联的替换文字或声音效果。 遵循对比度和显示大小的辅助功能标准。 使用听觉提示传达披露。  | [言语透明介绍](#verbal-transparent-introduction) <br>[听觉提示](#implicit-cues--feedback)<br>[触觉提示](#implicit-cues--feedback)<br>[对话透明度](#conversational-transparency)<br>[辅助功能标准](https://www.microsoft.com/accessibility) |
| 无屏幕、无设备，或使用语音作为主要或唯一交互模式 | 使用听觉提示传达披露。 | [言语透明介绍](#verbal-transparent-introduction) <br> [听觉提示](#implicit-cues--feedback)  |
| 可能包含多个用户/听众（例如多个家庭中的私人助理）  | 请注意各种用户上下文和理解级别，并在用户旅程中提供多个披露机会。  | [透明介绍（返回用户）](#transparent-introduction)<br> [提供详细了解语音发出方式的机会](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [对话透明度](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>显式披露
如果合成语音体验需要高披露，则最好使用以下显式模式中的至少一种来清楚地声明合成性质。
### <a name="transparent-introduction"></a>透明介绍

在开始语音体验之前，请介绍数字助理，完全透明地公开其语音来源和功能。 加入新用户或向返回用户介绍新功能时是使用此模式的最佳时机。 在介绍过程中实现隐式提示，可帮助用户形成有关数字代理合成性质的心理模型。

#### <a name="first-time-user-experience"></a>首次用户体验

![首次运行体验过程中的透明介绍](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
合成语音在加入新用户时引入。

建议
- 说明语音是人工合成的（例如“数字”）
- 说明代理能够执行的操作
- 显式说明语音的来源
- 提供可详细了解合成语音的入口点

#### <a name="returning-user-experience"></a>返回用户体验

如果用户跳过载入体验，继续提供透明介绍体验的入口点，直到用户首次触发语音。
<br/>

![返回用户体验过程中的透明介绍](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
提供合成语音体验的一致入口点。当用户在用户旅程中的任何时间点首次触发语音时，允许其返回载入体验。


### <a name="verbal-transparent-introduction"></a>言语透明介绍

通过语音提示陈述数字助理的语音来源，足以显式实现披露。 此模式最适用于高披露方案，在此类方案中，语音是唯一可用的交互模式。
<br/>

![口述透明介绍](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>在用户体验中的某些时刻需要介绍或说明某人的音源时，请使用透明介绍。


![第一人称口述透明介绍](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
为提高透明度，配音演员可以以第一人称披露合成语音的来源。

### <a name="explicit-byline"></a>显式署名

如果用户将与音频播放器或交互式组件进行交互以触发语音，请使用此模式。


![新闻媒体方案中的显式署名](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*显式署名是指说明语音来源*。

建议

- 提供详细了解合成语音的入口点

### <a name="customization-and-calibration"></a>自定义和校准

为用户提供对数字助理对其响应方式（即语音的听感）的控制。  如果用户以其自己的方式与系统进行交互，且在与系统交互时心中有特定目标，则根据定义，他们已经认识到系统不是真人。

#### <a name="user-control"></a>用户控件

提供对合成语音体验具有意义深刻且显著影响的选择。

![用户首选项](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
用户首选项允许用户自定义及改进其体验。

建议

- 允许用户自定义语音（如选择语言和语音类型）
- 向用户提供一种教授系统响应其独特语音的方法（如语音校准、自定义命令）
- 优化用户生成的交互或上下文交互（如提醒）

#### <a name="persona-customization"></a>角色自定义

提供自定义数字助理语音的方法。 如果语音基于公众人物或知名度较高的人，请考虑在用户预览语音时同时使用视觉和语音介绍。

![语音自定义](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
提供从一组语音中进行选择的功能，有助于表达人工合成这一性质。

建议
- 允许用户预览每个语音的声音
- 为每个语音使用真实的介绍
- 提供详细了解合成语音的入口点

### <a name="parental-disclosure"></a>向家长披露

除遵守 COPPA 法规外，如果你的主要目标受众是少年儿童，且公开水平较高，则请向家长提供披露。 对于敏感用途，请考虑在成人认可使用合成语音前管控这一体验。 鼓励家长向其孩子传达消息。

![向家长披露](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
针对家长进行了优化的透明介绍，确保在孩子与语音交互之前，成人已经了解该语音的合成性质。

建议

- 以家长为主要受众进行披露
- 鼓励家长向其孩子传达披露
- 提供详细了解合成语音的入口点
- 通过向家长询问一个简单的“保护”问题来证明他们已经阅读了披露内容，从而管控体验

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>提供详细了解语音发出方式的机会

提供上下文敏感入口点，指向可提供有关合成语音技术详细信息的页面、弹出项或外部站点。 例如，在载入过程中或在对话过程中用户表示需要更多信息时，可以显示链接供其了解详细信息。

![了解详细信息的入口点](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
提供详细了解合成语音机会的输入点的示例。

用户请求有关合成语音的详细信息后，主要目标是向他们讲解合成语音的来源，并透明地介绍该技术。

![向用户提供有关合成语音的详细信息](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
可在外部站点帮助站点中提供详细信息。

建议

- 简化复杂概念，避免使用法律术语和技术术语
- 请勿将此内容埋没在隐私声明和使用条款中
- 保持内容简洁，在可用时使用图像

## <a name="implicit-disclosure"></a>隐式披露

一致性是在整个用户旅程中隐式实现披露的关键。 跨设备和交互模式一致地使用视觉和听觉提示，有助于在隐式模式和显式披露之间建立关联。

![隐式提示的一致性](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>隐式提示和反馈

可通过不同的方式表达拟人形象，从代理的实际视觉表示形式到语音、声音、光模式、弹跳形状甚至设备振动。 定义角色时，请利用隐式提示和反馈模式，而不要试图打造很像真人的头像。 这是最大程度减少对更显式披露需求的一种方法。

![视觉提示和反馈](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
这些提示可帮助使代理拟人化而又不过于像真人。长期一致地使用，它们本身也可以成为有效的披露机制。

结合以下类型的提示时，请考虑体验的不同交互模式：

| 视觉提示                                                                                                                                                               | 听觉提示                                                      | 触觉提示 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>响应式实时提示（如动画）<br> 非屏幕提示（如设备上的光和图案）<br>  | Sonicon（如简短的独特声音、一串音符） | 振动   |

### <a name="capability-disclosure"></a>功能披露

可通过对数字助理能够执行的操作设置准确期望来隐式实现披露。 提供示例命令，以便用户可以了解如何与数字助理交互，并提供上下文帮助，以在体验的早期阶段了解有关合成语音的详细信息。

![视觉提示和反馈](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>对话透明度

当会话进入意外路径时，请考虑创建可帮助重置预期、增强透明度，以及将用户导向正确路径的默认响应。 也有机会在对话中使用显式披露。

![处理意外路径](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
指向代理的任务外或“私人”问题，是提醒用户代理的合成性质并引导用户适当与之交流或将用户重定向到真人的好时机。

![处理任务外问题](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>何时披露

在整个用户旅程中，有很多进行披露的机会。 为第一次使用、第二次使用、第 n 次使用而设计，同时抓住“失败”的时机（如系统出错或用户发现代理功能的限制时）来强调透明度。

![整个用户旅程中的披露机会](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

突出各种披露机会的标准数字助理用户旅程示例。

### <a name="up-front"></a>预先披露

最佳披露时刻是用户第一次与合成语音进行交互时。  在个人语音助理方案中，应该是载入过程中或用户首次实际开启体验时。 在其他方案中，可能是合成语音首次朗读网站上的内容时，或者用户首次与虚拟角色进行交互时。

- [透明介绍](#transparent-introduction)
- [功能披露](#capability-disclosure)
- [自定义和校准](#customization-and-calibration)
- [隐式提示](#implicit-cues--feedback)

### <a name="upon-request"></a>在请求时披露

用户应该能在发起请求后轻松访问额外信息、控制首选项，并可在用户旅程中的任何时候接收透明讯息。

- [提供详细了解语音发出方式的机会](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [自定义和校准](#customization-and-calibration)
- [对话透明度](#conversational-transparency)

### <a name="continuously"></a>持续披露

使用可持续增强用户体验的隐式设计模式。

- [功能披露](#capability-disclosure)
- [隐式提示](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>在系统故障时披露

使用披露作为从容应对故障的机会。

- [对话透明度](#conversational-transparency)
- [提供详细了解语音发出方式的机会](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [提交给人](#conversational-transparency)



## <a name="additional-resources"></a>其他资源
- [Microsoft 机器人指南](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana 设计指南](https://docs.microsoft.com/cortana/voice-commands/voicecommand-design-guidelines)
- [Microsoft Windows UWP 语音设计指南](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)
- [Microsoft Windows 混合现实语音命令指南](https://docs.microsoft.com/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="reference-docs"></a>参考文档

* [向配音员公开](https://aka.ms/disclosure-voice-talent)
* [合成语音技术的负责任的部署指南](concepts-guidelines-responsible-deployment-synthetic.md)
* [管控概述](concepts-gating-overview.md)
* [如何披露](concepts-disclosure-guidelines.md)

## <a name="next-steps"></a>后续步骤

* [向配音员公开](https://aka.ms/disclosure-voice-talent)
