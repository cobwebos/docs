---
title: 阻止翻译内容 - 文本翻译 API
titlesuffix: Azure Cognitive Services
description: 使用文本翻译 API 阻止翻译内容。
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: 32e5ddecc336175443fc5e8743b19b480de71c6b
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515264"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>如何使用文本翻译 API 阻止翻译内容

通过文本翻译 API 可以标记内容，以便不对其进行翻译。 例如，你可能想要标记本地化后没有意义的代码、品牌名称或单词/短语。

## <a name="methods-for-preventing-translation"></a>阻止翻译的方法
1. 转义为 Twitter 标记 @somethingtopassthrough 或 #somethingtopassthrough。 翻译后取消转义。

2. 使用 `notranslate` 标记内容。

   示例：

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. 使用[动态词典](dynamic-dictionary.md)给出特定翻译。

4. 不要将字符串传递到文本翻译 API 进行翻译。

5. 自定义翻译器：使用[自定义翻译器中的词典](custom-translator/what-is-dictionary.md)以 100% 的概率给出短语翻译。


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [避免在 Translator API 调用中进行翻译](reference/v3-0-translate.md)
