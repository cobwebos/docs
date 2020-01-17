---
title: 如何为 Linux 配置 OpenSSL
titleSuffix: Azure Cognitive Services
description: 了解如何配置适用于 Linux 的 OpenSSL。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156482"
---
# <a name="configure-openssl-for-linux"></a>为 Linux 配置 OpenSSL

使用1.9.0 之前的任何语音 SDK 版本时， [OpenSSL](https://www.openssl.org)会动态配置为主机系统版本。 在更高版本的语音 SDK 中，OpenSSL （版本[1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)）静态链接到语音 sdk 的核心库。

## <a name="troubleshoot-connectivity"></a>排查连接问题

如果使用 Speech SDK 的1.9.0 版本时出现连接失败，请确保 `ssl/certs` 目录存在于 `/usr/lib` directory 中-在 Linux 文件系统中。 如果 `ssl/certs` 目录*不存在*，请使用以下命令检查系统中安装 OpenSSL 的位置：

```bash
which openssl
```

然后，找到 OpenSSL `certs` 目录，并将该目录的内容复制到 `/usr/lib/ssl/certs` directory 中。 接下来，请重试，查看是否已解决连接问题。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [关于语音 SDK](speech-sdk.md)