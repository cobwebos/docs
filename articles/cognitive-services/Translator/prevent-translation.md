---
title: 禁止内容翻译-转换器
titleSuffix: Azure Cognitive Services
description: 通过转换器防止内容翻译。 转换器允许对内容进行标记，使其不会被翻译。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 37d2e4c0131569ab50ebf49ff73b6adf7a420713
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996170"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>如何防止翻译内容翻译

转换器允许对内容进行标记，使其不会被翻译。 例如，你可能想要标记本地化后没有意义的代码、品牌名称或单词/短语。

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

3. 不要将该字符串传递给翻译人员的翻译。

4. 自定义转换器：[在自定义转换器中使用字典](custom-translator/what-is-dictionary.md)，以规定短语的翻译的概率为100%。


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [避免翻译人员拜访](reference/v3-0-translate.md)
