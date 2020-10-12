---
title: azcopy login | Microsoft Docs
description: 本文提供有关 azcopy login 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 98f8554d6313147c03d4a0bec74e36043cdce342
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87285265"
---
# <a name="azcopy-login"></a>azcopy login

登录到 Azure Active Directory 以访问 Azure 存储资源。

## <a name="synopsis"></a>概要

登录到 Azure Active Directory 以访问 Azure 存储资源。

若要获得访问 Azure 存储帐户的授权，必须在存储帐户、父资源组或父订阅的上下文中为用户帐户分配“存储 Blob 数据参与者”角色。

此命令会通过 OS 内置机制缓存当前用户的已加密登录信息。

> [!IMPORTANT]
> 如果使用命令行设置环境变量，则可以在命令行历史记录中读取该变量。 请考虑从命令行历史记录中清除包含凭据的变量。 若要防止变量出现在历史记录中，可以使用脚本提示用户输入凭据，并设置环境变量。

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="examples"></a>示例

将默认的 AAD 租户 ID 设置为常用 ID，以交互方式登录：

```azcopy
azcopy login
```

使用指定的租户 ID 以交互方式登录：

```azcopy
azcopy login --tenant-id "[TenantID]"
```

使用虚拟机 (VM) 的系统分配标识登录：

```azcopy
azcopy login --identity
```

使用 VM 的用户分配标识和服务标识的客户端 ID 登录：
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
使用 VM 的用户分配标识和服务标识的对象 ID 登录：

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

使用 VM 的用户分配标识和服务标识的资源 ID 登录：
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

使用客户端机密以服务主体身份登录：将环境变量 AZCOPY_SPA_CLIENT_SECRET 设置为客户端机密，以便进行基于机密的服务主体身份验证。

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

使用证书和密码以服务主体身份登录：

将环境变量 AZCOPY_SPA_CERT_PASSWORD 设置为证书的密码，以便进行基于证书的服务主体授权：

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

将 `/path/to/my/cert` 视为 PEM 或 PKCS12 文件的路径。 AzCopy 不会到系统证书存储中获取你的证书。

进行基于证书的服务主体身份验证时，`--certificate-path` 是必需的。

## <a name="options"></a>选项

“--aad-endpoint”字符串要使用的 Azure Active Directory 终结点。 Azure 云的默认 (https://login.microsoftonline.com) 是正确的。 在国家云中进行身份验证时设置此参数。 托管服务标识不需要。

“--application-id”字符串 - 用户分配标识的应用程序 ID。 服务主体身份验证所需。

“--certificate-path”字符串 - 用于 SPN 身份验证的证书的路径。 基于证书的服务主体身份验证所需。

“--help” - 获取 `azcopy login` 命令的帮助。

“--identity” - 使用虚拟机的标识（也称托管服务标识 (MSI)）登录。

“--identity-id”字符串 - 用户分配的标识的客户端 ID。

“--identity-object-id”字符串 - 用户分配的标识的对象 ID。

“--identity-resource-id”字符串 - 用户分配的标识的资源 ID。

“--service-principal”- 使用证书或机密通过服务主体名称 (SPN) 登录。 客户端机密或证书密码必须置于相应的环境变量中。 键入 AzCopy env 即可查看环境变量的名称和说明。

“--tenant-id”字符串 - Azure Active Directory 目录 ID，用于 OAuth 设备交互式登录。

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps float|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|
|--trusted-microsoft-suffixes 字符串   |指定可在其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为“.core.windows.net;.core.chinacloudapi.cn;.core.cloudapi.de;.core.usgovcloudapi.net” 。 此处列出的任何内容都会添加到默认值。 为安全，你只应在此处放置 Microsoft Azure 域。 用分号分隔多个条目。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
