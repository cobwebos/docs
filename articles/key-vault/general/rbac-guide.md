---
title: 使用 Azure RBAC 向应用程序授予访问 Azure Key Vault 的权限 | Microsoft Docs
description: 了解如何使用 Azure 基于角色的访问控制提供对密钥、机密和证书的访问权限。
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 04b5c9464c614c32f178e35e72cee98450007a62
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91772781"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control-preview"></a>使用 Azure 基于角色的访问控制提供对 Key Vault 密钥、证书和机密的访问权限（预览）

> [!NOTE]
> Key Vault 资源提供程序支持两种资源类型： **保管库** 和 **托管的 hsm**。 本文中所述的访问控制仅适用于 **保管库**。 若要了解有关托管 HSM 的访问控制的详细信息，请参阅 [托管 hsm 访问控制](../managed-hsm/access-control.md)。

Azure 基于角色的访问控制 (Azure RBAC) 是在 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)基础上构建的授权系统，针对 Azure 资源提供精细的访问权限管理。

Azure RBAC 允许用户管理密钥、机密和证书权限。 它提供了一个可跨所有密钥保管库管理所有权限的位置。 

Azure RBAC 模型提供了在不同范围级别设置权限的功能：管理组、订阅、资源组或单个资源。  用于密钥保管库的 Azure RBAC 还提供了对单个密钥、机密和证书设定单独权限的功能

