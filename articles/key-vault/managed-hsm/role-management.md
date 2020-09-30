---
title: 托管 HSM 数据平面角色管理 - Azure Key Vault | Microsoft Docs
description: 使用本文管理托管 HSM 的角色分配
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 6654b97f914ce4c1e3e55d38f47bd5bde0a4891e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992146"
---
# <a name="managed-hsm-role-management"></a>托管 HSM 角色管理

> [!NOTE]
> Key Vault 支持两种类型的资源：保管库和托管 HSM。 本文介绍托管 HSM。 若要了解如何管理保管库，请参阅[使用 Azure CLI 管理密钥保管库](../general/manage-with-cli2.md)。

有关托管 HSM 的概述，请参阅[什么是托管 HSM？](overview.md)。 如果你还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

本文介绍如何管理托管 HSM 数据平面的角色。 若要了解托管 HSM 访问控制模型，请参阅[托管 HSM 访问控制](access-control.md)。

若要允许安全主体（例如用户、服务主体、组或托管标识）执行托管 HSM 数据平面操作，需要为它们分配允许执行这些操作的角色。 例如，如果要允许应用程序使用密钥执行签名操作，则必须为其分配一个角色，该角色包含“Microsoft.KeyVault/managedHSM/keys/sign/action”作为数据操作之一。 可以在特定范围内分配角色。 托管 HSM 本地 RBAC 支持两种范围，HSM 范围（`/` 或 `/keys`）和按密钥 (`/keys/<keyname>`)。

有关所有托管 HSM 内置角色及其允许的操作的列表，请参阅[托管 HSM 内置角色](built-in-roles.md)。

## <a name="prerequisites"></a>先决条件

若要使用本文中的 Azure CLI 命令，必须准备好以下各项：

* Microsoft Azure 订阅。 如果没有，可以注册[免费试用版](https://azure.microsoft.com/pricing/free-trial)。
* Azure CLI 版本 2.12.0 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。
* 订阅中的托管 HSM。 请参阅[快速入门：使用 Azure CLI 预配和激活托管 HSM](quick-create-cli.md)，预配和激活托管 HSM。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

若要使用 CLI 登录到 Azure，可以键入：

```azurecli
az login
```

若要详细了解通过 CLI 使用的登录选项，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)

## <a name="create-a-new-role-assignment"></a>创建新的角色分配

### <a name="assign-roles-for-all-keys"></a>为所有密钥分配角色

使用 `az keyvault role assignment create` 命令为适用于 ContosoHSM 中所有密钥（范围 `/keys`）的用户主体名称 user2@contoso.com 所标识的用户分配托管 HSM 加密管理人员角色  。

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>为特定密钥分配角色

使用 `az keyvault role assignment create` 命令为由名为 myrsakey 的特定密钥的用户主体名称 user2@contoso.com 标识的用户分配托管 HSM 加密管理人员角色  。

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>列出现有的角色分配

使用 `az keyvault role assignment list` 列出角色分配。

在范围 /（没有指定范围时的默认选择）中为所有用户（指定被分派人时的默认选择）执行的所有角色分配

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

特定用户 user1@contoso.com 在 HSM 级别的所有角色分配。

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

针对特定密钥 myrsakey 的特定用户 user2@contoso.com 的所有角色分配 。

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

为特定用户 user2@contoso.com 分配适用于特定密钥 myrsakey 的“托管 HSM 加密管理人员”这一特定角色  


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>删除角色分配

使用 `az keyvault role assignment delete` 命令删除分配给用户 user2@contoso.com 的适用于密钥 myrsakey2 的“托管 HSM 加密管理人员”角色  。

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>列出所有可用的角色定义

使用 `az keyvault role definition list` 命令列出所有角色定义。

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 的概述。
- 请参阅有关[托管 HSM 角色管理](role-management.md)的教程
- 详细了解[托管 HSM 访问控制模型](access-control.md)
- 查看[托管 HSM 本地 RBAC 的所有内置角色](built-in-roles.md)