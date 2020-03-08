---
title: 语音拼音集-语音服务
titleSuffix: Azure Cognitive Services
description: 了解语音服务拼音字母如何映射到国际拼音字母（IPA）以及何时使用哪个设置。
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675327"
---
# <a name="speech-service-phonetic-sets"></a>语音服务拼音设置

语音服务定义了包含七种语言的拼音字母（简称 "电话设置"）;`en-US`、`fr-FR`、`de-DE`、`es-ES`、`ja-JP`、`zh-CN`和 `zh-TW`。 语音服务电话通常会映射到<a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">国际注音字母表（IPA） <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>。 语音服务电话组与[语音合成标记语言（SSML）](speech-synthesis-markup.md)结合使用，作为文本到语音服务产品的一部分。 在本文中，你将了解如何映射这些电话集以及何时使用哪些电话集。

# <a name="en-us"></a>[en-us](#tab/en-US)

### <a name="english-suprasegmentals"></a>英语 suprasegmentals

| 示例1（开始对于辅音，为元音输入单词初始值） | 示例2（Intervocalic for 辅音，word 词中核心） | 示例3（用于辅音的 Coda，元音的单词 final） | 注释 |
|--|--|--|--|
| 快餐/b er **1** r-g ax r/ | falafel/f ax-l aa **1** -f ax l/ | 吉他/g ih-t aa **1** r/ | 语音服务电话设置在压力音节的元音后施加压力 |
| 不合时宜/ih **2** -n aa-p ax r-t uw 1 n/ | 选用/d ih-s ih **2**-m i i i url-iy-ung/ | 劳动力/w er 1 r k-f ao **2** r s/ | 语音服务电话设置将压力置于子负载音节的元音后面 |

### <a name="english-vowels"></a>英文元音

| `sapi` | `ipa` | 示例 1     | 示例 2 | 示例 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| url-iy-ung     | `i`   | **ea**t       | f**ee**l  | vall**3om-ey-2vk**                  |
| ih     | `ɪ`   | **i**f        | f**i**ll  |                             |
| 3om-ey-2vk     | `eɪ`  | **te**       | g**a**te  | d**ay**                     |
| 吧     | `ɛ`   | **e**非常     | p**e**t   | m**eh** （罕见词 finally） |
| ae     | `æ`   | **c)**    | c**a**t   | n**ah** （罕见词最终） |
| 后面     | `ɑ`   | **o**bstinate | p**o**ppy | r**ah** （罕见词最终） |
| ao     | `ɔ`   | **o**范围    | c**au**se | 不在**ah**                    |
| 哦     | `ʊ`   | b**oo**k      |           |                             |
| o     | `oʊ`  | **o**ld       | cl**o**ne | g**o**                      |
| uw     | `u`   | **U**ber      | b**oo**st | t**oo**                     |
| 啊     | `ʌ`   | **u**ncle     | c**u**t   |                             |
| ay     | `aɪ`  | **i**ce       | b**i**te  | 佛罗里达州**y**                     |
| aw     | `aʊ`  | **ou**t       | s**ou** | c**o**                     |
| oy     | `ɔɪ`  | **oi**l       | j**oi**n  | t**oy**                     |
| y uw   | `ju`  | **Yu**ma      | h**u**man | f**新建**                     |
| ax150     | `ə`   | **开始**       | wom**n** | 是                    |

### <a name="english-r-colored-vowels"></a>英语 R-彩色元音

| `sapi` | `ipa` | 示例 1    | 示例 2      | 示例 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **耳**     | t**ir**amisu   | n**耳**   |
| eh r   | `ɛɹ`  | **飞机** | 应用**ar**ently | sc**ar**e  |
| 糟糕 r   | `ʊɹ`  |              |                | c**你的**电子   |
| ay r   | `aɪɹ` | **Protokol**土地  | f**ir**eplace  | ch**oir**  |
| aw r   | `aʊɹ` | **小时**s    | p**下限**rest-ful   | s   |
| ao r   | `ɔɹ`  | **或**a)   | m**或**al      | s**oar**   |
| aa r   | `ɑɹ`  | **ar**tist   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **耳**第一    | b**ir**d       | f    |
| ax r   | `ɚ`   |              | 所有**er**gy    | 禁止**er** |

