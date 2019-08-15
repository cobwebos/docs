---
title: 使用 Azure CLI (预览版) 为容器或 blob 创建用户委托 SAS-Azure 存储
description: 了解如何使用 Azure CLI 在 Azure 存储中使用 Azure Active Directory 凭据创建用户委托 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c8b17c4a2c58595d12a88ed3aff6c928b56f5e2c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990801"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli-preview"></a>使用 Azure CLI (预览版) 为容器或 blob 创建用户委托 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文介绍如何使用 Azure Active Directory (Azure AD) 凭据为包含 Azure CLI (预览版) 的容器或 blob 创建用户委托 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>安装最新版本的 Azure CLI

若要使用 Azure CLI 使用 Azure AD 凭据来保护 SAS, 首先请确保已安装最新版本的 Azure CLI。 有关安装 Azure CLI 的详细信息, 请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sign-in-with-azure-ad-credentials"></a>用 Azure AD 凭据登录

用你的 Azure AD 凭据登录到 Azure CLI。 有关详细信息，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)。

## <a name="assign-permissions-with-rbac"></a>通过 RBAC 分配权限

若要从 Azure PowerShell 创建用户委托 SAS, 必须为用于登录 Azure CLI 的 Azure AD 帐户分配包含 storageAccounts **//blobServices/generateUserDelegationKey**操作的角色。 此权限允许 Azure AD 帐户请求*用户委托密钥*。 用户委托密钥用于对用户委托 SAS 进行签名。 提供 storageAccounts/ **blobServices/generateUserDelegationKey**操作的角色必须在存储帐户、资源组或订阅的级别上进行分配。

如果你的权限不足, 无法将 RBAC 角色分配到 Azure AD 安全主体, 你可能需要要求帐户所有者或管理员分配必要的权限。

下面的示例分配**存储 Blob 数据参与者**角色, 其中包括 storageAccounts **//blobServices/generateUserDelegationKey**操作。 角色的作用域为存储帐户的级别。

请务必将尖括号中的占位符值替换为你自己的值：

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

有关包含 storageAccounts/ **blobServices/generateUserDelegationKey**操作的内置角色的详细信息, 请参阅[Azure 资源的内置角色](/role-based-access-control/built-in-roles)。

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>使用 Azure AD 凭据来保护 SAS

使用 Azure CLI 创建用户委托 SAS 时, 将为你隐式创建用于对 SAS 进行签名的用户委托密钥。 为 SAS 指定的开始时间和到期时间也用作用户委托密钥的开始时间和到期时间。

由于用户委托密钥的有效最大时间间隔是从开始日期起的7天, 因此, 你应为开始时间在7天内的 SAS 指定到期时间。 此 SA 在用户委托密钥过期后无效, 因此过期时间超过7天的 SAS 仍将仅适用于7天。

创建用户委托 SAS 时, 和`--auth-mode login` `--as-user parameters`是必需的。 指定`--auth-mode`参数的*登录名*, 以便向 Azure 存储空间发出的请求授权 Azure AD 凭据。 `--as-user`指定参数以指示返回的 sas 应为用户委托 sas。

### <a name="create-a-user-delegation-sas-for-a-container"></a>为容器创建用户委托 SAS

若要为具有 Azure CLI 的容器创建用户委托 SAS, 请调用[az storage container 生成-SAS](/cli/azure/storage/container#az-storage-container-generate-sas)命令。

容器上的用户委托 SAS 支持的权限包括添加、创建、删除、列出、读取和写入。 权限可以单独指定或组合指定。 有关这些权限的详细信息, 请参阅[创建用户委托 SAS](/rest/api/storageservices/create-a-user-delegation-sas)。

以下示例返回容器的用户委托 SAS 令牌。 请记住, 用自己的值替换括号中的占位符值:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

返回的用户委托 SAS 令牌如下所示:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>为 blob 创建用户委托 SAS

若要为带有 Azure CLI 的 blob 创建用户委托 SAS, 请调用[az storage blob 生成 sas](/cli/azure/storage/blob#az-storage-blob-generate-sas)命令。

Blob 上的用户委托 SAS 支持的权限包括添加、创建、删除、读取和写入。 权限可以单独指定或组合指定。 有关这些权限的详细信息, 请参阅[创建用户委托 SAS](/rest/api/storageservices/create-a-user-delegation-sas)。

以下语法返回 blob 的用户委托 SAS。 该示例指定`--full-uri`参数, 该参数返回附加 SAS 令牌的 blob URI。 请记住, 用自己的值替换括号中的占位符值:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

返回的用户委托 SAS URI 将类似于:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> 用户委托 SAS 不支持使用存储访问策略定义权限。

## <a name="see-also"></a>请参阅

- [创建用户委派 SAS (REST API)](/rest/api/storageservices/create-a-user-delegation-sas)
- [获取用户委派密钥操作](/rest/api/storageservices/get-user-delegation-key)
