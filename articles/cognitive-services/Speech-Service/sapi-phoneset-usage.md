---
title: SAPI 手机设置使用情况-语音服务
titleSuffix: Azure Cognitive Services
description: 适用于 SAPI 的详细电话使用情况文档。
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: jiajzhan
ms.openlocfilehash: c14636e46a09bf4e7528dd732548c1a09dc10f92
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228633"
---
# <a name="sapi-phone-set-usage"></a>SAPI 手机设置使用情况

Microsoft 定义了七种语言的 SAPI 手机集（en-us、fr-fr、反 DE、es、ja-jp、zh-chs、zh-chs）。 SAPI 手机集的使用相对简单，因此尤其适用于本机扬声器。 例如，Ja-JP 使用假名作为手机，zh-chs 使用拼音作为手机集。 但有时它的功能不够强大，例如，不能用到法语的 SAPI 手机集来表示法语联络。 

## <a name="en-us"></a>zh-CN

| SAPI  | IPA  | 示例1（开始 for 辅音，元音的单词初始值） | example2 （intervocalic for 辅音，word 中间词核心 for 元音） | example3 （用于辅音的 coda，元音的单词 final） | comments                                                     |
| ---- | ---- | ---------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ |
| SUPRASEGMENTALS|      |                                                            |                                                              |                                                         |                                                              |
| 1                    | ˈ    | 快餐/b er **1** r-g ax r/                                | falafel/f ax-l aa **1** -f ax l/                           | 吉他/g ih-t aa **1** r/                               | 在压力音节的元音后，SAPI 手机集施加压力 |
| 2                    | ˌ    | 不合时宜/ih **2** -n aa-p ax r-t uw 1 n/              | 选用/d ih-s ih **2**-m i i i url-iy-ung/ | 劳动力/w er 1 r k-f ao **2** r s/                     | Substressed 音节后，SAPI 手机集将压力置于元音后面 |
| -                    | 。    |                                                            |                                                              |                                                         |                                                              |
| 后面               |      |                                                            |                                                              |                                                         |                                                              |
| url-iy-ung                   | i    | **ea**t                                                        | f**ee**l                                                         | vall**3om-ey-2vk**                                                  |                                                              |
| ih                   | ɪ    | **i**f                                                         | f**i**ll                                                         |                                                         |                                                              |
| 3om-ey-2vk                   | eɪ   | **te**                                                        | g**a**te                                                         | d**ay**                                                     |                                                              |
| 吧                   | ɛ    | **e**非常                                                      | p**e**t                                                          | m**eh**（罕见词 finally）                               |                                                              |
| ae                   | æ    | **c)**                                                     | c**a**t                                                          | n**ah**（罕见词最终）                               |                                                              |
| 后面                   | ɑ    | **o**bstinate                                                  | p**o**ppy                                                        | r**ah**（罕见词最终）                               |                                                              |
| ao                   | ɔ    | **o**范围                                                     | c**au**se                                                        | 不在**ah**                                                    |                                                              |
| 哦                   | ʊ    |                                                            | b**oo**k                                                         |                                                         |                                                              |
| o                   | oʊ   | **o**ld                                                        | cl**o**ne                                                        | g**o**                                                      |                                                              |
| uw                   | u    | **U**ber                                                       | b**oo**st                                                        | t**oo**                                                     |                                                              |
| 啊                   | ʌ    | **u**ncle                                                      | c**u**t                                                          |                                                         |                                                              |
| ay                   | aɪ   | **i**ce                                                        | b**i**te                                                         | 佛罗里达州**y**                                                     |                                                              |
| aw                   | aʊ   | **ou**t                                                        | s**ou**                                                        | c**o**                                                     |                                                              |
| oy                   | ɔɪ   | **oi**l                                                        | j**oi**n                                                         | t**oy**                                                     |                                                              |
| y uw                 | ju   | **Yu**ma                                                       | h**u**man                                                        | f**新建**                                                     |                                                              |
| ax150                   | ə    | **开始**                                                        | wom**n**                                                        | 是                                                    |                                                              |
| R-彩色元音|      |                                                            |                                                              |                                                         |                                                              |
| ih r                 | ɪɹ   | **耳**                                                       | t**ir**amisu                                                     | n**耳**                                                    |                                                              |
| eh r                 | ɛɹ   | **飞机**                                                   | 应用**ar**ently                                                   | sc**ar**e                                                   |                                                              |
| 糟糕 r                 | ʊɹ   |                                                            |                                                              | c**你的**电子                                                    |                                                              |
| ay r                 | aɪɹ  | **Protokol**土地                                                    | f**ir**eplace                                                    | ch**oir**                                                   |                                                              |
| aw r                 | aʊɹ  | **小时**s                                                      | p**下限**rest-ful                                                     | s                                                    |                                                              |
| ao r                 | ɔɹ   | **或**a)                                                     | m**或**al                                                        | s**oar**                                                    |                                                              |
| aa r                 | ɑɹ   | **ar**tist                                                     | st**ar**t                                                        | c**ar**                                                     |                                                              |
| er r                 | ɝ    | **耳**第一                                                      | b**ir**d                                                         | f                                                     |                                                              |
| ax r                 | ɚ    |                                                            | 所有**er**gy                                                      | 禁止**er**                                                  |                                                              |
| SEMIVOWELS|      |                                                            |                                                              |                                                         |                                                              |
| w                    | w    | **w**i，s**ue**de                                                | al**w**ays                                                       |                                                         |                                                              |
| y                    | j    | **y**ard，f**e**w                                                  | **我**开启                                                        |                                                         |                                                              |
| ASPIRATED 口头停止 |      |                                                            |                                                              |                                                         |                                                              |
| p                    | p    | **p**                                                        | ha**pp**en                                                       | fla**p**                                                    |                                                              |
| b                    | b    | **b**ig                                                        | num**b**er                                                       | cra**b**                                                    |                                                              |
| t                    | t    | **t**alk                                                       | capi**t**al                                                      | sough**t**                                                  |                                                              |
| d                    | d    | **d**ig                                                        | 已运行**d**om                                                       | ro**d**                                                     |                                                              |
| k                    | k    | **c**                                                        | sla**ck**er                                                      | Ira**q**                                                    |                                                              |
| g                    | g    | **g**o                                                         | a**g**o                                                          | dra**g**                                                    |                                                              |
| NASAL 停止          |      |                                                            |                                                              |                                                         |                                                              |
| m                    | m    | **m**at，smash                                                 | ca**m**年代                                                       | 金塔**m**                                                    |                                                              |
| n                    | n    | **n**o，s**n**o                                                   | te**n**t                                                         | chicke**n**                                                 |                                                              |
| ng                   | ŋ    |                                                            | l**n**k                                                         | s                                                    |                                                              |
| FRICATIVES|      |                                                            |                                                              |                                                         |                                                              |
| f                    | f    | **f**o)                                                       | le**f**t                                                         | hal**f**                                                    |                                                              |
| v                    | v    | **v**alue                                                      | e**v**ent                                                        | lo**v**e                                                    |                                                              |
| th                   | θ    | **第**                                                       | empa**第**y 个                                                      | 周一**次**                                                   |                                                              |
| dh                   | –    | **第**en                                                       | mo**次**er                                                       | **第**smoo   |                                                              |
| s                    | s    | **s**it                                                        | ri**s**k                                                         | 事实**s**                                                   |                                                              |
| {1}z{2}                    | {1}z{2}    | **z**ap                                                        | bu**s**y                                                         | 儿童                                                    |                                                              |
| sh                   | ʃ    | **sh** e                                                        | abbrevia**ti**on                                                 | ru**sh**                                                    |                                                              |
| zh                   | ʒ    | **J**acques                                                    | 劝告**s**u)                                                     | gara**g**e                                                  |                                                              |
| h                    | h    | **h**elp                                                       | en**h**外观                                                      | a-**h**a！                                                   |                                                              |
| AFFRICATES           |      |                                                            |                                                              |                                                         |                                                              |
| 48                   | tʃ   | **ch**in                                                       | fu**t**u)                                                       | atta**ch**                                                  |                                                              |
| yey-jh-tfz                   | dʒ   | **j**oy                                                        | 原**g**inal                                                     | oran**g**e                                                  |                                                              |
| APPROXIMANTS|      |                                                            |                                                              |                                                         |                                                              |
| l                    | l    | **l**id，g**l**ad                                                  | pa**l**ace                                                       | chi**ll**                                                   |                                                              |
| r                    | ɹ    | **研发**，b**r**                                                 | bo**rr**o                                                       | ta**r**                                                     |                                                              |