### <a name="english-semivowels"></a>英语 Semivowels

| `sapi` | `ipa` | 示例 1           | 示例 2  | 示例 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**i，s**ue**de | al**w**ays |           |
| y      | `j`   | **y**ard，f**e**w   | **我**开启  |           |

### <a name="english-aspirated-oral-stops"></a>英语 aspirated 口头停止

| `sapi` | `ipa` | 示例 1 | 示例 2   | 示例 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**   | ha**pp**en  | fla**p**   |
| b      | `b`   | **b**ig   | num**b**er  | cra**b**   |
| t      | `t`   | **t**alk  | capi**t**al | sough**t** |
| d      | `d`   | **d**ig   | 已运行**d**om  | ro**d**    |
| k      | `k`   | **c**   | sla**ck**er | Ira**q**   |
| g      | `g`   | **g**o    | a**g**o     | dra**g**   |

### <a name="english-nasal-stops"></a>英语 Nasal 停止

| `sapi` | `ipa` | 示例 1        | 示例 2  | 示例 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**at，smash   | ca**m**年代 | 金塔**m**    |
| n      | `n`   | **n**o，s**n**o | te**n**t   | chicke**n** |
| ng     | `ŋ`   |                  | l**n**k   | s    |

### <a name="english-fricatives"></a>英语 fricatives

| `sapi` | `ipa` | 示例 1   | 示例 2        | 示例 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f**o)    | le**f**t         | hal**f**   |
| v      | `v`   | **v**alue   | e**v**ent        | lo**v**e   |
| th     | `θ`   | **第**    | empa**第**y 个      | 周一**次**  |
| dh     | `ð`   | **第**en    | mo**次**er       | **第**smoo |
| s      | `s`   | **s**it     | ri**s**k         | 事实**s**  |
| {1}z{2}      | `z`   | **z**ap     | bu**s**y         | 儿童   |
| sh     | `ʃ`   | **sh** e    | abbrevia**ti**on | ru**sh**   |
| zh     | `ʒ`   | **J**acques | 劝告**s**u)     | gara**g**e |
| h      | `h`   | **h**elp    | en**h**外观      | a-**h**a！  |

### <a name="english-affricates"></a>英语 affricates

| `sapi` | `ipa` | 示例 1 | 示例 2    | 示例 3  |
|--------|-------|-----------|--------------|------------|
| 48     | `tʃ`  | **ch**in  | fu**t**u)   | atta**ch** |
| yey-jh-tfz     | `dʒ`  | **j**oy   | 原**g**inal | oran**g**e |

### <a name="english-approximants"></a>英语 approximants

| `sapi` | `ipa` | 示例 1          | 示例 2  | 示例 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**id，g**l**ad  | pa**l**ace | chi**ll** |
| r      | `ɹ`   | **研发**，b**r** | bo**rr**o | ta**r**   |

# <a name="fr-fr"></a>[fr](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>法语 suprasegmentals

语音服务电话集将压力置于压力音节的元音后面，但`fr-FR` 语音服务电话集不支持 IPA substress "ˌ"。 如果需要 IPA substress，则应直接使用 IPA。

### <a name="french-vowels"></a>法语元音

