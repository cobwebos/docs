---
title: 如何使用 Azure CLI 向托管标识分配对 Azure 资源的访问权限
description: 分步说明如何使用 Azure CLI 将托管标识分配给一个资源，将访问权限分配给另一个资源。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fb16b1762f1e5330cf058c37a6b7e0f008eb447
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60290972"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>使用 Azure CLI 向托管标识分配对资源的访问权限

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

为 Azure 资源配置托管标识后，便可以授予该托管标识对其他资源的访问权限，这一点与安全主体一样。 此示例展示了如何使用 Azure CLI 授予 Azure 虚拟机或虚拟机规模集的托管标识对 Azure 存储帐户的访问权限。

## <a name="prerequisites"></a>必备组件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要运行 CLI 脚本示例，可以使用下列三种方法：
    - 在 Azure 门户中使用 [Azure Cloud Shell](../../cloud-shell/overview.md)（见下一部分）。
    - 单击各代码块右上角的“试运行”按钮，使用嵌入的 Azure Cloud Shell。
    - 如果喜欢使用本地 CLI 控制台，请[安装最新版的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>使用 RBAC 授予托管标识对另一资源的访问权限

在 Azure 资源（如 [Azure 虚拟机](qs-configure-cli-windows-vm.md)或 [Azure 虚拟机规模集](qs-configure-cli-windows-vmss.md)）上启用托管标识后： 

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure。 使用与要在其下部署 VM 或虚拟机规模集的 Azure 订阅关联的帐户：

   ```azurecli-interactive
   az login
   ```

2. 此示例要授予 Azure 虚拟机对存储帐户的访问权限。 首先，我们使用 [az resource list](/cli/azure/resource/#az-resource-list) 获取名为 myVM 的虚拟机的服务主体：

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   对于 Azure 虚拟机规模集，使用的命令相同，但获取名为“DevTestVMSS”的虚拟机规模集的服务主体：
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. 获得服务主体 ID 后，立即使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)授予虚拟机或虚拟机规模集对“myStorageAcct”存储帐户的“读者”访问权限：

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>后续步骤

- [Azure 资源的托管标识概述](overview.md)
- 若要启用 Azure 虚拟机上的托管标识，请参阅[使用 Azure CLI 在 VM 上配置 Azure 资源的托管标识](qs-configure-cli-windows-vm.md)。
- 若要启用 Azure 虚拟机规模集上的托管标识，请参阅[使用 Azure CLI 在虚拟机规模集上配置 Azure 资源的托管标识](qs-configure-cli-windows-vmss.md)。
