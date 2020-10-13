---
title: 密钥保管库 .NET 2.x API 发行说明 | Microsoft Docs
description: 了解如何更新为早期版本的 Azure Key Vault 编写的应用，以便使用适用于 C# 和 .NET 的 Azure Key Vault 库的 2.0 版本。
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 018570019b306dced76760fefa4441ee7d86ad2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88189833"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure 密钥保管库 .NET 2.0 - 发行说明和迁移指南
以下信息有助于迁移到 C# 和 .NET 的 Azure Key Vault 库版本 2.0。  针对早期版本所编写的应用需进行更新，以支持最新版本。  为完全支持新增和改进的功能（如 Key Vault 证书），这些更改是必需的  。

## <a name="key-vault-certificates"></a>Key Vault 证书

Key Vault 证书管理 x509 证书，并支持以下行为：  

* 通过 Key Vault 创建过程创建证书，或导入现有证书。 这包括自签名证书和证书颁发机构 (CA) 生成的证书。
* 安全地存储和管理 x509 证书存储，无需使用私钥材料进行交互。  
* 定义指导 Key Vault 管理证书生命周期的策略。  
* 为生命周期事件提供联系信息，如过期警告和续订通知。  
* 使用选定的证书颁发者（Key Vault 合作伙伴 X509 证书提供者和证书颁发机构）自动续订证书。* 支持来自备用（非合作伙伴）提供者和证书颁发机构的支持证书（不支持自动续订）。  

## <a name="net-support"></a>.NET 支持

* Azure Key Vault .NET 库 2.0 版不支持 **.NET 4.0**
* Azure Key Vault .NET 库 2.0 版支持 **.NET Framework 4.5.2**
* Azure Key Vault .NET 库 2.0 版支持 **.NET Standard 1.4**

## <a name="namespaces"></a>命名空间

* **模型**的命名空间从 **Microsoft.Azure.KeyVault** 更改为 **Microsoft.Azure.KeyVault.Models**。
* **Microsoft.Azure.KeyVault.Internal** 命名空间被弃用。
* 以下 Azure SDK 依赖项命名空间的更改 

    - Hyak.Common 现为 Microsoft.Rest   。
    - Hyak.Common.Internals 现为 Microsoft.Rest.Serialization   。

## <a name="type-changes"></a>类型更改

* *Secret* 更改为 *SecretBundle*
* *Dictionary* 更改为 *IDictionary*
* *List\<T>、字符串 []* 更改为 *IList\<T>*
* *NextList* 更改为 *NextPageLink*

## <a name="return-types"></a>返回类型

* KeyList 和 SecretList 将返回 IPage\<T> 而不是 ListKeysResponseMessage************
* 生成的 BackupKeyAsync 将返回 BackupKeyResult，其中包含“值”（备份 blob）    。 以前，此方法会包装且仅返回值。

## <a name="exceptions"></a>异常

* *KeyVaultClientException* 更改为 *KeyVaultErrorException*
* 服务错误从 exception.Error 更改为 exception.Body.Error.Message   。
* 从 **[JsonExtensionData]** 的错误消息中删除了其他信息。

## <a name="constructors"></a>构造函数

* 构造函数不接受 *HttpClient* 作为构造函数参数，只接受 *HttpClientHandler* 或 *DelegatingHandler[]* 。

## <a name="downloaded-packages"></a>下载的包

客户端在处理 Key Vault 依赖项时，将下载以下包：

### <a name="previous-package-list"></a>以前的包列表

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>当前的包列表

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>类更改

* 已删除 UnixEpoch 类  。
* Base64UrlConverter 类重命名为 Base64UrlJsonConverter   。

## <a name="other-changes"></a>其他更改

* 在此版本的 API 中，添加了针对暂时性故障配置 KV 操作重试策略的支持。

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* 对于返回 vault 的操作，返回类型是包含 Vault 属性的类   。 返回类型现在为 *Vault*。
* *PermissionsToKeys* 和 *PermissionsToSecrets* 现在是 *Permissions.Keys* 和 *Permissions.Secrets*
* 某些返回类型的更改也适用于控制面板。

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* 该包已分解为 **Microsoft.Azure.KeyVault.Extensions** 和用于加密操作的 **Microsoft.Azure.KeyVault.Cryptography**。