| `sapi` | `ipa` | 示例 1     | 示例 2       | 示例 3 |
|--------|-------|---------------|-----------------|-----------|
| （英文）。      | `a`   | **rbre**     | p**a**tte       | ir**a**   |
| 后面     | `ɑ`   |               | p **-** te        | p**a**s   |
| aa ~   | `ɑ̃`  | **en**fant    | enf**en**t      | t**em**ps |
| ax150     | `ə`   |               | p**e**tite      | l**e**    |
| 吧     | `ɛ`   | **e**lle      | p**e**rdu       | ét**ai**t |
| 各     | `ø`   | **œu**fs      | cr**eu**ser     | qu**eu**  |
| 3om-ey-2vk     | `e`   | ému           | crétin          | ôté       |
| eh ~   | `ɛ̃`  | **im**portant | p**ein**ture    | 材料 |
| url-iy-ung     | `i`   | **我**dée      | 宠物**i**te      | 上午   |
| oe     | `œ`   | **œu**f       | p**eu**r        |           |
| 哦     | `ɔ`   | **o**bstacle  | c**o**rps       |           |
| 哦 ~   | `ɔ̃`  | **on**ze      | deur**上的**r     | b**打开**   |
| o     | `o`   | **au**diteur  | b**eau**coup    | p**ô**    |
| oe ~   | `œ̃ ` | **&**        | l**取消**di       | b**卸载**  |
| uw     | `u`   | **ou**trage   | intr**ou**vable | **部门**    |
| uy     | `y`   | **u**ne       | p**u**nir       | él**u**   |

### <a name="french-consonants"></a>法语辅音

| `sapi` | `ipa` | 示例 1   | 示例 2     | 示例 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**ête    | ha**b**ille   | ro**b**e                         |
| d      | `d`   | **d**protokol    | ron**d**eur   | chau**d**e                       |
| f      | `f`   | **f**emme   | su**ff**ixe   | bo**f**                          |
| g      | `g`   | **g**auche  | é**g**ale     | ba**gu**e                        |
| ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)停车场 |
| 1xt-hy-ubw     | `ɥ`   | h**u**ile   | n**u**protokol     |                                  |
| k      | `k`   | **c**arte   | é**c**aille   | 为**c**                          |
| l      | `l`   | **l**总监    | é**l**protokol     | ba**l**                          |
| m      | `m`   | **m**adame  | ai**m**er     | po**mm**e                        |
| n      | `n`   | **n**个 ou    | te**n**ir     | bo**nn**e                        |
| nj     | `ɲ`   |             |               | pei**gn**e                       |
| p      | `p`   | **p**atte   | 将**p**重为     | ca**p**                          |
| r      | `ʁ`   | **r**at     | cha**r**iot   | senti**r**                       |
| s      | `s`   | **s**ourir  | **ss**ez     | pa**ss**e                        |
| sh     | `ʃ`   | **ch**anter | ma**ch**"   | po**ch**e                        |
| t      | `t`   | **t**ête    | ô**t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | 在**v**中输入  | rê**v**e                         |
| w      | `w`   | **ou**i     | f**ou**"    |                                  |
| y      | `j`   | **y**od     | p**i**étiner  | Marse**ille**                    |
| {1}z{2}      | `z`   | \* * z * * éro   | chiang**s**onner | ro**s**e                         |
| zh     | `ʒ`   | **j**ardin  | man**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | quan**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>*仅对某些外文单词*
**1** 。

> [!TIP]
> `fr-FR` 语音服务电话集不支持以下法语 liasions、`n‿`、`t‿`和 `z‿`。 如果需要，应考虑直接使用 IPA。

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>德国 suprasegmentals

| 示例1（开始对于辅音，为元音输入单词初始值） | 示例2（Intervocalic for 辅音，word 词中核心） | 示例3（用于辅音的 Coda，元音的单词 final） | 注释 |
|--|--|--|--|
| anders/a **1** n d ax r s/ | Multiplikationszeichen/m 噢 l t url-iy-ung-p l url-iy-ung-k a-ts y o **1** n s-ts ay-c n/ | Biologie/b url-iy-ung-o-l o-g url-iy-ung **1**/ | 语音服务电话设置在压力音节的元音后施加压力 |
| Allgemeinwissen/a **2** l-g ax-m ay 1 n-v ih-s n/ | Abfallentsorgungsfirma/a 1 p-f a l-^ eh n t-z，\r **2** ax r-g | Computertomographie/k 哦 m-p y uw 1-t ax r-t o-m o-g r a-f url-iy-ung **2**/ | 语音服务电话设置将压力置于子负载音节的元音后面 |

### <a name="german-vowels"></a>德国元音

