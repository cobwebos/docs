---
title: "如何使用 Azure CLI 授予 MSI 对 Azure 资源的访问权限"
description: "逐步介绍了如何使用 Azure CLI 授予一个资源上的 MSI 对另一个资源的访问权限。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: ca228a99eec1d376e456f5730aaaf8c569620f43
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>使用 Azure CLI 授予托管服务标识 (MSI) 对资源的访问权限

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

为 Azure 资源配置 MSI 后，便可以授予 MSI 对其他资源的访问权限，这一点与安全主体一样。 此示例展示了如何使用 Azure CLI 授予 Azure 虚拟机或虚拟机规模集的 MSI 对 Azure 存储帐户的访问权限。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

若要运行 CLI 脚本示例，可以使用下列三种方法：

- 在 Azure 门户中使用 [Azure Cloud Shell](../../cloud-shell/overview.md)（见下一部分）。
- 单击各代码块右上角的“试运行”按钮，使用嵌入的 Azure Cloud Shell。
- 如果希望使用本地 CLI 控制台，可以[安装最新版 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.13 或更高版本）。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>使用 RBAC 授予 MSI 对另一个资源的访问权限

在 Azure 资源（如 [Azure 虚拟机](qs-configure-cli-windows-vm.md)或 [Azure 虚拟机规模集](qs-configure-cli-windows-vmss.md)）上启用 MSI 后： 

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure。 使用与要在其下部署 VM 或虚拟机规模集的 Azure 订阅关联的帐户：

   ```azurecli-interactive
   az login
   ```

2. 此示例要授予 Azure 虚拟机对存储帐户的访问权限。 首先，我们使用 [az resource list](/cli/azure/resource/#az_resource_list) 获取名为“myVM”的虚拟机的服务主体：

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   对于 Azure 虚拟机规模集，使用的命令相同，但获取名为“DevTestVMSS”的虚拟机规模集的服务主体：
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. 获得服务主体 ID 后，立即使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)授予虚拟机或虚拟机规模集对“myStorageAcct”存储帐户的“读者”访问权限：

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>故障排除

如果资源的 MSI 未显示在可用标识列表中，请验证是否已正确启用 MSI。 在此示例中，我们可以在 [Azure 门户](https://portal.azure.com)中返回到 Azure 虚拟机或虚拟机规模集并执行以下操作：

- 查看“配置”页，并确保“MSI 已启用”配置为“是”。
- 查看“扩展”页，并确保已成功部署 MSI 扩展（“扩展”页不可用于 Azure 虚拟机规模集）。

如果有任一项不正确，可能都需要在资源上再次重新部署 MSI，或排查部署故障。

## <a name="related-content"></a>相关内容

- 有关 MSI 的概述，请参阅[托管服务标识概述](overview.md)。
- 若要在 Azure 虚拟机上启用 MSI，请参阅[使用 Azure CLI 配置 Azure VM 托管服务标识 (MSI)](qs-configure-cli-windows-vm.md)。
- 若要在 Azure 虚拟机规模集上启用 MSI，请参阅[使用 Azure 门户配置 Azure 虚拟机规模集托管服务标识 (MSI)](qs-configure-portal-windows-vmss.md)

使用以下评论部分提供反馈，帮助我们改进内容。

