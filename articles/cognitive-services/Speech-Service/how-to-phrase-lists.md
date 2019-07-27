---
title: 短语列表-语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用`PhraseListGrammar`对象向语音服务提供短语列表, 以改进语音到文本识别的结果。
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 0e552d502184d1b537263c2c1f6b2a8562cdf791
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562776"
---
# <a name="phrase-lists-for-speech-to-text"></a>用于语音到文本的短语列表

通过向语音服务提供短语列表, 你可以提高语音识别的准确性。 短语列表用于标识音频数据中的已知短语, 如人员姓名或特定位置。

例如, 如果你有一个 "移动到" 命令和可能会说 "Ward" 的可能目标, 则可以添加 "移动到 Ward" 的条目。 添加短语会增加识别音频时, 将识别出 "移动到 Ward" 而不是 "移到" 的概率。

可以将一个词或完整短语添加到短语列表。 在识别期间, 如果音频中包含完全匹配项, 则使用短语列表中的条目。 基于前面的示例, 如果 "短语" 列表包含 "移动到 Ward", 而捕获的短语 "移到缓慢", 则识别结果将为 "移动到 Ward 缓慢"。

>[!Note]
> 目前, 短语列表仅支持用于语音到文本的英语。

## <a name="how-to-use-phrase-lists"></a>如何使用短语列表

下面的示例演示了如何使用`PhraseListGrammar`对象构建词组列表。

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> 语音服务将用于匹配语音的短语列表的最大数目为1024个短语。

还可以`PhraseListGrammar`通过调用 clear () 来清除与相关联的短语。

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> `PhraseListGrammar`对对象所做的更改将在下一次识别或重新连接到语音服务时生效。

## <a name="next-steps"></a>后续步骤

* [语音 SDK 参考文档](speech-sdk.md)
