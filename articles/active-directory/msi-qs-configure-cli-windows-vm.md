---
title: "如何使用 Azure CLI 在 Azure VM 上配置 MSI"
description: "逐步介绍了如何使用 Azure CLI 在 Azure VM 上配置托管服务标识 (MSI)。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 03fb9cc6633f81e284ae299f7b2ba4018d19cc73
ms.contentlocale: zh-cn
ms.lasthandoff: 09/23/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>使用 Azure CLI 配置 VM 托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文将介绍如何使用 Azure CLI 为 Azure VM 启用和删除 MSI。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

若要运行 CLI 脚本示例，可以使用下列三种方法：

- 在 Azure 门户中使用 [Azure Cloud Shell](../cloud-shell/overview.md)（见下一部分）。
- 单击各代码块右上角的“试运行”按钮，使用嵌入的 Azure Cloud Shell。
- 如果希望使用本地 CLI 控制台，可以[安装最新版 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（2.0.13 或更高版本）。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>在 Azure VM 创建过程中启用 MSI

若要创建启用 MSI 的 VM，请执行以下操作：

1. 如果使用的不是 Azure 门户中的 Azure Cloud Shell，请先运行 [az login](/cli/azure/#login) 登录 Azure。 使用与要在其下部署 VM 的 Azure 订阅关联的帐户：

   ```azurecli-interactive
   az login
   ```

2. 运行 [az group create](/cli/azure/group/#create)，创建用于容纳和部署 VM 及其相关资源的[资源组](../azure-resource-manager/resource-group-overview.md#terminology)。 如果已有要改用的资源组，可以跳过这一步：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. 运行 [az vm create](/cli/azure/vm/#create) 创建 VM。 下面的示例创建名为 myVM 且已启用 MSI 的 VM（应 `--assign-identity` 参数的要求）。 `--admin-username` 和 `--admin-password` 参数指定用于登录虚拟机的管理用户名和密码帐户。 针对自己的环境相应地更新这些值： 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>在现有 Azure VM 上启用 MSI

如果需要在现有虚拟机上启用 MSI，请执行以下操作：

1. 如果使用的不是 Azure 门户中的 Azure Cloud Shell，请先运行 [az login](/cli/azure/#login) 登录 Azure。 使用与包含 VM 的 Azure 订阅关联的帐户。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”：

   ```azurecli-interactive
   az login
   ```

2. 结合使用 [az vm assign-identity](/cli/azure/vm/#az_vm_assign_identity) 与 `--assign-identity` 参数，将 MSI 添加到现有 VM：

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>在 Azure VM 上禁用 MSI

如果虚拟机不再需要 MSI，请执行以下操作：

1. 如果使用的不是 Azure 门户中的 Azure Cloud Shell，请先运行 [az login](/cli/azure/#login) 登录 Azure。 使用与包含 VM 的 Azure 订阅关联的帐户。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”：

   ```azurecli-interactive
   az login
   ```

2. 将 `-n ManagedIdentityExtensionForWindows` 或 `-n ManagedIdentityExtensionForLinux` 开关（具体取决于 VM 的类型）与 [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity) 配合使用以删除 MSI：

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>相关内容

- [托管服务标识概述](msi-overview.md)
- 有关完整的 Azure VM 创建快速入门，请参阅： 

  - [使用 CLI 创建 Windows 虚拟机](../virtual-machines/windows/quick-create-cli.md)  
  - [使用 CLI 创建 Linux 虚拟机](../virtual-machines/linux/quick-create-cli.md) 

使用以下评论部分提供反馈，帮助我们改进内容。

















