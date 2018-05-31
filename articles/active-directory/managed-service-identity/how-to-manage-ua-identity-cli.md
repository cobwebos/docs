---
title: 如何使用 Azure CLI 管理用户分配的托管服务标识 (MSI)
description: 分步说明如何使用 Azure CLI 创建、列出和删除用户分配的托管服务标识。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 5262914e469bdc07921c3b82e990d544349b5fd4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930660"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>使用 Azure CLI 创建、列出或删除用户分配标识

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

托管服务标识在 Azure Active Directory 中为 Azure 服务提供托管标识。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中输入凭据了。 

在本文中，你将了解如何使用 Azure CLI 创建、列出和删除用户分配标识。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉托管服务标识，请查阅[概述部分](overview.md)。 **请务必了解[系统分配标识与用户分配标识之间的差异](overview.md#how-does-it-work)**。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。

- 若要运行 CLI 脚本示例，可以使用下列三种方法：

    - 在 Azure 门户中使用 [Azure Cloud Shell](../../cloud-shell/overview.md)（见下一部分）。
    - 单击各代码块右上角的“试运行”按钮，使用嵌入的 Azure Cloud Shell。
    - 如果希望使用本地 CLI 控制台，可以[安装最新版 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.13 或更高版本）。 使用与要在其下部署用户分配标识的 Azure 订阅关联的帐户通过 `az login` 登录到 Azure。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识 

若要创建用户分配标识，请使用 [az identity create](/cli/azure/identity#az-identity-create) 命令。 `-g` 参数指定要创建用户分配标识的资源组，`-n` 参数指定其名称。 将 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 参数值替换为自己的值：

> [!IMPORTANT]
> 创建用户分配标识时仅支持字母数字和连字符（0-9 或 a-z 或 A-Z 或 -）字符。 另外，分配给 VM/VMSS 的名称长度应限制为 24 个字符，否则它无法正常工作。 稍后返回查看更新。 有关详细信息，请参阅 [FAQ 和已知问题](known-issues.md)

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>列出用户分配标识

若要列出用户分配标识，请使用 [az identity list](/cli/azure/identity#az-identity-list) 命令。  `-g` 参数指定创建了用户分配标识的资源组。  将 `<RESOURCE GROUP>` 替换为自己的值：

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
在 json 响应中，用户标识为 `type` 键返回 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 值。

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>删除用户分配标识

若要删除用户分配标识，请使用 [az identity delete](/cli/azure/identity#az-identity-delete) 命令。  -n 参数指定其名称，-g 参数指定创建了用户分配标识的资源组。  将 `<USER ASSIGNED IDENTITY NAME>` 和 `<RESOURCE GROUP>` 参数值替换为自己的值：

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> 删除用户分配标识不会从将其分配到的任何资源中移除引用。 请使用 `az vm/vmss identity remove` 命令从 VM/VMSS 中移除这些引用

## <a name="related-content"></a>相关内容

有关 Azure CLI 标识命令的完整列表，请参阅 [az identity](/cli/azure/identity)。

有关如何向 Azure VM 分配用户分配标识的信息，请参阅[使用 Azure CLI 配置托管服务标识 (MSI)](qs-configure-cli-windows-vm.md#user-assigned-identity)


 
