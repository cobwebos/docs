---
title: 语音语音集 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何将语音服务拼音字母映射到国际拼音字母表 （IPA），以及何时使用该设置。
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675327"
---
# <a name="speech-service-phonetic-sets"></a>语音服务语音集

语音服务定义语音字母表（简称"电话集"），由七种语言组成;`en-US`、、、、、、、、、、、、、、、、、、、、、、、、、、 `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` `zh-TW` 语音服务电话集通常映射到<a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">国际语音字母表 （IPA）。 <span class="docon docon-navigate-external x-hidden-focus"> </span> </a> 语音服务电话集与[语音合成标记语言 （SSML）](speech-synthesis-markup.md)结合使用，作为文本到语音服务服务的一部分。 在本文中，您将了解如何映射这些电话组以及何时使用哪个电话组。

# <a name="en-us"></a>[zh-CN](#tab/en-US)

### <a name="english-suprasegmentals"></a>英语超分段

| 示例 1（为辅音启动，元音的单词首字母） | 示例 2（辅音的交音，元音的单词中音核） | 示例 3（辅音 Coda，元音单词"最终"） | 注释 |
|--|--|--|--|
| 汉堡 /b er **1** r - g ax r/ | 法拉费尔 /f 斧 - l aa **1** - f ax l/ | 吉他 /g ih - t aa **1** r/ | 语音服务电话集在应力音节的元音后放置压力 |
| inopportune /ih **2** - n aa - p ax r - t uw 1 n/ | 异位 /d ih - s ih **2**- m 斧 - l eh 1 - r ax - t iy/ | 劳动力 /w er 1 r k - f ao **2** r s/ | 语音服务电话集在子应力音节的元音后放置应力 |

### <a name="english-vowels"></a>英语元音

| `sapi` | `ipa` | 示例 1     | 示例 2 | 示例 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| 伊伊     | `i`   | **ea**t       | f**ee l**  | 瓦尔**伊**                  |
| Ih     | `ɪ`   | **i**f        | f**我**  |                             |
| 伊伊     | `eɪ`  | **a**te       | g**a**te  | d**ay**                     |
| 嗯     | `ɛ`   | **e**非常     | p**e**t   | m**eh** （最后罕见单词） |
| ae     | `æ`   | **一个**c-ctive    | c**a**t   | n**啊**（最后是稀有的单词） |
| aa     | `ɑ`   | **o**bstinate | **p o**py | r**啊**（最后是稀有的单词） |
| ao     | `ɔ`   | **o**范围    | c**au**se | Ut**啊**                    |
| 呃     | `ʊ`   | b**oo**k      |           |                             |
| ow     | `oʊ`  | **o**ld       | cl**o**ne | g**o**                      |
| 微波     | `u`   | **U**贝尔      | b**oo**st | t**oo**                     |
| 啊     | `ʌ`   | **ncle**     | c**u**t   |                             |
| 哎     | `aɪ`  | **i**ce       | b**i**te  | fl**y**                     |
| aw     | `aʊ`  | **ou**t       | **s ou**th | c**ow**                     |
| oy     | `ɔɪ`  | **oi**l       | j**oi**n  | t**oy**                     |
| y uw   | `ju`  | **余**马      | **h u**人 | f**ew**                     |
| 斧头     | `ə`   | **去**       | wom**a**n | **是一个**                    |

### <a name="english-r-colored-vowels"></a>英语 R 色元音

| `sapi` | `ipa` | 示例 1    | 示例 2      | 示例 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **耳朵**     | t**ir**amisu   | n**耳**   |
| eh r   | `ɛɹ`  | **飞机** | 应用程序**ar** | sc**ar**e  |
| 呃 r   | `ʊɹ`  |              |                | c**您的**e   |
| ay r   | `aɪɹ` | **艾里**土地  | f**ir**eplace  | 乔 **·奥伊尔**  |
| aw r   | `aʊɹ` | **小时**s    | p**欠债**ful   | **是我们的**   |
| ao r   | `ɔɹ`  | **或**安吉   | m**或**al      | **桨**   |
| aa r   | `ɑɹ`  | **阿尔**蒂斯特   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **耳朵**    | b**ir**d       | **f 您的**    |
| ax r   | `ɚ`   |              | 所有**er**gy    | 苏普**呃** |

