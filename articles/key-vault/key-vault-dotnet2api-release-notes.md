---
title: "密钥保管库 .NET 2.x API 发行说明 | Microsoft Docs"
description: ".NET 开发人员将使用此 API 对 Azure 密钥保管库进行编码"
services: key-vault
documentationcenter: 
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/07/2017
ms.author: bruceper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d4dc8dea5c6e371db29fb9f85cfa635802cc0c52


---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure 密钥保管库 .NET 2.0 - 发行说明和迁移指南
下面的注意事项和指南适用于使用 Azure 密钥保管库 .NET / C# 库的开发人员。 在从 1.0 版到 2.0 版的变化中，进行了大量需要代码迁移的更新，使你可以受益于功能的改进和新增，例如密钥保管库证书支持。

密钥保管库证书支持提供 x509 证书的管理和下列行为：  

* 允许证书所有者通过密钥保管库创建过程或通过导入现有证书来创建证书。 这包括自签名证书和证书颁发机构生成的证书。
* 允许密钥保管库证书所有者实现 X509 证书的安全存储和管理，无需与私钥材料交互。  
* 允许证书所有者创建一个策略，指示密钥保管库来管理证书的生命周期。  
* 允许证书所有者为有关证书的过期和续订生命周期事件的通知提供联系信息。  
* 支持向所选的颁发者（密钥保管库合作伙伴 X509 的证书提供者/证书颁发机构）进行自动续订。
  
  * 注意 - 也允许使用非合作伙伴提供者/颁发机构，但将不支持自动续订功能。

## <a name="net-support"></a>.NET 支持
* Azure 密钥保管库 .NET/C# 库 2.0 版不支持 **.NET 4.0**
* Azure 密钥保管库 .NET/C# 库 2.0 版支持 **.NET Core**

## <a name="namespaces"></a>命名空间
* **模型**的命名空间从 **Microsoft.Azure.KeyVault** 更改为 **Microsoft.Azure.KeyVault.Models**。
* 已废除 **Microsoft.Azure.KeyVault.Internal** 命名空间。
* Azure SDK 依赖项命名空间从 **Hyak.Common** 和 **Hyak.Common.Internals** 更改为 **Microsoft.Rest** 和 **Microsoft.Rest.Serialization**

## <a name="type-changes"></a>类型更改
* *Secret* 更改为 *SecretBundle*
* *Dictionary* 更改为 *IDictionary*
* *List<T>、字符串 []* 更改为 *IList<T>*
* *NextList* 更改为 *NextPageLink*

## <a name="return-types"></a>返回类型
* **KeyList** 和 **SecretList** 将返回 *IPage<T>* 而不是 *ListKeysResponseMessage*
* 生成的 **BackupKeyAsync** 将返回 *BackupKeyResult*，其中包含 *值*（备份 blob）。 在该方法包装之前，仅返回值。

## <a name="exceptions"></a>异常
* *KeyVaultClientException* 更改为 *KeyVaultErrorException*
* 服务错误从 *exception.Error* 更改为 *exception.Body.Error.Message*。
* 从 **[JsonExtensionData]** 的错误消息中删除其他信息。

## <a name="constructors"></a>构造函数
* 构造函数不接受 *HttpClient* 作为构造函数参数，只接受 *HttpClientHandler* 或 *DelegatingHandler []*。

## <a name="downloaded-packages"></a>下载的包
当客户端处理密钥保管库的依赖项时，会已下载以下内容

#### <a name="previous-package-list"></a>以前的包列表
* 包 id="Hyak.Common"，版本="1.0.2"，targetFramework="net45"
* 包 id="Microsoft.Azure.Common"，版本="2.0.4"，targetFramework="net45"
* 包 id="Microsoft.Azure.Common.Dependencies"，版本="1.0.0"，targetFramework="net45"
* 包 id="Microsoft.Azure.KeyVault"，版本="1.0.0"，targetFramework="net45"
* 包 id="Microsoft.Bcl"，版本="1.1.9"，targetFramework="net45"
* 包 id="Microsoft.Bcl.Async"，版本="1.0.168"，targetFramework="net45"
* 包 id="Microsoft.Bcl.Build"，版本="1.0.14"，targetFramework="net45"
* 包 id="Microsoft.Net.Http"，版本="2.2.22"，targetFramework="net45"

#### <a name="current-package-list"></a>当前的包列表
* 包 id="Microsoft.Azure.KeyVault"，版本="2.0.0-preview"，targetFramework="net45"
* 包 id="Microsoft.Rest.ClientRuntime"，版本="2.2.0"，targetFramework="net45"
* 包 id="Microsoft.Rest.ClientRuntime.Azure"，版本="3.2.0"，targetFramework="net45"

## <a name="class-changes"></a>类的更改
* 已删除 **UnixEpoch** 类
* **Base64UrlConverter** 类重命名为 **Base64UrlJsonConverter**

## <a name="other-changes"></a>其他更改
* 此版本的 API 中添加了对暂时性故障的 KV 操作重试策略配置的支持。

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet
* 对于返回 *vault* 的操作，返回类型是包含 Vault 属性的类。 返回类型现在是 *Vault*。
* *PermissionsToKeys* 和 *PermissionsToSecrets* 现在是 *Permissions.Keys* 和 *Permissions.Secrets*
* 某些返回类型的更改也适用于控制面板。

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet
* 包分为 **Microsoft.Azure.KeyVault.Extensions** 和 **Microsoft.Azure.KeyVault.Cryptography** 以便执行加密操作。




<!--HONumber=Nov16_HO3-->


