---
title: 阻止翻译内容 - 文本翻译 API
titleSuffix: Azure Cognitive Services
description: 使用文本翻译 API 阻止翻译内容。 通过文本翻译 API 可以标记内容，以便不对其进行翻译。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: swmachan
ms.openlocfilehash: dd3684cbd7c03851bfcc75293a9690f77b4652b2
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184818"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>如何使用文本翻译 API 阻止翻译内容

通过文本翻译 API 可以标记内容，以便不对其进行翻译。 例如，你可能想要标记本地化后没有意义的代码、品牌名称或单词/短语。

## <a name="methods-for-preventing-translation"></a>阻止翻译的方法
1. 转义为 Twitter 标记 @somethingtopassthrough 或 #somethingtopassthrough。 翻译后取消转义。 这是有效 twitter 标记的正则表达式： `\B@[A-Za-z]+[A-Za-z0-9_]+)`。 标记应以 "@" 符号开头，后跟一个字符，然后后跟一个或多个字符、数字或下划线。 建议保留标记简短，并且开始标记前面必须有一个空格。

2. 使用 `notranslate` 标记内容。

   示例：

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. 使用[动态词典](dynamic-dictionary.md)给出特定翻译。

4. 不要将字符串传递到文本翻译 API 进行翻译。

5. 自定义转换器：[在自定义转换器中使用字典](custom-translator/what-is-dictionary.md)，以规定短语的翻译的概率为100%。


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [避免在 Translator API 调用中进行翻译](reference/v3-0-translate.md)