有关详细信息，请参阅 [Azure 基于角色的访问控制 (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)。

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>单个密钥、机密和证书的最佳做法

我们的建议是对每个环境（开发环境、预生产环境和生产环境）的每个应用程序使用一个保管库。

单个密钥、机密和证书权限应仅用于特定场景：

-   需要在层之间分离访问控制的多层应用程序

-   包含公共机密的共享密钥保管库（当应用程序需要访问该密钥保管库中的机密子集时）

有关 Azure Key Vault 管理指南的详细信息，请参阅：

- [Azure Key Vault 最佳做法](best-practices.md)
- [Azure Key Vault 服务限制](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations-preview"></a>用于 Key Vault 数据平面操作的 Azure 内置角色（预览版）
> [!NOTE]
> `Key Vault Contributor` 角色适用于管理平面操作，用于管理密钥保管库。 它不允许访问密钥、机密和证书。

| 内置角色 | 说明 | ID |
| --- | --- | --- |
| Key Vault 管理员(预览版) | 对密钥保管库以及其中的所有对象（包括证书、密钥和机密）执行所有数据平面操作。 无法管理密钥保管库资源或管理角色分配。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault 证书管理人员(预览版) | 对密钥保管库的证书执行任何操作（管理权限除外）。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault 加密管理人员(预览版)| 对密钥保管库的密钥执行任何操作（管理权限除外）。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault 加密服务加密(预览版) | 读取密钥的元数据并执行包装/展开操作。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault 加密用户(预览版) | 使用密钥执行加密操作。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault 读取者(预览版)| 读取密钥保管库及其证书、密钥和机密的元数据。 无法读取机密内容或密钥材料等敏感值。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault 机密管理人员(预览版)| 对密钥保管库的机密执行任何操作（管理权限除外）。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault 机密用户(预览版)| 读取机密内容。 仅适用于使用“Azure 基于角色的访问控制”权限模型的密钥保管库。 | 4633458b-17de-408a-b874-0445c86b69e6 |

有关 Azure 内置角色定义的详细信息，请参阅 [Azure 内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>对密钥保管库使用 Azure RBAC 机密、密钥和证书权限

用于密钥保管库的新 Azure RBAC 权限模型提供了保管库访问策略权限模型的替代方案。 

### <a name="prerequisites"></a>先决条件

若要添加角色分配，必须具有：

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles.md#user-access-administrator)或[所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>对密钥保管库启用 Azure RBAC 权限

> [!IMPORTANT]
> 设置 Azure RBAC 权限模型会使所有访问策略权限失效。 若未分配对等的 Azure 角色，它可能会导致故障。

1.  对新密钥保管库启用 Azure RBAC 权限：

    ![启用 RBAC 权限 - 新建保管库](../media/rbac/image-1.png)

2.  对现有密钥保管库启用 Azure RBAC 权限：

    ![启用 RBAC 权限 - 现有保管库](../media/rbac/image-2.png)

### <a name="assign-role"></a>分配角色

> [!Note]
> 建议在脚本中使用唯一的角色 ID，而不是角色名称。 这样一来，即使角色重命名，脚本仍可以继续使用。 在预览期间，每个角色都会有“(预览版)”后缀，该后缀将在以后删除。 此文档中的角色名称仅用于提高可读性。

用于创建角色分配的 Azure CLI 命令：

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

在 Azure 门户中，Azure 角色分配屏幕可用于访问控制 (IAM) 选项卡上的所有资源。

![角色分配 - (IAM) 选项卡](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>资源组范围的角色分配

1.  转到密钥保管库资源组。
    ![角色分配 - 资源组](../media/rbac/image-4.png)

2.  单击“访问控制(IAM)”\>“添加角色分配”\>“添加”

3.  为当前用户创建 Key Vault 读取者角色“Key Vault 读取者(预览版)”

    ![添加角色 - 资源组](../media/rbac/image-5.png)

Azure CLI：
```azurecli
az role assignment create --role "Key Vault Reader (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

上述角色分配提供了在密钥保管库中列出密钥保管库对象的功能。

### <a name="key-vault-scope-role-assignment"></a>Key Vault 范围角色分配

1. 转到“Key Vault”\>“访问控制(IAM)”选项卡

2. 单击“添加角色分配”\>“添加”

3. 为当前用户创建 Key Vault 机密管理人员角色“Key Vault 机密管理人员(预览版)”。

    ![角色分配 - 密钥保管库](../media/rbac/image-6.png)

 Azure CLI：

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

创建上述角色分配后，你可以创建/更新/删除机密。

4. 创建新的机密（“机密”\>“+ 生成/导入”）以测试机密级别角色分配。

    ![添加角色 - 密钥保管库](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>机密范围角色分配

1. 打开一个以前创建的机密，找到“概述和访问控制(IAM) (预览版)”

2. 单击“访问控制(IAM) (预览版)”选项卡

    ![角色分配 - 机密](../media/rbac/image-8.png)

3. 为当前用户创建 Key Vault 机密管理人员角色“Key Vault 机密管理人员(预览版)”，与上面对 Key Vault 所做的操作相同。

Azure CLI：

```azurecli
az role assignment create --role "Key Vault Secrets Officer (preview)" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

### <a name="test-and-verify"></a>测试和验证

> [!NOTE]
> 浏览器使用缓存，删除角色分配后需要刷新页面。<br>
> 预留几分钟时间供角色分配刷新

1. 验证在密钥库级别上没有“Key Vault 机管理人员”角色的情况下添加新机密。

转到密钥保管库“访问控制(IAM)”选项卡，并删除此资源的“Key Vault 机密管理人员(预览版)”角色分配。

![删除分配 - 密钥保管库](../media/rbac/image-9.png)

导航到以前创建的机密。 你可以看到所有机密属性。

![具有访问权限的机密视图](../media/rbac/image-10.png)

若创建新机密（“机密”\>“+ 生成/导入”），应会看到以下错误：

   ![创建新机密](../media/rbac/image-11.png)

2.  验证在机密级别上没有“Key Vault 机密管理人员”角色的情况下编辑机密。

-   转到先前创建的机密“访问控制 (IAM) (预览版)”选项卡，并删除此资源的“Key Vault 机密管理人员(预览版)”角色分配。

-   导航到以前创建的机密。 你可以看到机密属性。

   ![无访问权限的机密视图](../media/rbac/image-12.png)

3. 验证在密钥库级别没有读取者角色的情况下读取机密。

-   转到密钥保管库资源组“访问控制(IAM)”选项卡，并删除“Key Vault 读取者(预览版)”角色分配。

-   若导航到密钥保管库的“机密”选项卡，应会看到以下错误：

   ![“机密”选项卡 - 错误](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>创建自定义角色 

[az role definition create 命令](https://docs.microsoft.com/cli/azure/role/definition#az-role-definition-create)

（CLI bash 脚本）</br>
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

-   每个订阅 2000 个 Azure 角色分配

-   角色分配延迟：在当前预期的性能下，角色分配更改后最多需要 10 分钟（600 秒）才能应用角色

## <a name="learn-more"></a>了解更多

- [Azure RBAC 概述](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [自定义角色教程](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-cli)
