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
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683165"
---
# <a name="configure-openssl-for-linux"></a>为 Linux 配置 OpenSSL

使用 1.9.0 之前的任何语音 SDK 版本时，会将 [OpenSSL](https://www.openssl.org) 动态配置为主机系统版本。 在更高版本的语音 SDK 中，OpenSSL（版本为 [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)）会静态链接到语音 SDK 的核心库。

若要确保连接性，请验证是否在系统中安装了 OpenSSL 证书。 运行命令：
```bash
openssl version -d
```

基于 Ubuntu/Debian 的系统上的输出应为：
```
OPENSSLDIR: "/usr/lib/ssl"
```

检查 OPENSSLDIR 下是否存在 `certs` 子目录。 在上面的示例中，它会是 `/usr/lib/ssl/certs`。

* 如果有 `/usr/lib/ssl/certs` 并且它包含多个单独的证书文件（扩展名为 `.crt` 或 `.pem`），则无需执行进一步的操作。

* 如果 OPENSSLDIR 不是 `/usr/lib/ssl`，并且/或者存在单个证书绑定文件而不是多个单独的文件，则需设置相应的 SSL 环境变量来指示可在何处找到证书。

## <a name="examples"></a>示例

- OPENSSLDIR 为 `/opt/ssl`。 存在 `certs` 子目录，其中包含许多 `.crt` 或 `.pem` 文件。
在运行使用语音 SDK 的程序之前，请将环境变量 `SSL_CERT_DIR` 设置为指向 `/opt/ssl/certs`。 例如：
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR 是 `/etc/pki/tls`（类似于基于 RHEL/CentOS 的系统）。 存在 `certs` 子目录，其中包含证书绑定文件（例如 `ca-bundle.crt`）。
在运行使用语音 SDK 的程序之前，请将环境变量 `SSL_CERT_FILE` 设置为指向该文件。 例如：
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> 还值得注意的是，Linux 的某些发行版没有定义 TMP 或 TMPDIR 环境变量。 这将导致语音 SDK 每次下载证书吊销列表 （CRL），而不是将 CRL 缓存到磁盘以进行重用，直到过期。 为了提高初始连接性能[，您可以创建名为 TMPDIR 的环境变量并将其设置为所选临时目录的路径。 .](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [关于语音 SDK](speech-sdk.md)