| `sapi` | `ipa`     | 示例 1                             | 示例 2     | 示例 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| a:     | `aː`      | **Ber**                              | Maßst**a**b   | 方案                         |
| （英文）。      | `a`       | **Bfall**                            | B**a**ch      | Agath                         |
| 哦     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| 吧    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1</sup>](#de-v-1)Fasci**ae** |
| 吧     | `ɛ`       | **ä**ndern                            | Proz**e**nt   | Amygdal**ae**                      |
| ax150     | `ə`       | [<sup>2</sup>](#de-v-2)"v**e**rstauen | Aach**e**n    | Frag**e**                          |
| url-iy-ung     | `iː`      | **我**运行                              | abb**ie**gt   | Relativitätstheor**ie**            |
| ih     | `ɪ`       | **我**nnung                            | s**i i**    | 木材**y**                          |
| 各     | `øː`      | **Ö**sen                              | abl**ö**sten  | Malm**ö**                          |
| o     | `o`、`oː` | **o**hne                              | Balk**o**n    | Trept**o**                        |
| oe     | `œ`       | **Ö**ffnung                           | bef**ö**rdern |                                    |
| 3om-ey-2vk     | `e`、`eː` | **E**berhard                          | .abf**e**gt    | b                                  |
| uw     | `uː`      | **U**                               | H**u**t       | Akk**u**                           |
| 哦     | `ʊ`       | **U**nterschiedes                     | b**u**nt      |                                    |
| ue-v     | `yː`      | **Ü**bermut                           | default.pfl**ü**gt    | 男人**ü**                           |
| uy     | `ʏ`       | **ü**ppig                             | S**y**词干    |                                    |

<a id="de-v-1"></a> *仅在外部源的单词中 
1，如： Fasci**ae**。*<br>
<a id="de-v-2"></a>
**2** *intially 仅**用于**外部源的单词，如 ppointment。音节-起初为： ' v**e**rstauen。*

### <a name="german-diphthong"></a>德国 diphthong

| `sapi` | `ipa`       | 示例 1    | 示例 2          | 示例 3 |
|--------|-------------|--------------|--------------------|-----------|
| ay     | `ai`        | **ei**nsam   | Unabhängigk**ei**t | Abt**ei** |
| aw     | `au`        | **au**ßen    | abb**au**st        | St**au**  |
| oy     | `ɔy`、`ɔʏ̯` | **欧盟**phorie | tr**äu**mt         | sch-m**欧盟** |

### <a name="german-semivowels"></a>德国 semivowels

| `sapi` | `ipa` | 示例 1 | 示例 2    | 示例 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | abänd**er**n | 锁定**er** |

### <a name="german-consonants"></a>德国辅音

| `sapi` | `ipa` | 示例 1 | 示例 2 | 示例 3 |
|--|--|--|--|--|
| b | `b` | **B**ank |  | [<sup>1</sup>](#de-c-1)Pu**b** |  |
| c | `ç` | **Ch**emie | mögli**ch**st | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**anken | [<sup>3</sup>](#de-c-3)Len**d**l | [<sup>4</sup>](#de-c-4)Clau**d**e |  |
| yey-jh-tfz | `ʤ` | **J**eff | gemana**g**t | [<sup>5</sup>](#de-c-5)更改**g**e |
| f | `f` | **F**ahrtdauer | angri**ff**slustig | abbruchrei**f** |  |
| g | `g` | **g** |  | [<sup>6</sup>](#de-c-6)Gre**g** |  |
| h | `h` | **H**ausanbau |  |  |  |
| y | `j` | **J**od | Reakt**i**on | hu**i** |  |
| k | `k` | **K**oma | Aspe**k**t | Flec**k** |  |
| l | `l` | **l**au | ähne**l**n | zuvie**l** |  |
| m | `m` | **M**不到 | A**m**t | Leh**m** |  |
| n | `n` | **n**un | u**n**d | 是的**n** |  |
| ng | `ŋ` | [<sup>7</sup>](#de-c-7)个**Ng**uyen | Schwa**nk** | R**ing** |  |
| p | `p` | **P**artner | abru**p**t | Ti**p** |  |
| pf | `pf` | **Pf**erd | dam**pf**t | 到**pf** |  |
| r | `ʀ`、`r`、`ʁ` | **R**eise | knu**rr**t | Haa**r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S**taccato | bi**s**t | mie**s** |  |
| sh | `ʃ` | **Sch-m**u) | mi**sch-m**t | lappi**sch-m** |  |
| t | `t` | **T**raum | S**t**raße | Mu**t** |  |
| ts | `ts` | **Z**buj-ug-pkt | Ar**z**t | Wit**z** |  |
| 48 | `tʃ` | **Tsch**echien | aufgepu**tsch**t | bundesdeu**tsch** |  |
| v | `v` | **w**inken | Q**u**alle | [<sup>9</sup>](#de-c-9)Gr**oo**ve |  |
| x | [<sup>10</sup>](#de-c-10)`x`，[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12)Ba**ch**erach | Ma**ch**t mögli**ch**st | 架构**ch** ' i**ch** |
| {1}z{2} | `z` | **s**uper |  |  |  |
| zh | `ʒ` | **G**enre | B**重新**ezinski | Edvi**g**e |

<a id="de-c-1"></a> *仅限外原点的单词 
1，如： Pu**b**。*<br>
<a id="de-c-2"></a> *"e" 和 "i" 后 
2 软 "ch"*<br>
<a id="de-c-3"></a> *仅限外原点的单词 
3，如： Len**d**l。*<br>
<a id="de-c-4"></a>仅 **
** *为外部源（例如： Clau**d**e）的单词。*<br>
<a id="de-c-5"></a>*仅限外部源（例如：更改**g**e）中的单词*
**5** 。<br>
<a id="de-c-6"></a>
**6** *terminally 仅用于外部源的单词（如 Gre**g**）。*<br>
<a id="de-c-7"></a>仅 
**7** *个字（如： **Ng**uyen）。*<br>
<a id="de-c-8"></a>仅 
**第 8**个*字，如： **S**taccato。*<br>
<a id="de-c-9"></a> *仅限外原点的单词 
9，如： Gr**oo**ve。*<br>
<a id="de-c-10"></a>
**10** *IPA `x` 在所有非前端元音（a、aa、哦、o、uw 和 diphthong aw）之后都是一个硬 "ch"。*<br>
<a id="de-c-11"></a>
**11** *，IPA `ç` 是前面元音元音后的软 "ch" （ih，url-iy-ung，eh，ae，uy，ue，oe，eu 也在 diphthongs ay，oy）和辅音*<br>
<a id="de-c-12"></a>
**12** *个字，最初仅限外部源的单词，如： **J**uan。音节-最初还采用类似于： Ba**ch**erach 的词。*<br>

### <a name="german-oral-consonants"></a>德语口头辅音

| `sapi` | `ipa` | 示例 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich/b ax-^ a 1 x t-l ih c/ |

> [!NOTE]
> 需要在两个不同元音之间添加 [gs\] 电话，但这两个元音为正版 diphthong。 此口头辅音为喉塞音符，有关详细信息，请参阅 <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">喉塞音 stop <span class="docon docon-navigate-external x-hidden-focus"></a></a>。

# <a name="es-es"></a>[es](#tab/es-ES)

### <a name="spanish-vowels"></a>西班牙语元音

| `sapi` | `ipa` | 示例 1    | 示例 2     | 示例 3    |
|--------|-------|--------------|---------------|--------------|
| （英文）。      | `a`   | **lto**     | c**a**ntar    | ca**a**     |
| i      | `i`   | **我**bérica  | av**i**spa    | 税金**i**     |
| e      | `e`   | **e**lefante | at**e**    | elefant**e** |
| o      | `o`   | **o**caso    | enc**o**ntrar | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**nta     | Juanl**u**   |

### <a name="spanish-consonants"></a>西班牙语辅音

| `sapi` | `ipa`      | 示例 1  | 示例 2      | 示例 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | am**b**        |
|        | `β`        |            | bao**b**ab     | baoba**b**     |
| 48     | `tʃ`       | **ch**eque | co**ch**e      | Marraque**ch** |
| d      | `d`        | **d**克瓦语   |                | portlan**d**   |
|        | `ð`        |            | de**d**o       | verda**d**     |
| f      | `f`        | **f**ácil  | 元素**f**上午   | pu**f**        |
| g      | `g`        | **g**潘  |                | dópin**g**     |
|        | `ɣ`        |            | **g**ua       | tuare**g**     |
| j      | `j`        | **我**odo   | cal**i**ente   | 重        |
| jj     | `j.j` `jj` |            | vi**ll**      |                |
| k      | `k`        | **c**oche  | bo**c**a       | titáni**c**    |
| l      | `l`        | **l**ápiz  | a**l**a        | corde**l**     |
| ll     | `ʎ`        | **ll**  | desarro**ll**o |                |
| m      | `m`        | **m**顺序 | a**m**ar       | álbu**m**      |
| n      | `n`        | **n**ada   | ce**n**a       | rató**n**      |
| nj     | `ɲ`        | **-** aña   | ara **-** azo    |                |
| p      | `p`        | **p**oca   | 到**p**o       | 停止**p**       |
| r      | `ɾ`        |            | ca**r**a       | abri**r**      |
| rr     | `r`        | **r**adio  | 共同**rr**e      | pu**rr**       |
| s      | `s`        | **s**简称 aco   | va**s**o       | pelo**s**      |
| t      | `t`        | **t**oldo  | **t**ar       | disque**t**    |
| th     | `θ`        | **z**ebra  | a**z**ul       | lápi**z**      |
| w      | `w`        | h**u**eso  | ag**u**a       | gua-eo-cwy**u**       |
| x      | `x`        | **j**ota   | **j**o        | relo**j**      |

> [!TIP]
> `es-ES` 语音服务电话集不支持以下西班牙语 IPA、`β`、`ð`和 `ɣ`。 如果需要，应考虑直接使用 IPA。

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

`zh-CN` 的语音服务电话设置基于本机电话<a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">拼音<span class="docon docon-navigate-external x-hidden-focus"></span> </a>集。

### <a name="tone"></a>色调

| 拼音 | `sapi` | 字符示例 |
|-------------|--------|-------------------|
| mā          | ma 1  | 妈                 |
| má          | ma 2  | 麻                 |
| mǎ          | ma 3  | 马                 |
| mà          | ma 4  | 骂                 |
| ma          | ma 5  | 嘛                 |

#### <a name="example"></a>示例

| 字符 | 语音服务                |
|-----------|-------------------------------|
| 组织关系      | zu 3-zhi 1-guan 1-xi 5 |
| 累进        | lei 3-金4                 |
| 西宅巷       | xi 1-zhai 2-xiang 4      |

# <a name="zh-tw"></a>[zh-chs-幼圆](#tab/zh-TW)

`zh-TW` 的语音服务电话设置基于本机电话<a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">注音<span class="docon docon-navigate-external x-hidden-focus"></span> </a>集。

### <a name="tone"></a>色调

| 语音服务音 | 注音符号 | 示例（word） | 语音服务电话 | 注音 | 拼音（拼音） |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | empty         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚ﨩ˊ                   | ㄔㄚ﨩ˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚˇ                   | ㄉㄚˇ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥˇㄗ̇               | 一ㄥˇㄗ̇  | yǐng zi    |

#### <a name="example"></a>示例

| 字符 | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡˇ      |
| 然后        | ㄖㄢ﨩ˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢˇㄉㄧㄠˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

`ja-JP` 的语音服务电话设置基于本机电话<a href="https://en.wikipedia.org/wiki/Kana" target="_blank">假名<span class="docon docon-navigate-external x-hidden-focus"></span> </a>集。

### <a name="stress"></a>施加

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` mainstress |
| `+`    | `ˌ` substress  |

#### <a name="example"></a>示例

| 字符 | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | 中转ˈ wɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化       | サィテキカ +  | sajitecikaˌ |

***