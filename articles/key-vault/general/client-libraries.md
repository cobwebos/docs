---
title: Azure Key Vault 的客户端库 | Microsoft Docs
description: Azure Key Vault 的客户端库
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 2c48917e7205bca03380267c1c97469acc245fa6
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628021"
---
# <a name="client-libraries-for-azure-key-vault"></a>Azure Key Vault 的客户端库

Azure 密钥保管库的客户端库允许以编程方式通过各种语言（包括 .NET、Python、Java 和 JavaScript）访问密钥保管库功能。

## <a name="client-libraries-per-language-and-object"></a>每种语言和每个对象的客户端库

每个 SDK 都有单独的用于密钥保管库、机密、密钥和证书的客户端库，如下表所示。

| 语言 | 机密 | 键 | 证书 | 密钥保管库（管理平面）
|--|--|--|--|
| .NET | - [API 参考](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [NuGet 包](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [快速入门](../secrets/quick-create-net.md) | - [API 参考](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [NuGet 包](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [API 参考](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [NuGet 包](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) | - [API 参考](/dotnet/api/microsoft.azure.management.keyvault?view=azure-dotnet)<br>- [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [API 参考](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [PyPi 包](https://pypi.org/project/azure-keyvault-secrets/)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [快速入门](../secrets/quick-create-python.md) |- [API 参考](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [PyPi 包](https://pypi.org/project/azure-keyvault-keys/)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [快速入门](../keys/quick-create-python.md) | - [API 参考](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [PyPi 包](https://pypi.org/project/azure-keyvault-certificates/)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [快速入门](../certificates/quick-create-python.md) | - [API 参考](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault?view=azure-python)<br> - [PyPi 包](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [API 参考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [快速入门](../secrets/quick-create-java.md) |- [API 参考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [API 参考](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |- [API 参考](/java/api/com.microsoft.azure.management.keyvault?view=azure-java-stable)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [API 参考](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [npm 包](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [快速入门](../secrets/quick-create-node.md) |- [API 参考](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [npm 包](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [API 参考](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [npm 包](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |  - [API 参考](/javascript/api/@azure/arm-keyvault/?view=azure-node-latest)<br>- [npm 包](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure Key Vault 开发人员指南](developers-guide.md)
- 了解有关[向密钥保管库进行身份验证](authentication.md)的详细信息
- 了解有关[保护对密钥保管库的访问](secure-your-key-vault.md)的详细信息
