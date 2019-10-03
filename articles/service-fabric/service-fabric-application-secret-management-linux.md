---
title: 在 Azure Service Fabric Linux 群集上设置加密证书并对机密进行加密 | Microsoft Docs
description: 了解如何在 Linux 群集上设置加密证书并对机密进行加密。
services: service-fabric
documentationcenter: .net
author: shsha
manager: ''
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: 9589d6ea69a2293d592a9e63f2b726f1a620bb9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126981"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>在 Linux 群集上设置加密证书并对机密进行加密
本文展示了如何在 Linux 群集上设置加密证书并使用它来加密机密。 对于 Windows 群集，请参阅[在 Windows 群集上设置加密证书并对机密进行加密][secret-management-windows-specific-link]。

## <a name="obtain-a-data-encipherment-certificate"></a>获取数据加密证书
数据加密证书专门用来对服务 Settings.xml 中的[参数][parameters-link]以及服务 ServiceManifest.xml 中的[环境变量][environment-variables-link]进行加密和解密。 它不用于密码文本的身份验证或签名。 该证书必须满足以下要求：

* 证书必须包含私钥。
* 证书密钥用途必须包括数据加密 (10)，不应包括服务器身份验证或客户端身份验证。

  例如，可以通过以下命令使用 OpenSSL 来生成所需的证书：
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>在群集中安装证书
必须在群集中每个节点上的 `/var/lib/sfcerts` 下安装此证书。 用来运行该服务的用户帐户（默认情况下为 sfuser）对已安装的证书（对于当前示例为 `/var/lib/sfcerts/TestCert.pem`）**应当具有读取访问权限**。

## <a name="encrypt-secrets"></a>加密机密
以下代码片段可用来加密机密。 此代码片段仅对值进行加密；**不**对密码文本进行签名。 若要生成机密值的密文，**必须使用**群集中安装的同一个加密证书。

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
所生成的输出到 encrypted.txt 中的 base-64 编码字符串包含机密密文，以及用来将其加密的证书相关信息。 可以通过使用 OpenSSL 将其解密来验证其有效性。
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>后续步骤
了解如何[在应用程序中指定加密的机密。][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
