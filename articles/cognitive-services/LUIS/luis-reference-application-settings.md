---
title: 应用程序设置
titleSuffix: Azure Cognitive Services
description: 了解语言理解应用程序的应用程序设置。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 3682b9e0c38344be1522440290b46f8c10bd5607
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275908"
---
# <a name="application-settings"></a>应用程序设置

这些应用程序设置存储在[导出](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)应用程序和[更新](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)使用 REST Api。 更改应用程序版本设置可将您的应用程序的培训状态重置为未训练。

|设置|默认值|说明|
|--|--|--|
|NormalizePunctuation|True|删除标点。|
|NormalizeDiacritics|True|删除音调符号。|

## <a name="diacritics-normalization"></a>标注字符规范化 

打开到 LUIS JSON 应用文件中的标注字符的查询文本规范化`settings`参数。

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

以下查询文本显示音调符号规范化将如何影响查询文本：

|具有标注字符设置为 false|标注字符设置为 true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>标注字符的语言支持

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>葡萄牙语 （巴西)`pt-br`音调符号

|标注字符设置为 false|标注字符设置为 true|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`| 
|||

#### <a name="dutch-nl-nl-diacritics"></a>荷兰语`nl-nl`音调符号

|标注字符设置为 false|标注字符设置为 true|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`| 
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>法语`fr-`音调符号

这包括法语和加拿大子区域性。

|标注字符设置为 false|标注字符设置为 true|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`| 
|`ê`|`e`| 
|`î`|`i`| 
|`ô`|`o`| 
|`û`|`u`| 
|`ç`|`c`| 
|`ë`|`e`| 
|`ï`|`i`| 
|`ü`|`u`| 
|`ÿ`|`y`| 

#### <a name="german-de-de-diacritics"></a>德语`de-de`音调符号

|标注字符设置为 false|标注字符设置为 true|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>意大利语`it-it`音调符号

|标注字符设置为 false|标注字符设置为 true|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`| 
|`í`|`i`| 
|`î`|`i`| 
|`ò`|`o`| 
|`ó`|`o`| 
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>西班牙语`es-`音调符号

这包括墨西哥西班牙语和加拿大。

|标注字符设置为 false|标注字符设置为 true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>标点规范化

打开查询文本规范化到 LUIS JSON 应用文件中的标点`settings`参数。

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

以下查询文本显示音调符号将如何影响查询文本：

|具有标注字符设置为 False|具有标注字符设置为 True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>删除标点

以下标点操作会删除`NormalizePunctuation`设置为 true。

|标点|
|--|
|`-`| 
|`.`| 
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|
