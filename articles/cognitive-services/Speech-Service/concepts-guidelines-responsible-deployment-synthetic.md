---
title: 合成语音技术的负责任部署指南
titleSuffix: Azure Cognitive Services
description: Microsoft 提供的使用合成语音技术时的一般设计准则。 Microsoft 在配音员、消费者以及有言语障碍的个人的协助下进行了研究，在研究中制定了这些准则，用以指导合成语音的负责任部署。
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "73836767"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>合成语音技术的负责任部署指南
下面是 Microsoft 提供的使用合成语音技术时的一般设计准则。 Microsoft 在配音员、消费者以及有言语障碍的个人的协助下进行了研究，在研究中制定了这些准则，用以指导合成语音的负责任部署。

## <a name="general-considerations"></a>一般注意事项
对于合成语音技术的部署，以下准则适用于大多数方案。

### <a name="disclose-when-the-voice-is-synthetic"></a>当语音为合成语音时进行披露
公开某种语音是由计算机生成的，不仅可以最大限度地降低欺骗带来的有害后果风险，而且还能增加对提供声音的组织的信任。 详细了解[如何进行披露](concepts-disclosure-guidelines.md)。

### <a name="select-appropriate-voice-types-for-your-scenario"></a>为你的方案选择合适的语音类型
仔细考虑与使用合成语音相关的使用上下文和潜在危害。 例如，高保真合成语音可能不适用于高风险方案，如个人消息传递、金融交易或需要人类的适应性或同理心的复杂情况。 用户对语音类型也可能有不同的期望。 例如，在收听由合成语音播报的敏感新闻时，一些用户喜欢更具同情心和更人性化的新闻播报，而另一些用户则喜欢更单调、无偏见的声音。 请考虑测试你的应用程序，以便更好地了解用户偏好。

### <a name="be-transparent-about-capabilities-and-limitations"></a>对功能和限制保持透明
与高保真合成语音代理交互时，用户更有可能具有更高的期望。 因此，当系统功能不能满足这些预期时，信任可能会受到影响，并可能导致令人不愉快的体验甚至有害的体验。

### <a name="provide-optional-human-support"></a>提供可选的人工支持
在模棱两可的事务性场景中（例如，呼叫支持中心），用户并不总是相信计算机代理能够适当地响应他们的请求。 在这些情况下，无论语音的逼真度或系统的功能如何，都可能需要人工支持。

## <a name="considerations-for-voice-talent"></a>针对配音员的注意事项
与配音员（例如声优）合作创建合成语音时，以下准则适用。

### <a name="obtain-meaningful-consent-from-voice-talent"></a>获得配音员的实质性同意
配音员希望能够控制他们的语音字体（如何使用以及在哪里使用），并在其语音被使用时获得补偿。 因此，系统所有者应获得配音员的明确书面许可，并在用例、使用期限、补偿等合同细节方面明确化。 一些配音员不知道该技术的潜在恶意使用，因此系统所有者应向他们介绍该技术的功能。 有关配音员和许可的详细信息，请阅读[针对配音员的披露内容](https://aka.ms/disclosure-voice-talent)。


## <a name="considerations-for-those-with-speech-disorders"></a>针对言语障碍患者的注意事项
与有言语障碍的人合作创建或部署合成语音技术时，以下准则适用。

### <a name="provide-guidelines-to-establish-contracts"></a>提供签订合同的准则
提供与使用合成语音协助说话的个人签订合同的准则。 合同应考虑规定语音的所有者、使用期限、所有权转移条件、删除语音字体的规程以及如何防止未经授权的访问。 另外，在获得当事人同意的情况下，可以在该人死后将语音字体所有权以合同形式转让给其家属。

### <a name="account-for-inconsistencies-in-speech-patterns"></a>将语音模式的不一致性考虑在内
对于那些录制自己的语音字体的言语障碍患者来说，他们的语音模式的不一致性（含糊不清或无法发特定单词的音）可能会使录制过程复杂化。 在这些情况下，合成语音技术和录制会话应当适应他们的节奏（也就是说，应提供中断和额外数量的录制会话）。

### <a name="allow-modification-over-time"></a>允许在一段时间内修改
具有言语障碍的人希望对其合成语音进行更新以反映年龄的增长（例如，儿童进入青春期）。 这些人还可能有随时间而改变的风格偏好，可能想要改变音调、口音或其他语音特征。


## <a name="reference-docs"></a>参考文档

* [针对配音员的披露内容](https://aka.ms/disclosure-voice-talent)
* [管控概述](concepts-gating-overview.md)
* [如何披露](concepts-disclosure-guidelines.md)
* [披露设计模式](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>后续步骤

* [针对配音员的披露内容](https://aka.ms/disclosure-voice-talent)
* [如何披露](concepts-disclosure-guidelines.md)
* [披露设计模式](concepts-disclosure-patterns.md)