### <a name="english-semivowels"></a>英语半音

| `sapi` | `ipa` | 示例 1           | 示例 2  | 示例 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**ith，**ue** | 阿尔**w**ays |           |
| y      | `j`   | **y**ard， f**e**w   | 上**我**上  |           |

### <a name="english-aspirated-oral-stops"></a>英语吸气口服停止

| `sapi` | `ipa` | 示例 1 | 示例 2   | 示例 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**ut   | 哈**普**恩  | fla**p**   |
| b      | `b`   | **b**ig   | 数字**b**er  | 克拉**b**   |
| t      | `t`   | **t**alk  | 卡皮**特**阿尔 | sough**t** |
| d      | `d`   | **d**ig   | 运行**d**om  | ro**d**    |
| k      | `k`   | **c**ut   | 斯拉**克**尔 | 伊拉**克**   |
| g      | `g`   | **g**o    | a**g**o     | 德拉**g**   |

### <a name="english-nasal-stops"></a>英国鼻停止

| `sapi` | `ipa` | 示例 1        | 示例 2  | 示例 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **我在**，粉碎   | 卡**m**时代 | 罗**姆**    |
| n      | `n`   | **n**o，**s n**ow | t**n**t   | 鸡**鸣 n** |
| ng     | `ŋ`   |                  | 利**n**k   | s**ing**    |

### <a name="english-fricatives"></a>英语装饰

| `sapi` | `ipa` | 示例 1   | 示例 2        | 示例 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f**ork    | le**f**t         | 哈尔**夫**   |
| v      | `v`   | **v**阿卢   | e**v**ent        | lo**v**e   |
| th     | `θ`   | **th**在    | empa**th y**      | 蒙**特**  |
| Dh     | `ð`   | **th**    | 莫尔**th**       | 斯莫**th** |
| s      | `s`   | **s**     | ri**s**k         | 事实**s**  |
| z      | `z`   | **z**ap     | 布**s**y         | 儿童**s**   |
| sh     | `ʃ`   | **sh** e    | 缩写**ti** | 鲁**什**   |
| zh     | `ʒ`   | **J**acques | 认罪**尿**     | 加拉**g**e |
| h      | `h`   | **h**埃尔普    | en**h**安切      | **a-h**a！  |

### <a name="english-affricates"></a>英语亲和力

| `sapi` | `ipa` | 示例 1 | 示例 2    | 示例 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**in  | 福**图**乌雷   | 阿塔**奇** |
| Jh     | `dʒ`  | **j**oy   | 奥里**g**inal | 猩**猩 g**e |

### <a name="english-approximants"></a>英语近似值

| `sapi` | `ipa` | 示例 1          | 示例 2  | 示例 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**id， g**l**广告  | pa**l**王牌 | 奇**尔** |
| r      | `ɹ`   | **r**ed，**b r ing** | bo**rr**ow | 塔**r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>法国超段

然而，语音服务电话集在强调音节的元音之后造成压力;`fr-FR`语音服务电话集不支持 IPA 子压力 ""。""。""。""。""。""。""。""。""。""。""。""。""。""。""。""。""。""。"表示""。"表示""。"表示" 如果需要 IPA 子应力，应直接使用 IPA。

### <a name="french-vowels"></a>法语元音

| `sapi` | `ipa` | 示例 1     | 示例 2       | 示例 3 |
|--------|-------|---------------|-----------------|-----------|
| （英文）。      | `a`   | **一个**rbre     | p**a**tte       | ir**a**   |
| aa     | `ɑ`   |               | p **=** te        | p**a**s   |
| aa =   | `ɑ̃`  | **恩**幻想    | enf**en**t      | t**em**ps |
| 斧头     | `ə`   |               | p**e**tite      | l**e**    |
| 嗯     | `ɛ`   | **e**lle      | **p e**rdu       | _t**ai**t |
| eu     | `ø`   | **_u**fs      | cr**eu**ser     | qu**eu**  |
| 伊伊     | `e`   | _mu           | 克雷丁          | *       |
| 嗯 |   | `ɛ̃`  | **im**波特 | p**ein**ture    | 垫**在** |
| 伊伊     | `i`   | **i**dée      | 宠物**i**te      | **我**   |
| Oe     | `œ`   | **_u**f       | p**eu**r        |           |
| 哦     | `ɔ`   | **o**bstacle  | c**o**rps       |           |
| 哦 ~   | `ɔ̃`  | **在**泽      | r 在 deur**上**     | b**上**   |
| ow     | `o`   | **奥**迪特尔  | b**au**政变    | p *****    |
| oe |   | `œ̃ ` | **联合国**        | l**un**di       | br**un**  |
| 微波     | `u`   | **乌**·拉格   | intr**ou**可 | **ou**    |
| 乌伊     | `y`   | **u**ne       | p**u**尼       | _l**u**   |

