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
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80052489"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>如何使用文本翻译 API 阻止翻译内容

通过文本翻译 API 可以标记内容，以便不对其进行翻译。 例如，你可能想要标记本地化后没有意义的代码、品牌名称或单词/短语。

## <a name="methods-for-preventing-translation"></a>阻止翻译的方法

1. 使用 `notranslate` 标记内容。 根据设计，仅当输入 textType 设置为 HTML 时，这才起作用

   示例：

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. 使用[动态词典](dynamic-dictionary.md)给出特定翻译。

3. 不要将字符串传递到文本翻译 API 进行翻译。

4. 自定义转换器：[在自定义转换器中使用字典](custom-translator/what-is-dictionary.md)，以规定短语的翻译的概率为100%。


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [避免在 Translator API 调用中进行翻译](reference/v3-0-translate.md)
