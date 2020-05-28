---
title: 在防火墙后转换-翻译人员
titleSuffix: Azure Cognitive Services
description: Azure 认知服务转换器可以使用域名或 IP 筛选功能在防火墙后进行转换。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 78a53c99f5f184c1b6b45d59d86c23efb898d7dc
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996952"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>如何在 IP 防火墙后面翻译转换器

转换器可以使用域名或 IP 筛选功能在防火墙后进行转换。 域名筛选是首选方法。 我们建议不要  在经过 IP 筛选的防火墙后面运行 Microsoft Translator。 安装程序在将来可能会发生中断，恕不另行通知。

## <a name="translator-ip-addresses"></a>Translator IP 地址
2019年8月21日的 api.cognitive.microsofttranslator.com-转换器的 IP 地址：

* **亚太区：** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **欧洲：** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **北美：** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [在转换器中的 IP 防火墙后面翻译](reference/v3-0-translate.md)