## <a name="fr-fr"></a>fr-FR

| SAPI            | IPA                   | 示例1（开始对于辅音，为元音输入单词初始值） | 示例2（intervocalic for 辅音，word 词中核心） | 示例3（用于辅音的 coda，元音的单词 final） | comment                                                      |
| --------------- | --------------------- | -------------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------- | ------------------------------------------------------------ |
| SUPRASEGMENTALS |                       |                                                          |                                                              |                                                       |                                                              |
| 1               | ˈ（主要压力）    |                                                          |                                                              |                                                       | 在压力音节的元音后，SAPI 手机集施加压力 |
|                 | ˌ (substress)         |                                                          |                                                              |                                                       | 如果需要，fr-fr SAPI 手机集不支持表示 IPA substress "ˌ"，应直接使用 IPA |
| -               | 。 （音节边界） |                                                          |                                                              |                                                       |                                                              |
| 后面          |                       |                                                          |                                                              |                                                       |                                                              |
| （英文）。               | （英文）。                     | **rbre**                                                    | p**a**tte                                                        | ir**a**                                                   |                                                              |
| 后面              | ɑ                     |                                                          | p **-** te                                                         | p**a**s                                                   |                                                              |
| aa ~            | ɑ̃                     | **en**fant                                                   | enf**en**t                                                       | t**em**ps                                                 |                                                              |
| ax150              | ə                     |                                                          | p**e**tite                                                       | l**e**                                                    |                                                              |
| 吧              | ɛ                     | **e**lle                                                     | p**e**rdu                                                        | ét**ai**t                                                 |                                                              |
| 各              | ø                     | **œu**fs                                                     | cr**eu**ser                                                      | qu**eu**                                                  |                                                              |
| 3om-ey-2vk              | e                     | ému                                                      | crétin                                                       | ôté                                                   |                                                              |
| eh ~            | ɛ̃                     | **im**portant                                                | p**ein**ture                                                     | 材料                                                 |                                                              |
| url-iy-ung              | i                     | **我**dée                                                     | 宠物**i**te                                                       | 上午                                                   |                                                              |
| oe              | –                     | **œu**f                                                      | p**eu**r                                                         |                                                       |                                                              |
| 哦              | ɔ                     | **o**bstacle                                                 | c**o**rps                                                        |                                                       |                                                              |
| 哦 ~            | ɔ̃                     | **on**ze                                                     | deur**上的**r                                                      | b**打开**                                                   |                                                              |
| o              | o                     | **au**diteur                                                 | b**eau**coup                                                     | p**ô**                                                    |                                                              |
| oe ~            | œ̃                     | **&**                                                       | l**取消**di                                                        | b**卸载**                                                  |                                                              |
| uw              | u                     | **ou**trage                                                  | intr**ou**vable                                                  | **部门**                                                    |                                                              |
| uy              | y                     | **u**ne                                                      | p**u**nir                                                        | él**u**                                                   |                                                              |
| 辅音      |                       |                                                          |                                                              |                                                       |                                                              |
| b               | b                     | **b**ête                                                     | ha**b**ille                                                      | ro**b**e                                                  |                                                              |
| d               | d                     | **d**protokol                                                     | ron**d**eur                                                      | chau**d**e                                                |                                                              |
| f               | f                     | **f**emme                                                    | su**ff**ixe                                                      | bo**f**                                                   |                                                              |
| g               | g                     | **g**auche                                                   | é**g**ale                                                        | ba**gu**e                                                 |                                                              |
| ng              | ŋ                     |                                                          |                                                              | 停车场                                              | 仅适用于某些外文字词                                 |
| 1xt-hy-ubw              | ɥ                     | h**u**ile                                                    | n**u**protokol                                                        |                                                       |                                                              |
| k               | k                     | **c**arte                                                    | é**c**aille                                                      | 为**c**                                                   |                                                              |
| l               | l                     | **l**总监                                                     | é**l**protokol                                                        | ba**l**                                                   |                                                              |
| m               | m                     | **m**adame                                                   | ai**m**er                                                        | po**mm**e                                                 |                                                              |
| n               | n                     | **n**个 ou                                                     | te**n**ir                                                        | bo**nn**e                                                 |                                                              |
| nj              | ɲ                     |                                                          |                                                              | pei**gn**e                                                |                                                              |
| p               | p                     | **p**atte                                                    | 将**p**重为                                                        | ca**p**                                                   |                                                              |
| r               | ɹ                     | **r**at                                                      | cha**r**iot                                                      | senti**r**                                                |                                                              |
| s               | s                     | **s**ourir                                                   | **ss**ez                                                        | pa**ss**e                                                 |                                                              |
| sh              | ʃ                     | **ch**anter                                                  | ma**ch**"                                                      | po**ch**e                                                 |                                                              |
| t               | t                     | **t**ête                                                     | ô**t**er                                                         | ne**t**                                                   |                                                              |
| v               | v                     | **v**ent                                                     | 在**v**中输入                                                     | rê**v**e                                                  |                                                              |
| w               | w                     | **ou**i                                                      | f**ou**"                                                       |                                                       |                                                              |
| y               | j                     | **y**od                                                      | p**i**étiner                                                     | Marse**ille **                                            |                                                              |
| {1}z{2}               | {1}z{2}                     | \* * z * * éro                                                     | chiang**s**onner                                                    | ro**s**e                                                  |                                                              |
| zh              | ʒ                     | **j**ardin                                                   | man**g**er                                                       | piè**g**e                                                 |                                                              |
|                 | 北                    |                                                          |                                                              | u**n** arbre                                              | 如果需要，fr-fr SAPI 手机集不支持表示法语 liasion "n ‿"，应直接使用 IPA |
|                 | 关心                    |                                                          |                                                              | quan**d**                                                 | 如果需要，fr-fr SAPI 手机集不支持表示法语 liasion "t ‿"，应直接使用 IPA |
|                 | z                    |                                                          |                                                              | di**x**                                                   | 如果需要，fr-fr SAPI 手机集不支持表示法语 liasion "z ‿"，应直接使用 IPA |


