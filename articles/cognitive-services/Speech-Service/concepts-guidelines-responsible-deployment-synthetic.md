---
title: 负责部署合成语音技术的指导方针
titleSuffix: Azure Cognitive Services
description: 微软使用合成语音技术的通用设计指南。 这些是微软对语音人才、消费者以及有语音障碍的个人进行的研究开发的，以指导合成语音的负责任开发。
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836767"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>负责部署合成语音技术的指导方针
以下是微软使用合成语音技术的一般设计指南。 这些是微软对语音人才、消费者以及有语音障碍的个人进行的研究开发的，以指导合成语音的负责任发展。

## <a name="general-considerations"></a>一般注意事项
对于合成语音技术的部署，以下准则适用于大多数方案。

### <a name="disclose-when-the-voice-is-synthetic"></a>语音合成时披露
披露语音是计算机生成的，不仅可以将欺骗导致有害结果的风险降至最低，而且还会增加对提供语音的组织的信任。 详细了解[如何披露](concepts-disclosure-guidelines.md)。

### <a name="select-appropriate-voice-types-for-your-scenario"></a>为您的方案选择适当的语音类型
仔细考虑使用环境以及与使用合成语音相关的潜在危害。 例如，高保真合成声音在高风险情况下可能并不合适，例如个人消息传送、金融交易或需要人类适应性或同理心的复杂情况。 用户也可能对语音类型有不同的期望。 例如，当听一个合成的声音阅读敏感新闻时，一些用户更喜欢更同情和人性化的新闻阅读，而另一些用户则更喜欢单调、公正的声音。 请考虑测试应用程序以更好地了解用户首选项。

### <a name="be-transparent-about-capabilities-and-limitations"></a>对功能和限制保持透明
当与高保真合成语音代理交互时，用户更有可能有更高的期望。 因此，当系统功能无法满足这些期望时，信任可能会受到影响，并可能导致不愉快甚至有害的体验。

### <a name="provide-optional-human-support"></a>提供可选的人工支持
在不明确的事务性方案中（例如，呼叫支持中心），用户并不总是信任计算机代理来适当响应其请求。 在这些情况下，无论系统的声音或能力的真实质量如何，可能都有必要提供人工支持。

## <a name="considerations-for-voice-talent"></a>语音人才的注意事项
当与语音天才（如语音演员）合作创建合成语音时，以下准则适用。

### <a name="obtain-meaningful-consent-from-voice-talent"></a>获得语音人才的有意义的同意
语音人才希望控制其语音字体（使用方式和位置），并在使用时获得补偿。 因此，系统所有者应获得语音人才的明确书面许可，并在使用案例、使用期限、补偿等方面有明确的合同规范。 一些语音人才不知道该技术的潜在恶意使用，系统所有者应该对技术的能力进行教育。 有关语音天赋和同意的更多内容，请阅读我们的[语音人才披露](https://aka.ms/disclosure-voice-talent)。


## <a name="considerations-for-those-with-speech-disorders"></a>言语障碍患者的注意事项
当与有言语障碍的个人合作时，创建或部署合成语音技术时，适用以下准则。

### <a name="provide-guidelines-to-establish-contracts"></a>提供建立合同的指导原则
提供与使用合成语音协助说话的个人签订合同的准则。 合同应考虑指定拥有语音的各方、使用期限、所有权转移标准、删除语音字体的程序以及如何防止未经授权的访问。 此外，如果家庭成员已给予许可，则允许在死后将语音字体所有权的合同转让给家庭成员。

### <a name="account-for-inconsistencies-in-speech-patterns"></a>考虑语音模式的不一致
对于有语音障碍的人，他们记录自己的语音字体，他们的语音模式的不一致（含糊不清或无法发音某些单词）可能会使录制过程复杂化。 在这些情况下，合成语音技术和录制会话应适应它们（即提供中断和额外数量的录制会话）。

### <a name="allow-modification-over-time"></a>允许随时间而修改
有言语障碍的人希望更新他们的合成声音，以反映衰老（例如，孩子进入青春期）。 个人也可能具有随时间变化的样式偏好，并且可能希望更改音调、口音或其他语音特征。


## <a name="reference-docs"></a>参考文档

* [语音人才披露](https://aka.ms/disclosure-voice-talent)
* [门控概述](concepts-gating-overview.md)
* [如何披露](concepts-disclosure-guidelines.md)
* [披露设计模式](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>后续步骤

* [语音人才披露](https://aka.ms/disclosure-voice-talent)
* [如何披露](concepts-disclosure-guidelines.md)
* [披露设计模式](concepts-disclosure-patterns.md)
