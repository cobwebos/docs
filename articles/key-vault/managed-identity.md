---
title: 使用系统分配的托管标识访问 Azure Key Vault
description: 了解如何为应用服务应用程序创建托管标识，以及如何使用它来访问 Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 75317f73ad724b4ce8ad7a894890b2269bd8c5d0
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837500"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>提供具有托管标识的 Key Vault 身份验证

通过 Azure Active Directory 中的托管标识，你的应用可以轻松访问其他 Azure AD 保护的资源。 标识由 Azure 平台托管，无需设置或转交任何机密。 有关详细信息，请参阅 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 

本文介绍如何为应用服务应用程序创建一个托管标识，并使用该标识来访问 Azure Key Vault。 对于 Azure Vm 中托管的应用程序，请参阅[使用 WINDOWS VM 系统分配的托管标识访问 Azure Key Vault](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件 

若要完成本指南，你必须拥有以下资源。 

- 密钥保管库。 你可以使用现有的密钥保管库，或者按照以下快速入门之一中的步骤创建一个新的密钥保管库：
   - [使用 Azure CLI 创建密钥保管库](quick-create-cli.md)
   - [使用 Azure PowerShell 创建密钥保管库](quick-create-powershell.md)
   - [使用 Azure 门户创建密钥保管库](quick-create-portal.md)。
- 要向其授予密钥保管库访问权限的现有应用服务应用程序。 可以按照[应用服务文档](../app-service/overview.md)中的步骤来快速创建一个。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)或[Azure PowerShell](/powershell/azure/overview)。 或者，您可以使用[Azure 门户](https://portal.azure.com)。


## <a name="adding-a-system-assigned-identity"></a>添加系统分配的标识 

首先，必须将系统分配的标识添加到应用程序。 
 
### <a name="azure-portal"></a>Azure 门户 

要在门户中设置托管标识，需先按常规创建应用程序，然后启用该功能。 

1. 如果使用函数应用，请导航到“平台功能”。 对于其他应用类型，请在左侧导航区域向下滚动到“设置”组。 

1. 选择“托管标识”。 

1. 在“系统分配的”选项卡中，将“状态”切换为“启用”。 单击“保存”。 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

本快速入门需要 Azure CLI 版本2.0.4 或更高版本。 运行 `az --version` 即可查找当前版本。 如需安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 

若要使用 Azure CLI 登录，请使用[az login](/cli/azure/reference-index?view=azure-cli-latest#az-login)命令：

```azurecli-interactive
az login
```

有关 Azure CLI 的登录选项的详细信息，请参阅[登录 Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。 

若要为此应用程序创建标识，请使用 Azure CLI [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) command 或[az functionapp identity assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign)命令：


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

记下 `PrincipalId`，这将在下一部分中需要。

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

1.  选择 "**访问策略**"，然后单击 "**添加访问策略**"。 

1.  在 "**机密权限**" 中，选择**Get，List**。 

1.  选择 "**选择主体**"，然后在 "搜索" 字段中输入应用程序的名称。  在结果列表中选择应用，然后单击 "**选择**"。 

1.  单击 "**添加**" 以添加新的访问策略。

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

若要向应用程序授予对密钥保管库的访问权限，请使用 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)命令，并在上面记下的**PrincipalId**中提供**ObjectId**参数。

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 安全性：标识和访问管理](overview-security.md#identity-and-access-management)
- [提供访问控制策略 Key Vault 身份验证](key-vault-group-permissions-for-apps.md)
- [关于键、密钥和证书](about-keys-secrets-and-certificates.md)
- [保护密钥保管库](key-vault-secure-your-key-vault.md)。
- [Azure Key Vault 开发人员指南](key-vault-developers-guide.md)
- 查看 [Azure Key Vault 最佳做法](key-vault-best-practices.md)