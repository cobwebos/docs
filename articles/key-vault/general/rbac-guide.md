---
title: 使用 Azure RBAC 向应用程序授予访问 Azure 密钥保管库的权限 |Microsoft Docs
description: 了解如何使用 Azure 基于角色的访问控制提供对密钥、机密和证书的访问权限。
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: b80b3cf1712fab17b8f626bae5fef97849e44e20
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90972268"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control-preview"></a>使用 Azure 基于角色的访问控制 (预览版，提供对 Key Vault 密钥、证书和机密的访问权限) 

> [!NOTE]
> Key Vault 资源提供程序支持两种资源类型： **保管库** 和 **托管的 hsm**。 本文中所述的访问控制仅适用于 **保管库**。 若要了解有关托管 HSM 的访问控制的详细信息，请参阅 [托管 hsm 访问控制](../managed-hsm/access-control.md)。

Azure RBAC) 的 azure 基于角色的访问控制 (是在 [azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 上构建的授权系统，可提供对 azure 资源的精细访问管理。

Azure RBAC 允许用户管理密钥、机密和证书权限。 它为管理所有密钥保管库中的所有权限提供了一个位置。 

Azure RBAC 模型提供了在不同的作用域级别上设置权限的功能：管理组、订阅、资源组或单个资源。  用于密钥保管库的 Azure RBAC 还提供对单个密钥、机密和证书具有单独权限的功能

有关详细信息，请参阅 azure [RBAC)  (azure 基于角色的访问控制 ](https://docs.microsoft.com/azure/role-based-access-control/overview)。

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>单个密钥、机密和证书的最佳实践

建议将每个应用程序的保管库用于每个环境 (开发、预生产和生产) 。

单个密钥、机密和证书权限仅应用于特定方案：

-   需要在层之间分隔访问控制的多层应用程序

-   当应用程序需要访问该密钥保管库中的机密子集时，具有常见机密的共享密钥保管库

有关 Azure Key Vault 管理准则的详细信息，请参阅：

- [Azure Key Vault 最佳做法](best-practices.md)
- [Azure Key Vault 服务限制](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations-preview"></a>用于 Key Vault 数据平面操作 (预览版的 Azure 内置角色) 

| 内置角色 | 说明 | ID |
| --- | --- | --- |
| Key Vault 管理员 (预览版)  | 对密钥保管库以及其中的所有对象（包括证书、密钥和机密）执行所有数据平面操作。 无法管理 key vault 资源或管理角色分配。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
|  (预览版 Key Vault 证书官员)  | 对密钥保管库的证书执行任何操作（管理权限除外）。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | a4417e6f-fecd-4de8-b567-7b0420556985 |
|  (预览版 Key Vault 加密官) | 对密钥保管库的密钥执行任何操作（"管理权限" 除外）。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault 加密服务加密 (预览版)  | 读取密钥的元数据并执行包装/解包操作。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault 加密用户 (预览版)  | 使用密钥执行加密操作。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault Reader (预览版) | 读取密钥保管库及其证书、密钥和机密的元数据。 无法读取机密内容或密钥材料之类的敏感值。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | 21090545-7ca7-4776-b22c-e363652d74d2 |
|  (预览版 Key Vault 秘密专员) | 对密钥保管库的密码执行任何操作（"管理权限" 除外）。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault 机密用户 (预览版) | 读取机密内容。 仅适用于使用 "Azure 基于角色的访问控制" 权限模型的密钥保管库。 | 4633458b-17de-408a-b874-0445c86b69e6 |

有关 Azure 内置角色定义的详细信息，请参阅 [azure 内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>在 Key Vault 中使用 Azure RBAC 机密、密钥和证书权限

Key vault 的新 Azure RBAC 权限模型提供保管库访问策略权限模型的替代方法。 

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>启用 Key Vault 上的 Azure RBAC 权限

> [!IMPORTANT]
> 设置 Azure RBAC 权限模型会使所有访问策略权限失效。 当未分配等效的 Azure 角色时，它可能会导致中断。

1.  启用新 key vault 的 Azure RBAC 权限：

    ![启用 RBAC 权限-新建保管库](../media/rbac/image-1.png)

2.  启用现有密钥保管库的 Azure RBAC 权限：

    ![启用 RBAC 权限-现有保管库](../media/rbac/image-2.png)

### <a name="assign-role"></a>分配角色

> [!Note]
> 建议在脚本中使用唯一角色 ID，而不是角色名称。 因此，如果重命名了某个角色，你的脚本将继续工作。 在预览期间，每个角色都有 " (预览版) " 后缀，稍后会删除。 此文档中的角色名称仅用于提高可读性。

Azure CLI 命令创建角色分配：

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

在 Azure 门户中，"Azure 角色分配" 屏幕可用于 "访问控制 (IAM) " 选项卡上的所有资源。

![角色分配- (IAM) "选项卡](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>资源组作用域角色分配

1.  中转到 key vault 资源组。
    ![角色分配-资源组](../media/rbac/image-4.png)

2.  单击 "访问控制 (IAM) " \> 添加角色分配 "" \> 添加 "

3.  为当前用户创建 Key Vault 读者角色 "Key Vault 读者 (预览) "

    ![添加角色-资源组](../media/rbac/image-5.png)

Azure CLI：
```azurecli
az role assignment create --role "Key Vault Reader (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

上述角色分配提供在 key vault 中列出 key vault 对象的能力。

### <a name="key-vault-scope-role-assignment"></a>Key Vault 作用域角色分配

1. 中转到 Key Vault \> 访问控制 (IAM) "选项卡

2. 单击 "添加-角色分配" " \> 添加"

3. 为当前用户创建密钥机密官角色 "Key Vault 秘密专员 (预览版) "。

    ![角色分配-密钥保管库](../media/rbac/image-6.png)

 Azure CLI：

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

创建以上角色分配后，你可以创建/更新/删除机密。

4. 创建新机密 ( 密码 \> + 生成/导入) 用于测试机密级别角色分配。

    ![添加角色-密钥保管库](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>机密作用域角色分配

1. 打开以前创建的机密之一，请注意 (IAM)  (预览中的 "概述" 和 "访问控制") 

2. 单击 "访问控制 (IAM) # B2 预览) " 选项卡

    ![角色分配-密码](../media/rbac/image-8.png)

3. 为当前用户创建密钥机密官角色 "Key Vault (预览版) "，就像在 Key Vault 中执行此操作一样。

Azure CLI：

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

### <a name="test-and-verify"></a>测试和验证

> [!NOTE]
> 删除角色分配后，浏览器使用缓存和页面刷新。<br>
> 允许角色分配几分钟来刷新

1. 在密钥保管库级别上，验证是否添加新机密，但没有 "Key Vault 机密专员" 角色。

请参阅 key vault Access control (IAM) "选项卡，然后删除此资源的" Key Vault 机密专员 (预览) "角色分配。

![删除分配-密钥保管库](../media/rbac/image-9.png)

导航到以前创建的机密。 你可以看到所有机密属性。

![具有访问权限的机密视图](../media/rbac/image-10.png)

创建新机密 ( 密码 \> + 生成/导入) 应显示以下错误：

   ![创建新机密](../media/rbac/image-11.png)

2.  验证机密级别上没有 "Key Vault 秘密专员" 角色的机密编辑。

-   请访问以前创建的机密访问控制 (IAM)  (预览 ") " 选项卡，然后删除此资源的 "Key Vault 机密官 (预览) " 角色分配。

-   导航到以前创建的机密。 你可以看到机密属性。

   ![无访问权限的机密视图](../media/rbac/image-12.png)

3. 验证密钥保管库级别上没有读取者角色的机密读取。

-   请访问 key vault 资源组访问控制 (IAM) "选项卡，并删除" Key Vault 读取器 (预览) "角色分配。

-   导航到 key vault 的机密选项卡会显示以下错误：

   ![机密选项卡-错误](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>创建自定义角色 

[az role definition create 命令](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-create)

** (CLI bash 脚本) </br>**
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```

有关如何创建自定义角色的详细信息，请参阅：

[Azure 自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)

## <a name="known-limits-and-performance"></a>已知的限制和性能

-   2000每个订阅的 Azure 角色分配

-   角色分配延迟：达到当前预期性能时，将需要10分钟 (600 秒) 更改角色分配以应用角色

## <a name="learn-more"></a>了解详细信息

- [Azure RBAC 概述](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [自定义角色教程](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-cli)
