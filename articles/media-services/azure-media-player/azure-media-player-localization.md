---
title: Azure 媒体播放器本地化
description: 对非英语区域设置的用户提供多种语言支持。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727172"
---
# <a name="localization"></a>本地化 #

多种语言支持允许非英语区域设置的用户与玩家进行本机交互。 Azure 媒体播放器将实例化为通用语言词典，该词典将根据页面语言本地化错误消息。 开发人员还可以使用强制集语言创建播放器实例。 默认语言为英语（en）。

> [!NOTE]
> 此功能仍在经过一些测试，因此会受到错误的影响。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure 媒体播放器当前支持具有相应语言代码的以下语言：

| 语言            | 代码 | 语言                | 代码   | 语言                | 代码         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| 英语 [默认]   | en   | 克罗地亚语                | hr     | 罗马尼亚语                | ro           |
| 阿拉伯语              | ar   | 匈牙利语               | hu     | 斯洛伐克语                  | sk           |
| 保加利亚语           | bg   | 印度尼西亚语              | id     | 斯洛文尼亚                 | sl           |
| 加泰罗尼亚语             | ca   | 冰岛语               | is     | 塞尔维亚语 - 西里尔      | sr-cyrl-cs   |
| 捷克语               | cs   | 意大利语                 | it     | 塞尔维亚语 - 拉丁         | sr-latn-rs   |
| 丹麦语              | da   | 日语                | ja     | 俄语                 | ru           |
| 德语              | de   | 哈萨克语                  | kk     | 瑞典语                 | sv           |
| 希腊语               | el   | 朝鲜语                  | ko     | 泰语                    | th           |
| 西班牙语             | es   | 立陶宛语              | lt     | 他加禄语                 | Tl           |
| 爱沙尼亚语            | et   | 拉脱维亚语                 | lv     | 土耳其语                 | tr           |
| 巴斯克语              | eu   | 马来西亚               | ms     | 乌克兰语               | uk           |
| 波斯语               | 发   | 挪威语 - 博克°l     | nb     | 乌尔都语                    | 你           |
| 芬兰语             | fi   | 荷兰语                   | nl     | 越南语              | vi           |
| 法语              | fr   | 挪威语 - 尼诺斯克语     | nn     | 中文 - 简体字    | zh-hans      |
| 加利西亚语            | gl   | 波兰语                  | pl     | 中文 - 传统   | zh-hant      |
| 希伯来语              | he   | 葡萄牙语 - 巴西     | pt-br  |                         |              |
| Hindi               | hu   | 葡萄牙语 - 葡萄牙   | pt-pt  |                         |              |


> [!NOTE]
> 如果您不希望进行任何本地化，则必须强制该语言为英语

## <a name="next-steps"></a>后续步骤 ##

- [Azure 媒体播放器快速入门](azure-media-player-quickstart.md)