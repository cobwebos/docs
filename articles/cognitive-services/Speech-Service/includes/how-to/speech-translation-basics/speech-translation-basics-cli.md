---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: f28dd3e94db9d16645bf0d63841a17ee66defd7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776642"
---
语音服务的核心功能之一是能够识别人类语音并将其翻译成其他语言。 本快速入门介绍如何在应用和产品中使用语音 SDK 来执行高质量的语音翻译。 此快速入门将麦克风中的语音转换为另一种语言的文本。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../get-started.md)。

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>设置源和目标语言

此命令调用语音 CLI，将语音从意大利语转换为法语。

```shell
 spx translate --microphone --source it-IT --target fr
```
