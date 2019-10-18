---
title: azcopy 登录 |Microsoft Docs
description: 本文提供 azcopy 登录命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 058506110a8ac4b11f272406a854f72062a1c90d
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514719"
---
# <a name="azcopy-login"></a>azcopy login

登录到 Azure Active Directory 以访问 Azure 存储资源。

## <a name="synopsis"></a>概要

登录到 Azure Active Directory 以访问 Azure 存储资源。

若要获得 Azure 存储帐户的授权，必须在存储帐户、父资源组或父订阅的上下文中将 "**存储 Blob 数据参与者**" 角色分配给用户帐户。

此命令将使用操作系统内置机制为当前用户缓存加密的登录信息。

有关详细信息，请参阅示例。

> [!IMPORTANT]
> 如果使用命令行设置环境变量，则可以在命令行历史记录中读取该变量。 请考虑清除命令行历史记录中包含凭据的变量。 若要使变量不显示在历史记录中，可以使用脚本来提示用户输入其凭据，并设置环境变量。

```azcopy
azcopy login [flags]
```

## <a name="examples"></a>示例

以交互方式登录，并将默认 AAD 租户 ID 设置为常见：

```azcopy
azcopy login
```

使用指定的租户 ID 以交互方式登录：

```azcopy
azcopy login --tenant-id "[TenantID]"
```

使用系统分配的虚拟机（VM）标识登录：

```azcopy
azcopy login --identity
```

使用用户分配的 VM 标识和服务标识的客户端 ID 登录：

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

使用用户分配的 VM 标识和服务标识的对象 ID 登录：

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

使用用户分配的 VM 标识和服务标识的资源 ID 登录：

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

使用客户端密码以服务主体身份登录。 将环境变量 AZCOPY_SPA_CLIENT_SECRET 设置为基于机密的服务主体身份验证的客户端密码。

```azcopy
azcopy login --service-principal
```

使用证书和密码以服务主体身份登录。 将环境变量 AZCOPY_SPA_CERT_PASSWORD 设置为证书的密码，以便进行基于证书的服务主体授权。

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

请确保将/path/to/my/cert 视为 PEM 或 PKCS12 文件的路径。 AzCopy 无法连接到系统证书存储来获取证书。

--证书-在执行基于证书的服务主体身份验证时，路径是必需的。

## <a name="options"></a>选项

|选项|描述|
|--|--|
|--application-id 字符串|用户分配的标识的应用程序 ID。 对于服务主体身份验证是必需的。|
|--证书-路径字符串|SPN 身份验证的证书路径。 基于证书的服务主体身份验证所必需的。|
|-h、--help|显示登录命令的帮助内容。|
|--identity|使用虚拟机的标识（也称为托管服务标识（MSI））登录。|
|--identity-id 字符串|用户分配的标识的客户端 ID。|
|--identity-id 字符串|用户分配的标识的对象 ID。|
|--identity-resource id 字符串|用户分配的标识的资源 ID。|
|--服务主体|使用证书或机密通过 SPN （服务主体名称）登录。 必须将客户端密码或证书密码放在相应的环境变量中。 键入 `AzCopy env` 以查看环境变量的名称和描述。|
|--租户-id 字符串| 用于 OAuth 设备交互登录的 Azure active directory 租户 ID。|

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|描述|
|---|---|
|--cap-mbps uint32|以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。|
|--output 类型字符串|命令输出的格式。 选项包括：文本、json。 默认值为 "text"。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
