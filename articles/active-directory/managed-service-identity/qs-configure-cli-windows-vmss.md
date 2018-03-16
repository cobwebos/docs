---
title: "使用 Azure CLI 在 Azure 虚拟机规模集上配置 MSI"
description: "分步说明如何使用 Azure CLI 在 Azure 虚拟机规模集上配置托管服务标识 (MSI)。"
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: d7a7b0c8b3f9bf0279282dbf1fed4fc8163d9170
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>使用 Azure CLI 配置虚拟机规模集托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文介绍如何使用 Azure CLI 为 Azure 虚拟机规模集启用和删除 MSI。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

若要运行 CLI 脚本示例，可以使用下列三种方法：

- 在 Azure 门户中使用 [Azure Cloud Shell](../../cloud-shell/overview.md)（见下一部分）。
- 单击各代码块右上角的“试运行”按钮，使用嵌入的 Azure Cloud Shell。
- 如果希望使用本地 CLI 控制台，可以[安装最新版 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.13 或更高版本）。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>在创建 Azure 虚拟机规模集的过程中启用 MSI

若要创建启用 MSI 的虚拟机规模集，请执行以下操作：

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure。 使用与要在其下部署虚拟机规模集的 Azure 订阅关联的帐户：

   ```azurecli-interactive
   az login
   ```

2. 使用 [az group create](/cli/azure/group/#az_group_create)，创建用于容纳和部署虚拟机规模集及其相关资源的[资源组](../../azure-resource-manager/resource-group-overview.md#terminology)。 如果已有要改用的资源组，可以跳过这一步：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. 使用 [az vmss create](/cli/azure/vmss/#az_vmss_create) 创建虚拟机规模集。 下面的示例创建名为 myVMSS 且已启用 MSI 的虚拟机规模集（应 `--assign-identity` 参数的要求）。 `--admin-username` 和 `--admin-password` 参数指定用于登录虚拟机的管理用户名和密码帐户。 针对自己的环境相应地更新这些值： 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>在现有 Azure 虚拟机规模集上启用 MSI

如果需要在现有 Azure 虚拟机规模集上启用 MSI，请执行以下操作：

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure。 使用与包含虚拟机规模集的 Azure 订阅关联的帐户。

   ```azurecli-interactive
   az login
   ```

2. 结合使用 [az vmss assign-identity](/cli/azure/vm/#az_vmss_assign_identity) 与 `--assign-identity` 参数，将 MSI 添加到现有 VM：

   ```azurecli-interactive
   az vmss assign-identity -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>从 Azure 虚拟机规模集中删除 MSI

如果虚拟机规模集不再需要 MSI，请执行以下操作：

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login](/cli/azure/reference-index#az_login) 登录到 Azure。 使用与包含虚拟机规模集的 Azure 订阅关联的帐户。

   ```azurecli-interactive
   az login
   ```

2. 结合使用 `--identities` 开关与 [az vmss remove-identity](/cli/azure/vmss/#az_vmss_remove_identity)，删除 MSI：

   ```azurecli-interactive
   az vmss remove-identity -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>后续步骤

- [托管服务标识概述](overview.md)
- 有关完整的 Azure 虚拟机规模集创建快速入门，请参阅： 

  - [使用 CLI 创建虚拟机规模集](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

使用以下评论部分提供反馈，帮助我们改进内容。
















