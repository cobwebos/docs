---
title: Azure Media Player 本地化
description: 对于非英语区域设置的用户，支持多种语言。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ca4dc888af414ede270118eff72652f098d3306c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779036"
---
# <a name="localization"></a>本地化 #

多语言支持允许非英语区域设置的用户以本机方式与播放机交互。 Azure Media Player 将使用一种语言的全局字典来实例化，这将基于页面语言本地化错误消息。 开发人员还可以使用强制集语言创建播放机实例。 默认语言为英语（en）。

> [!NOTE]
> 此功能仍会经历一些测试，因此会受到 bug 的限制。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player 当前支持以下语言及其相应的语言代码：

| 语言            | 代码 | 语言                | 代码   | 语言                | 代码         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| 英语 {默认}   | en   | 克罗地亚语                | hr     | 罗马尼亚语                | ro           |
| 阿拉伯语              | ar   | 匈牙利语               | hu     | 斯洛伐克语                  | sk           |
| 保加利亚语           | bg   | 印度尼西亚语              | id     | Slovene                 | sl           |
| 加泰罗尼亚语             | ca   | 冰岛语               | 是     | 塞尔维亚语 - 西里尔      | cyrl-cs   |
| 捷克语               | cs   | 意大利语                 | it     | 塞尔维亚语 - 拉丁         | sr-latn-rs   |
| 丹麦语              | da   | 日语                | ja     | 俄语                 | ru           |
| 德语              | de   | 哈萨克语                  | kk     | 瑞典语                 | sv           |
| 希腊语               | el   | 朝鲜语                  | ko     | 泰语                    | th           |
| 西班牙语             | es   | 立陶宛语              | lt     | 他加禄语                 | tl           |
| 爱沙尼亚语            | et   | 拉脱维亚语                 | lv     | 土耳其语                 | tr           |
| 巴斯克语              | eu   | 马来西亚               | ms     | 乌克兰语               | uk           |
| 波斯语               | fa   | 挪威语-BokmÃ¥     | nb     | 乌尔都语                    | ur           |
| 芬兰语             | fi   | 荷兰语                   | nl     | 越南语              | vi           |
| 法语              | fr   | 挪威语 - 尼诺斯克语     | nn     | 中文-简体    | zh-hans      |
| 加利西亚语            | gl   | 波兰语                  | pl     | 中文-繁体   | zh-hant      |
| 希伯来语              | he   | 葡萄牙语 - 巴西     | pt-br  |                         |              |
| Hindi               | hi   | 葡萄牙语 - 葡萄牙   | pt-pt  |                         |              |


> [!NOTE]
> 如果不想进行任何本地化，则必须将语言强制为英语

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
