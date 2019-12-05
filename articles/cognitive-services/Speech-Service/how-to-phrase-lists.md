---
title: 短语列表-语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用 `PhraseListGrammar` 对象为语音服务提供短语列表，以改进语音到文本识别的结果。
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 052e02ef562da0637b6b5b9683120f0c397dbfd5
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805869"
---
# <a name="phrase-lists-for-speech-to-text"></a>用于语音到文本的短语列表

通过向语音服务提供短语列表，你可以提高语音识别的准确性。 短语列表用于标识音频数据中的已知短语，如人员姓名或特定位置。

例如，如果你有一个 "移动到" 命令和可能会说 "Ward" 的可能目标，则可以添加 "移动到 Ward" 的条目。 添加短语会增加识别音频时，将识别出 "移动到 Ward" 而不是 "移到" 的概率。

可以将一个词或完整短语添加到短语列表。 在识别期间，如果音频中包含完全匹配项，则使用短语列表中的条目。 基于前面的示例，如果短语列表包含 "移动到 Ward"，并且音频捕获的声音与 "移到" 和 "移动到 Ward" 一样，则识别结果更有可能被识别为 "移动到 Ward 慢"。

>[!Note]
> 目前，短语列表仅支持用于语音到文本的英语。

## <a name="how-to-use-phrase-lists"></a>如何使用短语列表

下面的示例演示如何使用 `PhraseListGrammar` 对象生成短语列表。

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

还可以通过调用 clear （）来清除与 `PhraseListGrammar` 关联的短语。

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
> 对 `PhraseListGrammar` 对象所做的更改将在下一次识别或重新连接到语音服务后生效。

## <a name="next-steps"></a>后续步骤

* [语音 SDK 参考文档](speech-sdk.md)
