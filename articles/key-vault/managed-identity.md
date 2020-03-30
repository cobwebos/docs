---
title: 使用系统分配的托管标识访问 Azure Key Vault
description: 了解如何为应用服务应用程序创建托管标识，以及如何使用它来访问 Azure Key Vault。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 36a4871339401629300eedd77b6441aed10aabf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270948"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>使用托管标识提供 Key Vault 身份验证

借助 Azure Active Directory 的托管标识，应用可以轻松访问其他受 Azure AD 保护的资源。 标识由 Azure 平台托管，无需设置或转交任何机密。 有关详细信息，请参阅 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 

本文介绍如何为应用服务应用程序创建托管标识，以及如何使用它来访问 Azure Key Vault。 对于托管在 Azure VM 中的应用程序，请参阅[使用 Windows VM 系统分配的托管标识来访问 Azure Key Vault](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件 

若要完成本指南，必须备好以下资源。 

- 一个 Key Vault。 可以使用现有的 Key Vault，或者遵循以下快速入门之一中的步骤创建一个新的 Key Vault：
   - [使用 Azure CLI 创建 Key Vault](quick-create-cli.md)
   - [使用 Azure PowerShell 创建 Key Vault](quick-create-powershell.md)
   - [使用 Azure 门户创建 Key Vault](quick-create-portal.md)
- 一个现有的应用服务应用程序，需向其授予密钥保管库访问权限。 可以按[应用服务文档](../app-service/overview.md)中的步骤快速创建一个。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)或[Azure 电源外壳](/powershell/azure/overview)。 或者，可以使用 [Azure 门户](https://portal.azure.com)。


## <a name="adding-a-system-assigned-identity"></a>添加系统分配的标识 

首先，必须向应用程序添加系统分配的标识。 
 
### <a name="azure-portal"></a>Azure 门户 

要在门户中设置托管标识，需先按常规创建应用程序，然后启用该功能。 

1. 如果使用函数应用，请导航到“平台功能”。**** 对于其他应用类型，请在左侧导航区域向下滚动到“设置”组。**** 

1. 选择**托管标识**。 

1. 在“系统分配的”选项卡中，将“状态”切换为“启用”************。 单击“保存”。**** 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

本快速入门需要 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找当前版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 

若要使用 Azure CLI 登录，请使用 [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令：

```azurecli-interactive
az login
```

若要详细了解 Azure CLI 的登录选项，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。 

若要为此应用程序创建标识，请使用 Azure CLI 的 [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) 命令或 [az functionapp identity assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) 命令：


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

记下 `PrincipalId`，下一部分需要用到它。

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>授予应用对 Key Vault 的访问权限 

### <a name="azure-portal"></a>Azure 门户

1.  导航到 Key Vault 资源。 

1.  选择“访问策略”****，然后单击“添加访问策略”****。 

1.  在“机密权限”**** 中，选择“获取、列出”。**** 

1.  选择“选择主体”****，并在搜索字段中输入应用的名称。  选择结果列表中的应用，并单击“选择”****。 

1.  单击“添加”，完成添加新访问策略的操作****。

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

若要授予应用程序访问密钥保管库的权限，请使用 Azure CLI 的 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令，并为 **ObjectId** 参数提供此前记下的 **principalId**。

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>后续步骤

- [Azure 密钥保管库安全性：标识和访问管理](overview-security.md#identity-and-access-management)
- [使用访问控制策略提供 Key Vault 身份验证](key-vault-group-permissions-for-apps.md)
- [关于密钥、机密和证书](about-keys-secrets-and-certificates.md)
- [保护您的密钥保管库](key-vault-secure-your-key-vault.md)。
- [Azure 密钥保管库开发人员指南](key-vault-developers-guide.md)
- 查看 [Azure Key Vault 最佳做法](key-vault-best-practices.md)