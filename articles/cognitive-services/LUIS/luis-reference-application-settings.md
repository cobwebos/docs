---
title: 应用程序设置 - LUIS
description: Azure 认知服务语言理解应用的应用程序设置存储在应用和门户中。
ms.topic: reference
ms.date: 05/04/2020
ms.openlocfilehash: 7b545e0959a43520b7d643ef8c0658a1e1a3b295
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590973"
---
# <a name="app-and-version-settings"></a>应用和版本设置

这些设置存储在[导出](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)的应用中，并通过 REST API 或 LUIS 门户更新。

更改应用版本设置会将应用训练状态重置为“未训练”。

[!INCLUDE [App and version settings](includes/app-version-settings.md)]


文本参考和示例包括：

* [标点符号](#punctuation-normalization)
* [语音](#diacritics-normalization)

## <a name="diacritics-normalization"></a>音调符号规范化

以下话语显示了音调符号规范化如何影响话语：

|音调符号设置为 false 时|音调符号设置为 true 时|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>对音调符号的语言支持

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>巴西葡萄牙语 `pt-br` 音调符号

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

以下话语显示了标点如何影响话语：

|当标点设置为 False 时|当标点设置为 True 时|
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

## <a name="next-steps"></a>后续步骤

* 了解音调符号和标点的[概念](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)。
