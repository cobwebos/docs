---
title: "密钥保管库.NET 2.x API 发行说明 |Microsoft 文档"
description: ".NET 开发人员将使用此代码的 API 来获取 Azure 密钥保管库"
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: bruceper
ms.openlocfilehash: c5b5fd7f16faf17d16ecc82269fb1264adf4dd06
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure 密钥保管库.NET 2.0-发行说明和迁移指南
以下说明和指南适用于开发人员使用 Azure 密钥保管库.NET / C# 库。 在 1.0 版过渡到 2.0 版中，大量的更新进行该需要代码中你能够受益的功能改进和功能添加如顺序的迁移工作**密钥保管库证书**支持。

## <a name="key-vault-certificates"></a>密钥保管库证书

密钥保管库证书支持都提供用于管理你的 x509 证书和以下行为：  

* 允许是证书所有者才能创建通过密钥保管库创建过程或现有的证书导入的证书。 这包括自签名和证书颁发机构生成证书。
* 允许的密钥保管库证书所有者来实现安全地存储和管理的 X509 证书而无需使用私钥材料的交互。  
* 允许是证书所有者才能创建策略，将定向密钥保管库管理证书的生命周期。  
* 允许证书所有者提供联系信息通知有关生命周期事件的过期和续订证书。  
* 使用所选的颁发者的密钥保管库合作伙伴 X509 支持自动续订证书提供程序 / 证书颁发机构。
  
  * 注意-非时合作提供程序/机构也允许使用属性，但将不支持在自动续订功能。

## <a name="net-support"></a>.NET 支持

* **.NET 4.0**不受 Azure 密钥保管库.NET 2.0 版 / C# 库
* **.NET 核心**受 Azure 密钥保管库.NET 2.0 版 / C# 库

## <a name="namespaces"></a>命名空间

* 命名空间**模型**从更改**Microsoft.Azure.KeyVault**到**Microsoft.Azure.KeyVault.Models**。
* **Microsoft.Azure.KeyVault.Internal**删除命名空间。
* Azure SDK 依赖项命名空间更改从**Hyak.Common**和**Hyak.Common.Internals**到**Microsoft.Rest**和**Microsoft.Rest.Serialization**

## <a name="type-changes"></a>类型更改

* *机密*更改为*SecretBundle*
* *字典*更改为*IDictionary*
* *列表<T>，string []*更改为*IList<T>*
* *NextList*更改为*NextPageLink*

## <a name="return-types"></a>返回类型

* **KeyList**和**SecretList**将返回*IPage<T>* 而不是*ListKeysResponseMessage*
* 生成**BackupKeyAsync**将返回*BackupKeyResult*其中包含*值*(备份 blob)。 该方法已包装之前并返回的值。

## <a name="exceptions"></a>异常

* *KeyVaultClientException*更改为*KeyVaultErrorException*
* 从更改服务错误*异常。错误*到*异常。Body.Error.Message*。
* 从错误消息以删除其他信息**[JsonExtensionData]**。

## <a name="constructors"></a>构造函数

* 而不是接受*HttpClient*作为构造函数自变量，构造函数只接受*HttpClientHandler*或*DelegatingHandler []*。

## <a name="downloaded-packages"></a>下载的包

当客户端处理依赖于密钥保管库以下已下载

### <a name="previous-package-list"></a>前面的包列表

* 打包 id="Hyak.Common"版本 ="1.0.2"targetFramework ="net45"
* 打包 id="Microsoft.Azure.Common"版本 ="2.0.4"targetFramework ="net45"
* 打包 id="Microsoft.Azure.Common.Dependencies"版本 ="1.0.0"targetFramework ="net45"
* 打包 id="Microsoft.Azure.KeyVault"版本 ="1.0.0"targetFramework ="net45"
* 打包 id="Microsoft.Bcl"版本 ="1.1.9"targetFramework ="net45"
* 打包 id="Microsoft.Bcl.Async"版本 ="1.0.168"targetFramework ="net45"
* 打包 id="Microsoft.Bcl.Build"版本 ="1.0.14"targetFramework ="net45"
* 打包 id="Microsoft.Net.Http"版本 ="2.2.22"targetFramework ="net45"

### <a name="current-package-list"></a>当前的包列表

* 打包 id="Microsoft.Azure.KeyVault"版本 ="2.0.0-preview"targetFramework ="net45"
* 打包 id="Microsoft.Rest.ClientRuntime"版本 ="2.2.0"targetFramework ="net45"
* 打包 id="Microsoft.Rest.ClientRuntime.Azure"版本 ="3.2.0"targetFramework ="net45"

## <a name="class-changes"></a>类更改

* **UnixEpoch**类已删除
* **Base64UrlConverter**类已重命名为**Base64UrlJsonConverter**

## <a name="other-changes"></a>其他更改

* 支持 KV 上暂时性故障的操作重试策略配置已添加到此版本的 API。

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* 操作，返回*保管库*，返回类型已包含保管库属性的类。 返回类型是现在*保管库*。
* *PermissionsToKeys*和*PermissionsToSecrets*现在*Permissions.Keys*和*Permissions.Secrets*
* 某些返回类型更改应用于控件的平面以及。

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* 包已损坏达**Microsoft.Azure.KeyVault.Extensions**和**Microsoft.Azure.KeyVault.Cryptography**为加密操作。