### <a name="french-consonants"></a>法语辅音

| `sapi` | `ipa` | 示例 1   | 示例 2     | 示例 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**_ te    | 哈 **·布**·伊勒   | ro**b**e                         |
| d      | `d`   | **d**ire    | 罗恩 **·德**·eur   | chau**d**e                       |
| f      | `f`   | **f**emme   | 苏**伊夫**·ix埃   | 博**f**                          |
| g      | `g`   | **g**奥切  | ***g**阿莱     | 巴**古**e                        |
| ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)**停车场** |
| Hy     | `ɥ`   | h**u**ile   | **n u**ire     |                                  |
| k      | `k`   | **c**艺术   | ***c**艾勒   | 是**c**                          |
| l      | `l`   | **l**ong    | l**l**ire     | 巴**l**                          |
| m      | `m`   | **姆**阿达姆  | 艾**姆**呃     | po**mm**e                        |
| n      | `n`   | **n**ous    | te**n**ir     | bo**nn**e                        |
| 新泽西州     | `ɲ`   |             |               | 佩 **·格恩**                       |
| p      | `p`   | **p**atte   | re**p**作为     | ca**p**                          |
| r      | `ʁ`   | **r**在     | 查 **·伊**奥特   | 森蒂**r**                       |
| s      | `s`   | **是我们的**  | **ss**ez     | pa**ss e**                        |
| sh     | `ʃ`   | **ch**安特 | 马**奇**因   | 波**奇**e                        |
| t      | `t`   | **t**_ te    | **\t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | 在**v**输入  | ré**v**e                         |
| w      | `w`   | **ou**i     | f**ou**ine    |                                  |
| y      | `j`   | **y**od     | p**i**_tiner  | 马赛 **·伊耶**                    |
| z      | `z`   | _z _ro   | 莱 **·斯**翁纳 | ro**s**e                         |
| zh     | `ʒ`   | **j**阿尔丁  | 男子**g**er    | 皮埃**格**                        |
|        | `n‿`  |             |               | **n 阿尔**布雷                     |
|        | `t‿`  |             |               | 泉**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *仅适用于一些外来词。*

> [!TIP]
> 语音`fr-FR`服务电话集不支持以下法语，`n‿`和`t‿`。 `z‿` 如果需要，应考虑直接使用 IPA。

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>德国超段

| 示例 1（为辅音启动，元音的单词首字母） | 示例 2（辅音的交音，元音的单词中音核） | 示例 3（辅音 Coda，元音单词"最终"） | 注释 |
|--|--|--|--|
| 安德斯 /a **1** n - d ax r s/ | 多普列克泽琴 /m uh l - t iy - p l iy - k a - ts y ow **1** ns - ts ay - c n/ | 生物 /b iy - ow - l ow - g iy **1**/ | 语音服务电话集在应力音节的元音后放置压力 |
| Allgemeinwissen / **2** l - g ax - m ay 1 n - v ih - s n/ | Abfallentsorgssfirma /a 1 p - f a l - = eh n t - z 哦**2** ax r - g ng s - f ih ax r - m a/ | 计算机学 /k oh m - p y uw 1 - t ax r - t ow - m ow - g r a - f iy **2**/ | 语音服务电话集在子应力音节的元音后放置应力 |

### <a name="german-vowels"></a>德语元音

