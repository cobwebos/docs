---
title: 如何为 Linux 配置 OpenSSL
titleSuffix: Azure Cognitive Services
description: 了解如何为 Linux 配置 OpenSSL。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331138"
---
# <a name="configure-openssl-for-linux"></a>为 Linux 配置 OpenSSL

使用 1.9.0 之前的任何语音 SDK 版本时，会将 [OpenSSL](https://www.openssl.org) 动态配置为主机系统版本。 在更高版本的语音 SDK 中，OpenSSL（版本为 [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)）会静态链接到语音 SDK 的核心库。

为确保连接，请验证系统中是否安装了 OpenSSL 证书。 运行命令：
```bash
openssl version -d
```

基于 Ubuntu/Debian 的系统上的输出应为：
```
OPENSSLDIR: "/usr/lib/ssl"
```

检查 OPENSSLDIR 下是否有`certs`子目录。 在上面的示例中，它将是`/usr/lib/ssl/certs`。

* 如果有`/usr/lib/ssl/certs`并且它包含许多单独的证书文件（带有`.crt`或`.pem`扩展名），则无需进一步操作。

* 如果 OPENSSLDIR 是和`/usr/lib/ssl`/或存在单个证书捆绑文件而不是多个单个文件，则需要设置适当的 SSL 环境变量以指示在何处可以找到证书。

## <a name="examples"></a>示例

- OPENSSLDIR`/opt/ssl`是 。 有`certs`具有许多`.crt`或`.pem`文件的子目录。
在运行使用`SSL_CERT_DIR`语音 SDK`/opt/ssl/certs`的程序之前，将环境变量设置为指向。 例如：
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR`/etc/pki/tls`是（如在基于 RHEL/CentOS 的系统上）。 有带有`certs`证书捆绑文件的子目录，例如`ca-bundle.crt`。
在运行使用`SSL_CERT_FILE`语音 SDK 的程序之前，将环境变量设置为指向该文件。 例如：
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [关于语音 SDK](speech-sdk.md)