## <a name="de-de"></a>de-DE

| SAPI   | IPA     | 示例1（开始对于辅音，为元音输入单词初始值）  | 示例2（intervocalic for 辅音，word 词中核心） | 示例3（用于辅音的 coda，元音的单词 final）     | 注释                                                      |
| --------------- | --------------------- | -------------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------- | ------------------------------------------------------------ |
| SUPRASEGMENTALS |         |                                                              |                                                              |                                                              |                                                              |
| 1               | ˈ       | anders/a **1** n d ax r s/                                | Multiplikationszeichen/m 噢 l t url-iy-ung-p l url-iy-ung-k a-ts y o **1** n s-ts ay-c n/ | Biologie/b url-iy-ung-o-l o-g url-iy-ung **1**/                     | 在压力音节的元音后，SAPI 手机集施加压力 |
| 2               | ˌ       | Allgemeinwissen/a **2** l-g ax-m ay 1 n-v ih-s n/   | Abfallentsorgungsfirma/a 1 p-f a l-^ eh n t-z，\r **2** ax r-g | Computertomographie/k 哦 m-p y uw 1-t ax r-t o-m o-g r a-f url-iy-ung **2**/ | Substressed 音节后，SAPI 手机集将压力置于元音后面 |
| -               | 。       |                                                              |                                                              |                                                              |                                                              |
| 后面          |         |                                                              |                                                              |                                                              |                                                              |
| 的             | ː      | **Ber**                                        | Maßst**a**b                             | 方案                                      |                                                              |
| （英文）。               | （英文）。       | **Bfall**                                        | B**a**ch                                               | Agath                                    |                                                              |
| 哦              | ɔ       | **O**sten                                          | Pf**o**sten                                     |                                                              |                                                              |
| 吧            | ɛː      | **Ä**hnlichkeit                      | B**ä**r                                           | 仅限外部源的单词，如： Fasci**ae**      |                                                              |
| 吧              | ɛ       | **ä**ndern                                    | Proz**e**nt                                | Amygdal**ae**                         |                                                              |
| ax150              | ə       | 仅限外部源中的单词（例如 ppointment 音节）的 intially，**最初为：** ' v**e**rstauen      | Aach**e**n                                       | Frag**e**                                      |                                                              |
| url-iy-ung              | 我ː      | **我**运行                                         | abb**ie**gt                                    | Relativitätstheor**ie**     |                                                              |
| ih              | ɪ       | **我**nnung                                       | s**i i**                                     | 木材**y**                                         |                                                              |
| 各              | øː      | **Ö**sen                                             | abl**ö**sten                               | Malm**ö**                                        |                                                              |
| o              | o，o ː   | **o**hne                                            | Balk**o**n                                     | Trept**o**                                   |                                                              |
| oe              | –       | **Ö**ffnung                                    | bef**ö**rdern                     |                                                              |                                                              |
| 3om-ey-2vk              | e、e ː   | **E**berhard                            | .abf**e**gt                                     | b                                                    |                                                              |
| uw              | u ː      | **U**                                             | H**u**t                                                | Akk**u**                                             |                                                              |
| 哦              | ʊ       | **U**nterschiedes              | b**u**nt                                             |                                                              |                                                              |
| ue-v              | y ː      | **Ü**bermut                              | default.pfl**ü**gt                                        | 男人**ü**                                          |                                                              |
| uy              | 误差       | **ü**ppig                                         | S**y**词干                                    |                                                              |                                                              |
| DIPHTHONG       |         |                                                              |                                                              |                                                              |                                                              |
| ay              | ai      | **ei**nsam                                     | Unabhängigk**ei**t      | Abt**ei**                                          |                                                              |
| aw              | au      | **au**ßen                                            | abb**au**st                                  | St**au**                                              |                                                              |
| oy              | ɔy, ɔʏ̯  | **欧盟**phorie                                 | tr**äu**mt                                         | sch-m**欧盟**                                               |                                                              |
| SEMIVOWEL       |         |                                                              |                                                              |                                                              |                                                              |
| ax r            | ɐ       |                                                              | abänd**er**n                          | 锁定**er**                                     |                                                              |
| 辅音       |         |                                                              |                                                              |                                                              |                                                              |
| b               | b       | **B**ank                                             |                                                              | ' 仅限外部源的字，如： Pu**b**     |                                                              |
| c               | ç       | **Ch**emie                                        | mögli**ch**st                             | i**ch**                                                  | "e" 和 "i" 后的软 "ch"                                 |
| d               | d       | **d**anken                                       | 仅限外原点的单词，如： Len**d**l      | 仅限外部源的单词，如： Clau**d**e      |                                                              |
| yey-jh-tfz              | ʤ       | **J**eff                                                | gemana**g**t                        | 仅限外部源的单词，如：更改**g**e      | 仅限外部源的字                             |
| f               | f       | **F**ahrtdauer                          | angri**ff**slustig         | abbruchrei**f**                      |                                                              |
| g               | g       | **g**                                                |                                                              | 仅限外部源中的字词（如 Gre**g** ）的 terminally     |                                                              |
| h               | h       | **H**ausanbau                           |                                                              |                                                              |                                                              |
| y               | j       | **J**od                                                | Reakt**i**on                           | hu**i**                                                |                                                              |
| k               | k       | **K**oma                                          | Aspe**k**t                                     | Flec**k**                                            |                                                              |
| l               | l       | **l**au                                                  | ähne**l**n                                       | zuvie**l**                                     |                                                              |
| m               | m       | **M**不到                                                | A**m**t                                                 | Leh**m**                                              |                                                              |
| n               | n       | **n**un                                                | u**n**d                                                | 是的**n**                                               |                                                              |
| ng              | ŋ       | 仅限外部源的单词，如： **Ng**uyen      | Schwa**nk**                                       | R**ing**                                              |                                                              |
| p               | p       | **P**artner                                | abru**p**t                                     | Ti**p**                                                |                                                              |
| pf              | pf      | **Pf**erd                                       | dam**pf**t                                         | 到**pf**                                              |                                                              |
| r               | ʀ、r、ʁ | **R**eise                                         | knu**rr**t                                      | Haa**r**                                              |                                                              |
| s               | s       |  仅限外部源的字词，例如： **S**taccato      | bi**s**t                                             | mie**s**                                               |                                                              |
| sh              | ʃ       | **Sch-m**u)                                      | mi**sch-m**t                                          | lappi**sch-m**                                    |                                                              |
| t               | t       | **T**raum                                            | S**t**raße                                  | Mu**t**                                                |                                                              |
| ts              | ts      | **Z**buj-ug-pkt                                               | Ar**z**t                                           | Wit**z**                                              |                                                              |
| 48              | tʃ      | **Tsch**echien                               | aufgepu**tsch**t                     | bundesdeu**tsch**                   |                                                              |
| v               | v       | **w**inken                                      | Q**u**alle                                       | 仅限外原点的单词，如：沟      |                                                              |
| x               | x、ç     | Word-最初仅在外部来源的单词中，例如： **J**uan 音节-最初还按如下所示的词（如 Ba**ch**erach  | Ma**ch**t mögli**ch**st  | 架构**ch** ' i**ch**             | IPA [x]：在所有非前面元音元音（a，aa，哦，o，唉，uw 和 diphthong aw）之后硬 "ch"。  IPA [ç]：前元音元音后的软 "ch" （ih，url-iy-ung，eh，ae，uy，ue，oe，eu 也在 diphthongs ay，oy）和辅音 |
| {1}z{2}               | {1}z{2}       | **s**uper                                       |                                                              |                                                              |                                                              |
| zh              | ʒ       | **G**enre                                      | B**重新**ezinski                      | Edvi**g**e                                     | 仅限外部源的字                             |
| 口头辅音  |         |                                                              |                                                              |                                                              |                                                              |
| ^               | ʔ       | beachtlich/b ax-^ a 1 x t-l ih c/      |                                                              |                                                              | 它表示喉塞音停止，详细信息，请单击[此处](http://en.wikipedia.org/wiki/Glottal_stop)。 我们需要在两个不同元音之间添加 [gs\] 电话，但两个元音是一个正版 diphthong。 |


## <a name="es-es"></a>es-ES

| SAPI            | IPA            | 示例1（开始对于辅音，为元音输入单词初始值） | 示例2（intervocalic for 辅音，word 词中核心） | 示例3（用于辅音的 coda，元音的单词 final） | comments                                                     |
| --------------- | -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| SUPRASEGMENTALS |                |                                                              |                                                              |                                                              |                                                              |
| -               | 。 音节   |                                                              |                                                              |                                                              |                                                              |
| 1               | ˈ (mainstress) |                                                              |                                                              |                                                              | 在压力音节的元音后，SAPI 手机集施加压力 |
| 2               | ˌ (substress)  |                                                              |                                                              |                                                              | Substressed 音节后，SAPI 手机集将压力置于元音后面 |
| 后面          |                |                                                              |                                                              |                                                              |                                                              |
| （英文）。               | （英文）。              | **lto**                          | c**a**ntar                           | ca**a**                                                        |                                                              |
| i               | i   | **我**bérica                                                      | av**i**spa                                                       | 税金**i**                                                         |                                                              |
| e               | e              | **e**lefante                                                     | at**e**                                                       | elefant**e**                                                     |                                                              |
| o               | o              | **o**caso                                                        | enc**o**ntrar                                                    | ocasenc**o**                                                        |                                                              |
| u               | u              | **u**sted                                                        | p**u**nta                                                        | Juanl**u**                                                       |                                                              |
| 辅音      |                |                                                              |                                                              |                                                              |                                                              |
| b               | b              | **b**aobab                                                       |                                                              | am**b**                                                          |                                                              |
|                 | β              |                                                              | bao**b**ab                                                       | baoba**b**                                                       | es SAPI 手机集不支持表示 IPA "β"，如果需要，应直接使用 IPA |
| 48              | tʃ             | **ch**eque                                                       | co**ch**e                                                        | Marraque**ch**                                                   |                                                              |
| d               | d              | **d**克瓦语                                                         |                                                              | portlan**d**                                                     |                                                              |
|                 | –              |                                                              | de**d**o                                                         | verda**d**                                                       | es SAPI 手机集不支持表示 IPA "-"，如果需要，应直接使用 IPA |
| f               | f              | **f**ácil                                                        | 元素**f**上午                                                     | pu**f**                                                          |                                                              |
| g               | g              | **g**潘                                                        |                                                              | dópin**g**                                                       |                                                              |
|                 | ɣ              |                                                              | **g**ua                                                         | tuare**g**                                                       | es SAPI 手机集不支持表示 IPA "ɣ"，如果需要，应直接使用 IPA |
| j               | j              | **我**odo                                                         | cal**i**ente                                                     | 重                                                          |                                                              |
| jj              | j j/jj         |                                                              | vi**ll**                                                        |                                                              |                                                              |
| k               | k              | **c**oche                                                        | bo**c**a                                                         | titáni**c**                                                      |                                                              |
| l               | l              | **l**ápiz                                                        | a**l**a                                                          | corde**l**                                                       |                                                              |
| ll              | ʎ              | **ll**                                                        | desarro**ll**o                                                   |                                                              |                                                              |
| m               | m              | **m**顺序                                                       | a**m**ar                                                         | álbu**m**                                                        |                                                              |
| n               | n              | **n**ada                                                         | ce**n**a                                                         | rató**n**                                                        |                                                              |
| nj              | ɲ              | **-** aña                                                         | ara **-** azo                                                      |                                                              |                                                              |
| p               | p              | **p**oca                                                         | 到**p**o                                                         | 停止**p**                                                         |                                                              |
| r               | ɾ              |                                                              | ca**r**a                                                         | abri**r**                                                        |                                                              |
| rr              | r              | **r**adio                                                        | 共同**rr**e                                                        | pu**rr**                                                         |                                                              |
| s               | s              | **s**简称 aco                                                         | va**s**o                                                         | pelo**s**                                                        |                                                              |
| t               | t              | **t**oldo                                                        | **t**ar                                                         | disque**t**                                                      |                                                              |
| th              | θ              | **z**ebra                                                        | a**z**ul                                                         | lápi**z**                                                        |                                                              |
| w               | w              | h**u**eso                                                        | ag**u**a                                                         | gua-eo-cwy**u**                                                         |                                         |
| x               | x              | **j**ota                                                         | **j**o                                                          | relo**j**                                                        |                                                              |


## <a name="zh-cn"></a>zh-CN

Zh-chs 的 TTS SAPI 手机集-CN 基于本机电话设置拼音。 对于本机发言人，建议使用 SAPI （拼音），这种情况很容易使用，而不是 IPA。 

**语气**

MS TTS zh-chs-CN SAPI 使用号码： 1 2 3 4 5 批注音调。

| 音调的拼音示例 | SAPI  | 字符示例 |
| ------------------------- | ----- | ------------------ |
| mā                        | ma 1 | 妈                 |
| má                        | ma 2 | 麻                 |
| mǎ                        | ma 3 | 马                 |
| mà                        | ma 4 | 骂                 |
| ma                        | ma 5 | 嘛                 |

**示例**

| SAPI 示例 |                               |
| ------------- | ----------------------------- |
| 字符     | SAPI                          |
| 组织关系      | zu 3-zhi 1-guan 1-xi 5 |
| 累进          | lei 3-金4                 |
| 西宅巷        | xi 1-zhai 2-xiang 4      |

## <a name="zh-tw"></a>zh-TW

适用于 zh-chs 的 SAPI 电话设置-幼圆基于本机手机集注音。 对于本机发言人，建议使用 "SAPI （注音）"，这种情况很容易使用，而不是 IPA。 

**语气**

| SAPI 音频 | 注音符号 | 示例（word） | SAPI 手机 | 注音   | 拼音（拼音） |
| ---------- | ------------- | ------------------ | ----------- | ---------- | -------------------- |
| ˉ          | empty         | 偵                 | ㄓㄣˉ       | ㄓㄣ       | zhēn                 |
| ˊ          | ˊ             | 察                 | ㄔㄚ﨩ˊ       | ㄔㄚ﨩ˊ      | chá                  |
| ˇ          | ˇ             | 打                 | ㄉㄚˇ       | ㄉㄚˇ      | dǎ                   |
| ˋ          | ˋ             | 望                 | ㄨㄤˋ       | ㄨㄤˋ      | wàng                 |
| ˙          | ˙             | 影子               | 一ㄥˇㄗ̇  | 一ㄥˇㄗ̇ | yǐng zi             |

**示例**

| SAPI 示例 |                |
| ------------- | -------------- |
| 字符     | SAPI           |
| 狗            | ㄍㄡˇ          |
| 然后          | ㄖㄢ﨩ˊㄏㄡˋ     |
| 剪掉          | ㄐㄧㄢˇㄉㄧㄠˋ |

## <a name="ja-jp"></a>ja-JP

Ja-jp 的 SAPI 电话设置基于日语本机手机集（假名）。 对于本机发言人，建议使用 SAPI （假名），这种情况很容易使用，而不是 IPA。 

**施加**

| 施加 |                 |
| ------ | --------------- |
| SAPI   | IPA             |
| ˈ      | ˈ (mainstress) |
| +      | ˌ (substress)  |

**示例**

| SAPI 示例 |              |             |
| ------------- | ------------ | ----------- |
| 字符     | SAPI         | IPA         |
| 合成          | ゴ'ウセ      | 中转ˈ wɯseji   |
| 所有者        | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化        | サィテキカ +  | sajitecikaˌ |