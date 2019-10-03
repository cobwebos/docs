---
title: 在防火墙后面翻译 - 文本翻译 API
titleSuffix: Azure Cognitive Services
description: 使用文本翻译 API 在防火墙后面翻译。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 3d5c775d24c89d126962b6c4bccb4d5a572801ac
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906765"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>如何使用文本翻译 API 在防火墙后面翻译

文本翻译 API 可以使用域名或 IP 筛选在防火墙后面翻译。 域名筛选是首选方法。 我们建议不要在经过 IP 筛选的防火墙后面运行 Microsoft Translator。 安装程序在将来可能会发生中断，恕不另行通知。

## <a name="translator-ip-addresses"></a>Translator IP 地址
2019年8月21日文本翻译 API 的 IP 地址-Microsoft:

* **亚太区：** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **欧洲：** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **北美：** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [通过 Translator API 调用在防火墙后面翻译](reference/v3-0-translate.md)
