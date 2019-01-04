---
title: 在防火墙后面翻译 - 文本翻译 API
titlesuffix: Azure Cognitive Services
description: 使用文本翻译 API 在防火墙后面翻译。
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: bf1a63d996a1ed2021654c96576eb9dac2cff8e6
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52683330"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>如何使用文本翻译 API 在防火墙后面翻译

文本翻译 API 可以使用域名或 IP 筛选在防火墙后面翻译。 域名筛选是首选方法。 我们建议不要在经过 IP 筛选的防火墙后面运行 Microsoft Translator。 安装程序在将来可能会发生中断，恕不另行通知。 

## <a name="translator-ip-addresses"></a>Translator IP 地址
自 2018 年 11 月 20 日起，api.cognitive.microsofttranslator.com - Microsoft 文本翻译 API 的 IP 地址：

* **亚太区：** 40.90.139.163, 104.44.89.44
* **欧洲：** 40.90.138.4, 40.90.141.99
* **北美：** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [通过 Translator API 调用在防火墙后面翻译](reference/v3-0-translate.md)