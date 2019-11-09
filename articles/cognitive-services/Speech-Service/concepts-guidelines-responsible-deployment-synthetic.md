---
title: 综合语音技术的责任部署指南
titleSuffix: Azure Cognitive Services
description: Microsoft 使用综合语音技术的一般设计准则。 这些都是在 Microsoft 使用语音、消费者和语音可能进行的调查中开发的，以指导综合语音的责任开发。
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836767"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>综合语音技术的责任部署指南
下面是 Microsoft 关于使用综合语音技术的一般设计准则。 这些都是在 Microsoft 使用语音人才、消费者以及使用语音可能的个人进行研究中开发的，以指导综合语音的责任开发。

## <a name="general-considerations"></a>一般注意事项
对于综合语音技术的部署，以下指南适用于大多数方案。

### <a name="disclose-when-the-voice-is-synthetic"></a>当语音为合成时公开
公开一种语音是计算机生成的，不仅最大限度地降低了欺骗的有害后果，而且还增加了组织提供语音的信任。 详细了解[如何公开](concepts-disclosure-guidelines.md)。

### <a name="select-appropriate-voice-types-for-your-scenario"></a>为方案选择适当的语音类型
仔细考虑使用的上下文和与使用综合语音相关的潜在损害。 例如，高保真合成语音可能不适用于高风险方案，例如个人消息传递、金融交易或需要人工适应性或理解的复杂情况。 用户还可以对语音类型产生不同的期望。 例如，当倾听由综合语音读取的敏感新闻时，某些用户喜欢更 empathetic 和人类喜欢的新闻阅读，而另一些用户首选一种更 monotone 的无偏置声音。 请考虑测试应用程序，以便更好地了解用户首选项。

### <a name="be-transparent-about-capabilities-and-limitations"></a>对功能和限制透明
与高保真合成语音代理交互时，用户更有可能获得更高的期望。 因此，当系统功能不能满足这些要求时，信任可能会受到影响，并可能导致令人不愉快的体验甚至有害的体验。

### <a name="provide-optional-human-support"></a>提供可选的人工支持
在不明确的事务方案中（例如，调用支持中心），用户不总是信任计算机代理来适当地响应其请求。 无论系统的声音或功能的实际质量如何，都可以在这些情况下进行人工支持。

## <a name="considerations-for-voice-talent"></a>语音人才注意事项
使用语音人才（例如语音演员）创建综合声音时，以下准则适用。

### <a name="obtain-meaningful-consent-from-voice-talent"></a>从语音获得有意义的许可
语音人才希望能够控制其语音字体（使用方式和位置），并在使用时进行补偿。 因此，系统所有者应该从语音获得明确的书面许可，并清楚地说明用例、使用持续时间、补偿等。 某些语音人才不知道这种技术的潜在恶意使用，应由系统所有者来了解技术的功能。 有关语音人才和许可的详细信息，请阅读我们[的语音信息公开](https://aka.ms/disclosure-voice-talent)。


## <a name="considerations-for-those-with-speech-disorders"></a>带有 speech 可能的注意事项
使用 speech 可能的个人时，若要创建或部署综合语音技术，请遵循以下准则。

### <a name="provide-guidelines-to-establish-contracts"></a>提供建立协定的准则
提供与使用综合语音获得帮助的个人建立合同的相关准则。 协定应考虑指定拥有语音、使用持续时间、所有权转让标准、删除语音字体的过程，以及如何防止未经授权的访问。 此外，如果用户已获得权限，则允许在死亡后将语音字体所有权转让给家庭成员。

### <a name="account-for-inconsistencies-in-speech-patterns"></a>在语音模式中不一致的帐户
对于录制自己语音字体的语音可能的个人，语音模式中的不一致（slurring 或无法发音某些词）可能会使录制过程复杂化。 在这些情况下，综合语音技术和记录会话应适应它们（也就是说，提供中断和额外数量的录制会话）。

### <a name="allow-modification-over-time"></a>允许在一段时间内修改
使用 speech 可能的个人希望对其综合语音进行更新，以反映老化（例如，儿童到达 puberty）。 个人还可能具有随时间变化的样式首选项，并且可能需要对螺距、强调或其他语音特征进行更改。


## <a name="reference-docs"></a>参考文档

* [为语音人才公开](https://aka.ms/disclosure-voice-talent)
* [概述概述](concepts-gating-overview.md)
* [如何公开](concepts-disclosure-guidelines.md)
* [泄露设计模式](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>后续步骤

* [为语音人才公开](https://aka.ms/disclosure-voice-talent)
* [如何公开](concepts-disclosure-guidelines.md)
* [泄露设计模式](concepts-disclosure-patterns.md)