| `sapi` | `ipa`     | 示例 1                             | 示例 2     | 示例 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| a:     | `aː`      | **一个**啤酒                              | 马斯特**a**b   | 舍姆**a**                         |
| （英文）。      | `a`       | **一个**失败                            | B**a**ch      | 阿加思**a**                         |
| 哦     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| 嗯：    | `ɛː`      | **·** 赫利希基特                       | B **+** r       | [<sup>1</sup>](#de-v-1)法西 **·艾** |
| 嗯     | `ɛ`       | **·** 恩德恩                            | Proz**e**nt   | 艾米格达尔 **·艾**                      |
| 斧头     | `ə`       | [<sup>2</sup>](#de-v-2)'v**e**rstauen | Aach**e**n    | 弗拉格**e**                          |
| 伊伊     | `iː`      | **我**跑了                              | abb**ie gt**   | 相对主义者**ie**            |
| Ih     | `ɪ`       | **我**年                            | **s i**ngen    | 木材**y**                          |
| eu     | `øː`      | ***** 森                              | abl **+** 斯滕  | 马尔姆 *****                          |
| ow     | `o`, `oː` | **o**hne                              | 巴尔克**o**n    | 特雷普特**ow**                        |
| Oe     | `œ`       | ***** ffnung                           | 贝夫 **·** 勒德恩 |                                    |
| 伊伊     | `e`, `eː` | **E**贝尔哈德                          | abf**e**gt    | b                                  |
| 微波     | `uː`      | **U**do                               | H**u**t       | Akk**u**                           |
| 呃     | `ʊ`       | **尤**恩特谢德斯                     | b**u**nt      |                                    |
| Ue     | `yː`      | **•** 贝尔穆特                           | pfl **@** gt    | 男士 **|**                           |
| 乌伊     | `ʏ`       | **•** ppig                             | S**y**茎    |                                    |

<a id="de-v-1"></a>
**1** *仅在外国血统的词语中，例如：法西**艾**。*<br>
<a id="de-v-2"></a>
**2** *字内仅在外来词中，如**点**缀。可音节最初在：'v**e**rstauen。*

### <a name="german-diphthong"></a>德国迪普通

| `sapi` | `ipa`       | 示例 1    | 示例 2          | 示例 3 |
|--------|-------------|--------------|--------------------|-----------|
| 哎     | `ai`        | **伊·** 恩萨姆   | 恩塔本**吉格** | 阿卜特 **·伊** |
| aw     | `au`        | **奥**·奥·埃恩    | 阿巴**奥**斯特        | 圣**奥**  |
| oy     | `ɔy`, `ɔʏ̯` | **欧**·菲里 | tr **_u**mt         | 施**尤** |

### <a name="german-semivowels"></a>德国半元音

| `sapi` | `ipa` | 示例 1 | 示例 2    | 示例 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | 阿比恩德 **·埃尔**恩 | 锁**er** |

### <a name="german-consonants"></a>德国辅音

| `sapi` | `ipa` | 示例 1 | 示例 2 | 示例 3 |
|--|--|--|--|--|
| b | `b` | **B**安克 |  | [<sup>1</sup>](#de-c-1)普**b** |  |
| c | `ç` | **查韦斯** | 梅格利**赫**斯特 | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **德**安肯 | [<sup>3</sup>](#de-c-3)伦**d**l | [<sup>4</sup>](#de-c-4)克劳**德** |  |
| Jh | `ʤ` | **J**eff | 杰马纳**g**t | [<sup>5</sup>](#de-c-5)陈**g**e |
| f | `f` | **弗**·阿赫特道尔 | 愤怒**ff**slustig | 阿布布鲁赫雷**夫** |  |
| g | `g` | **g**ut |  | [<sup>6</sup>](#de-c-6)格雷**g** |  |
| h | `h` | **H·** 乌桑博 |  |  |  |
| y | `j` | **J**od | 雷克特**我**上 | 胡**伊** |  |
| k | `k` | **K**奥马 | 阿斯佩**k**t | 弗莱克**克** |  |
| l | `l` | **l**au | \hne**l**n | 祖维**l** |  |
| m | `m` | **M**ut | **一个 m**t | 勒姆**m** |  |
| n | `n` | **n**un | **n**d | Huh**n** |  |
| ng | `ŋ` | [<sup>7</sup>](#de-c-7)**吴**恩 | 施瓦**恩克** | R**ing** |  |
| p | `p` | **P**阿特纳 | 阿布鲁**p**t | 提**普** |  |
| 普夫 | `pf` | **Pf**erd | 水坝**pf**t | 到**pf** |  |
| r | `ʀ`, `r`, `ʁ` | **瑞**塞 | 克努**rr**t | 哈**r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S**塔卡托 | bi**s**t | 米**斯** |  |
| sh | `ʃ` | **舒**莱 | mi**sch**t | 拉皮**施** |  |
| t | `t` | **T**raum | S**t**raée | Mu**t** |  |
| ts | `ts` | **Z**ug | Ar**z**t | 维特**兹** |  |
| ch | `tʃ` | **奇**·埃钦 | 阿夫格普**茨**特 | 联邦**tsch** |  |
| v | `v` | **w**因肯 | Q**u**alle | [<sup>9</sup>](#de-c-9)格**劳乌** |  |
| x | [<sup>10</sup>](#de-c-10)`x`，[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12)巴**赫**·拉赫 | 马**赫**特·梅格利**赫**斯特 | 施马**奇**'i**ch** |
| z | `z` | **向上** |  |  |  |
| zh | `ʒ` | **G**enre | **B·雷**津斯基 | 埃德维**g**e |

<a id="de-c-1"></a>
**1** *仅在外国血统的词语中，例如：Pu**b**。*<br>
<a id="de-c-2"></a>
**2** *"e"和"i"之后的软"ch"*<br>
<a id="de-c-3"></a>
**3** *仅在外国血统的词语中，例如：Len**d**l。*<br>
<a id="de-c-4"></a>
**4** *仅在外国血统的词语中，例如：Clau**d**e。*<br>
<a id="de-c-5"></a>
**5** *只用外国血统的词，如：陈**g**e。*<br>
<a id="de-c-6"></a>
**6** *字终端仅在外国来源的单词，如Gre**g**。*<br>
<a id="de-c-7"></a>
**7** *仅在外国血统的词语中，如 **：Ng**uyen。*<br>
<a id="de-c-8"></a>
**8** *仅在外国血统的单词中，如 **：S**taccato。*<br>
<a id="de-c-9"></a>
**9** *只用外国血统的词语，如：Gr**oo**ve。*<br>
<a id="de-c-10"></a>
**10** *IPA`x`是所有非正面元音（a，aa，哦，哦，嗯，uw和diphthong aw）之后，是一个硬的"ch"。*<br>
<a id="de-c-11"></a>
**11** *IPA`ç`是一个软的'ch'后前元音 （ih， iy， eh， ae， uy， ue， eu 也在 diphthongs ay， oy） 和辅音*<br>
<a id="de-c-12"></a>
**12** *单词最初只在外来词中，例如 **：J**uan。音节最初也用诸如：巴**切**拉赫这样的词。*<br>

### <a name="german-oral-consonants"></a>德国口服辅音

| `sapi` | `ipa` | 示例 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | 比奇利奇 /b 斧 - = 1 x t - l ih c/ |

> [!NOTE]
> 我们需要在两个不同的元音之间\]添加一个_gs电话，除了两个元音是一个真正的二元音。 这个口腔辅音是一个腺停止，欲了解更多信息，见<a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">格洛塔停止。 <span class="docon docon-navigate-external x-hidden-focus"> </a> </a>

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>西班牙语元音

| `sapi` | `ipa` | 示例 1    | 示例 2     | 示例 3    |
|--------|-------|--------------|---------------|--------------|
| （英文）。      | `a`   | **一个**lto     | c**ntar**    | cas**a**     |
| i      | `i`   | **伊**·贝里卡  | av**i**spa    | 税**i**     |
| e      | `e`   | **埃**勒凡特 | 在**e**nto    | 埃莱凡特**e** |
| o      | `o`   | **o**卡索    | 恩克 **·奥**恩特拉 | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**nta     | 胡安尔**u**   |

### <a name="spanish-consonants"></a>西班牙辅音

| `sapi` | `ipa`      | 示例 1  | 示例 2      | 示例 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**奥巴布 |                | am**b**        |
|        | `β`        |            | 宝**b**ab     | 保巴**b**     |
| ch     | `tʃ`       | **查韦斯** | co**ch**e      | 马拉克**奇** |
| d      | `d`        | **d**edo   |                | 波特兰**d**   |
|        | `ð`        |            | **德·德**奥       | 韦尔**达 d**     |
| f      | `f`        | **f**_cil  | ele**f**ante   | 普**f**        |
| g      | `g`        | **g**安加  |                | 多平**g**     |
|        | `ɣ`        |            | a**g**ua       | 图加尔**g**     |
| j      | `j`        | **i**odo   | 卡尔 **·伊**·恩特   | re**y**        |
| Jj     | `j.j` `jj` |            | vi**ll**a      |                |
| k      | `k`        | **c**奥切  | 波**c**a       | 蒂蒂尼**c**    |
| l      | `l`        | **l**_piz  | a**l**a        | 线**l**     |
| ll     | `ʎ`        | **ll**ave  | 德萨罗 **·洛奥** |                |
| m      | `m`        | **m**顺序 | 一**个 m**ar       | 阿尔布**姆**      |
| n      | `n`        | **n**ada   | ce**n**a       | 拉特内**n**      |
| 新泽西州     | `ɲ`        | **_aía**   | 阿拉 **·阿**佐    |                |
| p      | `p`        | **p**奥卡   | 到**p**o       | 斯托**普**       |
| r      | `ɾ`        |            | ca**r**a       | 阿布里**r**      |
| Rr     | `r`        | **r**adio  | co**rr**e      | 普**rr**       |
| s      | `s`        | **s**aco   | va**s**o       | 佩洛**s**      |
| t      | `t`        | **t**Oldo  | a**t**ar       | disque**t**    |
| th     | `θ`        | **兹**埃布拉  | a**z**ul       | 莱皮**兹**      |
| w      | `w`        | **h u**eso  | ag**u**a       | 瓜**乌**       |
| x      | `x`        | **j**奥塔   | a**j**o        | 雷洛**j**      |

> [!TIP]
> 语音`es-ES`服务电话集不支持以下西班牙语 IPA`β`和`ð`。 `ɣ` 如果需要，应考虑直接使用 IPA。

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

的`zh-CN`语音服务电话集基于本机电话<a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">拼音<span class="docon docon-navigate-external x-hidden-focus"></span></a>集。

### <a name="tone"></a>色调

| 拼音 | `sapi` | 字符示例 |
|-------------|--------|-------------------|
| mé          | ma 1  | ·                 |
| mé          | ma 2  | •                 |
| mmm          | 马 3  | *                 |
| mé          | 马 4  | ·                 |
| ma          | 马 5  | ·                 |

#### <a name="example"></a>示例

| 字符 | 语音服务                |
|-----------|-------------------------------|
| •      | zu 3 - 志 1 - 关 1 - xi 5 |
| 累进        | 雷 3 -津 4                 |
| ·       | Xi 1 - Zhai 2 - 翔 4      |

# <a name="zh-tw"></a>[日-TW](#tab/zh-TW)

语音`zh-TW`服务电话集基于本机电话<a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">Bopomofo<span class="docon docon-navigate-external x-hidden-focus"></span></a>集。

### <a name="tone"></a>色调

| 语音服务音 | 波波莫沃语调 | 示例（单词） | 语音服务电话 | 博波莫沃 | 拼音 （*） |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ·                   | empty         | ·              | ·                   | ·       | 日恩        |
| ˊ                   | ˊ             | ·              | ·                   | ·      | ché         |
| ·                   | ·             | *              | ·                   | ·      | d=          |
| ·                   | ·             | ·              | ·                   | ·      | 旺昂        |
| ˙                   | ˙             | ·             | [ ]               | [ ]  | y 翁子    |

#### <a name="example"></a>示例

| 字符 | `sapi`   |
|-----------|----------|
| *         | ·      |
| ·        | ·   |
| ·        | · |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

的`ja-JP`语音服务电话集基于本机电话<a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana<span class="docon docon-navigate-external x-hidden-focus"></span></a>集。

### <a name="stress"></a>应力

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ`主应力 |
| `+`    | `ˌ`子应力  |

#### <a name="example"></a>示例

| 字符 | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| ·        | *    | 戈瓦塞吉   |
| ·       | ショュ'ウ?ャ | [joj_w_ja |
| ·       | [ ]  | 萨吉特基卡 |

***