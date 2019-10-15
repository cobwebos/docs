---
title: 应用程序设置-LUIS
titleSuffix: Azure Cognitive Services
description: 了解语言理解应用的应用程序设置。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 6d516590b90efd937789fa0847b707d2521f6459
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932579"
---
# <a name="application-settings"></a>应用程序设置

这些应用程序设置存储在[导出的](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)应用中，并使用 REST API 进行[更新](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)。 更改应用版本设置会将应用训练状态重置为“未训练”。

|设置|默认值|说明|
|--|--|--|
|NormalizePunctuation|True|删除标点。|
|NormalizeDiacritics|True|删除音调符号。|

## <a name="diacritics-normalization"></a>音调符号规范化 

在 `settings` 参数中针对 LUIS JSON 应用文件的音调符号打开话语规范化。

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

以下话语显示了音调符号规范化如何影响话语：

|音调符号设置为 false 时|音调符号设置为 true 时|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>对音调符号的语言支持

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>葡萄牙语(巴西) `pt-br` 音调符号

|音调符号设置为 false|音调符号设置为 true|
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

#### <a name="dutch-nl-nl-diacritics"></a>荷兰语 `nl-nl` 音调符号

|音调符号设置为 false|音调符号设置为 true|
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

#### <a name="french-fr--diacritics"></a>法语 `fr-` 音调符号

这包括法国和加拿大的子区域性。

|音调符号设置为 false|音调符号设置为 true|
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

#### <a name="german-de-de-diacritics"></a>德语 `de-de` 音调符号

|音调符号设置为 false|音调符号设置为 true|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>意大利语 `it-it` 音调符号

|音调符号设置为 false|音调符号设置为 true|
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

#### <a name="spanish-es--diacritics"></a>西班牙语 `es-` 音调符号

这包括西班牙和加拿大墨西哥。

|音调符号设置为 false|音调符号设置为 true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>标点规范化

在 `settings` 参数中针对 LUIS JSON 应用文件的标点打开话语规范化。

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

以下话语显示了音调符号如何影响话语：

|音调符号设置为 False 时|音调符号设置为 True 时|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>标点已删除

将 `NormalizePunctuation` 设置为 true 时，将删除以下标点符号。

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
