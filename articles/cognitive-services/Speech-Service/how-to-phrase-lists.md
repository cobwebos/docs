---
title: 短语列表-语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用 `PhraseListGrammar` 对象为语音服务提供短语列表，以改进语音到文本识别的结果。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f84ea6b2b0f1e8246e1d765e54f663cd01f29b1d
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560841"
---
# <a name="phrase-lists-for-speech-to-text"></a>用于语音到文本的短语列表

通过向语音服务提供短语列表，你可以提高语音识别的准确性。 短语列表用于标识音频数据中的已知短语，如人员姓名或特定位置。

例如，如果你有一个 "移动到" 命令和可能会说 "Ward" 的可能目标，则可以添加 "移动到 Ward" 的条目。 添加短语会增加识别音频时，将识别出 "移动到 Ward" 而不是 "移到" 的概率。

可以将一个词或完整短语添加到短语列表。 在识别期间，如果整个短语的完全匹配项作为单独的短语包含在音频中，则将使用短语列表中的条目。 如果找不到与短语完全匹配的项，则识别将无法使用。

>[!Note]
> 目前，短语列表仅支持用于语音到文本的英语。

## <a name="how-to-use-phrase-lists"></a>如何使用短语列表

下面的示例演示如何使用 `PhraseListGrammar` 对象生成短语列表。

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> 语音服务将用于匹配语音的短语列表的最大数目为1024个短语。

还可以通过调用 clear （）来清除与 `PhraseListGrammar` 关联的短语。

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> 对 `PhraseListGrammar` 对象所做的更改将在下一次识别或重新连接到语音服务后生效。

## <a name="next-steps"></a>后续步骤

* [语音 SDK 参考文档](speech-sdk.md)
