---
title: 如何在 Azure VMSS 上使用 REST 配置系统和用户分配的标识
description: 分步说明如何在 Azure VMSS 上通过使用 CURL 进行 REST API 调用来配置系统和用户分配的标识。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/25/2018
ms.author: daveba
ms.openlocfilehash: 704342db2d1ff56a66eb70b33d3da10874844f2d
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2018
ms.locfileid: "42140628"
---
# <a name="configure-managed-identity-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>使用 REST API 调用在虚拟机规模集上配置托管标识

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管标识为 Azure 服务提供了 Azure Active Directory 中的自动托管系统标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文将介绍如何在 Azure 虚拟机规模集上通过使用 CURL 对 Azure 资源管理器 REST 终结点进行调用来执行以下托管标识操作：

- 在 Azure 虚拟机规模集上启用和禁用系统分配的标识
- 在 Azure 虚拟机规模集上添加和删除用户分配的标识

## <a name="prerequisites"></a>先决条件

- 如果不熟悉托管服务标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配标识与用户分配标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要执行本文中的管理操作，帐户需要分配以下角色：
    - [虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)，可创建虚拟机规模集，并从虚拟机规模集启用和删除系统和/或用户分配的托管标识。
    - [托管标识参与者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色，可以创建用户分配的标识。
    - [托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色，可在虚拟机规模集中分配和删除用户分配标识。
- 如果使用 Windows，请安装[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)或使用 Azure 门户中的 [Azure Cloud Shell](../../cloud-shell/overview.md)。
- 如果使用[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)或 [Linux 分发版 OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)，请[安装 Azure CLI 本地控制台](/azure/install-azure-cli)。
- 如果使用 Azure CLI 本地控制台，请使用 `az login` 和与要用于管理系统或用户分配的标识的 Azure 订阅关联的帐户登录。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>系统分配标识

本节将介绍如何在 Azure 虚拟机规模集上通过使用 CURL 对 Azure 资源管理器 REST 终结点进行调用来启用和禁用系统分配的标识。

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>在创建虚拟机规模集的过程中启用系统分配的标识

要创建启用了系统分配的标识的虚拟机规模集，则需要创建虚拟机规模集并检索访问令牌，使用 CURL 通过系统分配的标识类型值对资源管理器终结点进行调用。

1. 使用 [az group create](/cli/azure/group/#az-group-create)，创建用于容纳和部署虚拟机规模集及其相关资源的[资源组](../../azure-resource-manager/resource-group-overview.md#terminology)。 如果已有要改用的资源组，可以跳过这一步：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. 为虚拟机规模集创建[网络接口](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)：

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的虚拟机规模集。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 通过使用 CURL 对 Azure 资源管理器 REST 终结点进行调用，创建虚拟机规模集。 下面的示例在 “myResourceGroup”中创建名为“myVMSS”的虚拟机规模集，该规模集具有系统分配的标识（请求正文中用值 `"identity":{"type":"SystemAssigned"}` 进行标识）。 请将 `<ACCESS TOKEN>` 替换为上一步中请求持有者访问令牌和适合环境的 `<SUBSCRIPTION ID>` 值时收到的值。
 
    ```bash   
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>在现有 Azure 虚拟机规模集上启用系统分配标识

要在现有虚拟机规模集上启用系统分配的标识，需要获取访问令牌，然后使用 CURL 对资源管理器 REST 终结点进行调用以更新标识类型。

1. 检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的虚拟机规模集。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 使用以下 CURL 命令对 Azure 资源管理器 REST 终结点进行调用，为名为“myVMSS”的虚拟机规模集启用系统分配的标识（在请求正文中用值 `{"identity":{"type":"SystemAssigned"}` 进行标识）。  请将 `<ACCESS TOKEN>` 替换为上一步中请求持有者访问令牌和适合环境的 `<SUBSCRIPTION ID>` 值时收到的值。
   
   > [!IMPORTANT]
   > 若要确保不删除用户分配给虚拟机规模集的任何现有托管标识，需要使用以下 CURL 命令列出用户分配的标识：`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`。 如果具有用户分配给虚拟机规模集的任何标识（响应中用值 `identity` 进行标识），请跳过步骤 3，该步骤介绍了如何在虚拟机规模集上启用系统分配的标识的同时保留用户分配的标识。

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. 要在具有现有用户分配的标识的虚拟机规模集上启用系统分配的标识，需要将 `SystemAssigned` 添加到 `type` 值。  
   
   例如，如果虚拟机规模集具有用户分配给它的标识 `ID1` 和 `ID2`，并且你希望向该虚拟机规模集添加系统分配的标识，请使用以下 CURL 调用。 将 `<ACCESS TOKEN>` 和 `<SUBSCRIPTION ID>` 替换为适合环境的值。

   API 版本 `2018-06-01` 以字典格式将用户分配的标识存储在 `userAssignedIdentities` 值中，而 API 版本 `2017-12-01` 及早期版本以数组格式将其存储在 `identityIds` 值中。
   
   **API 版本 2018-06-01**

   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```
   
   **API 版本 2017-12-01 及早期版本**

   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>从 Azure 虚拟机规模集中禁用系统分配标识

要在现有虚拟机规模集上禁用系统分配的标识，需要获取访问令牌，然后使用 CURL 对资源管理器 REST 终结点进行调用以将标识类型更新为 `None`。

1. 检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的虚拟机规模集。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 通过使用 CURL 对 Azure 资源管理器 REST 终结点进行调用以禁用系统分配的标识，更新虚拟机规模集。  下面的示例从名为“myVMSS”的虚拟机规模集上禁用系统分配的标识（在请求正文中用值 `{"identity":{"type":"None"}}` 进行标识）。  请将 `<ACCESS TOKEN>` 替换为上一步中请求持有者访问令牌和适合环境的 `<SUBSCRIPTION ID>` 值时收到的值。

   > [!IMPORTANT]
   > 若要确保不删除用户分配给虚拟机规模集的任何现有托管标识，需要使用以下 CURL 命令列出用户分配的标识：`curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`。 如果具有用户分配给虚拟机规模集的任何标识，请跳过步骤 3，该步骤介绍了如何在从虚拟机规模集中删除系统分配的标识的同时保留用户分配的标识。

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. 如果使用的是 **API 版本 2018-06-01**，若要从具有用户分配的标识的虚拟机规模集中删除系统分配的标识，请从 `{"identity":{"type:" "}}` 值中删除 `SystemAssigned`，同时保留 `UserAssigned` 值和 `userAssignedIdentities` 字典值。 如果使用的是 **API 版本 2017-12-01** 或早期版本，请保留 `identityIds` 数组。

## <a name="user-assigned-identity"></a>用户分配标识

本节将介绍如何在 Azure 虚拟机规模集上通过使用 CURL 对 Azure 资源管理器 REST 终结点进行调用来添加和删除用户分配的标识。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>在创建虚拟机规模集的过程中分配用户分配的标识

1. 检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的虚拟机规模集。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 为虚拟机规模集创建[网络接口](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)：

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的虚拟机规模集。

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. 按照此处的说明创建用户分配的标识：[创建用户分配的托管标识](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)。

5. 通过使用 CURL 对 Azure 资源管理器 REST 终结点进行调用，创建虚拟机规模集。 下面的示例在资源组“myResourceGroup”中创建名为“myVMSS”的虚拟机规模集，该规模集具有用户分配的标识 `ID1`（请求正文中用值 `"identity":{"type":"UserAssigned"}` 进行标识）。 请将 `<ACCESS TOKEN>` 替换为上一步中请求持有者访问令牌和适合环境的 `<SUBSCRIPTION ID>` 值时收到的值。
 
   **API 版本 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   **API 版本 2017-12-01 及早期版本**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-virtual-machine-scale-set"></a>将用户分配的标识分配到现有 Azure 虚拟机规模集

1. 检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的虚拟机规模集。

   ```azurecli-interactive
   az account get-access-token
   ```

2.  按照此处的说明创建用户分配的标识：[创建用户分配的托管标识](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)。

3. 若要确保不删除用户或系统分配给虚拟机规模集的现有托管标识，需要使用以下 CURL 命令列出分配给虚拟机规模集的标识。 如果具有分配给虚拟机规模集的托管标识，则这些标识会在 `identity` 值中列出。
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

4. 如果没有用户或系统分配给虚拟机规模集的任何托管标识，请使用以下 CURL 命令对 Azure 资源管理器 REST 终结点进行调用，以将第一个用户分配的标识分配给虚拟机规模集。  如果具有用户或系统分配给虚拟机规模集的标识，请跳过步骤 5，该步骤介绍了如何将多个用户分配的标识添加到虚拟机规模集并同时保留系统分配的标识。

   下面的示例将用户分配的标识 `ID1` 分配给 资源组“myResourceGroup”中名为“myVMSS”的虚拟机规模集。  请将 `<ACCESS TOKEN>` 替换为上一步中请求持有者访问令牌和适合环境的 `<SUBSCRIPTION ID>` 值时收到的值。

   **API 版本 2018-06-01**

    ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   
    
   **API 版本 2017-12-01 及早期版本**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. 如果具有用户或系统分配给虚拟机规模集的现有标识，则：
   
   **API 版本 2018-06-01**

   将用户分配的标识添加到 `userAssignedIdentities` 字典值。

   例如，如果你具有当前分配给虚拟机规模集的系统分配的标识和用户分配的标识 `ID1` 并希望将用户分配的标识 `ID2` 添加到该虚拟机规模集，则：

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API 版本 2017-12-01 及早期版本**

   在 `identityIds` 数组值中保留要保持的用户分配的标识，同时添加新的用户分配的标识。

   例如，如果你具有当前分配给虚拟机规模集的系统分配的标识和用户分配的标识 `ID1` 并希望将用户分配的标识 `ID2` 添加到该虚拟机规模集，则： 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>从虚拟机规模集中删除用户分配的标识

1. 检索持有者访问令牌，下一步在授权标头中将使用该令牌创建具有系统分配的托管标识的虚拟机规模集。

   ```azurecli-interactive
   az account get-access-token
   ```

2. 若要确保不删除任何现有用户分配的托管标识（希望保留在虚拟机规模集上）或不删除系统分配的标识，需要使用以下 CURL 命令列出这些托管标识： 
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```
   
   如果具有分配给 VM 的托管标识，则这些标识会在 `identity` 值下列出。 
    
   例如，如果你有分配给虚拟机规模集的用户分配的标识 `ID1` 和 `ID2`，并且仅希望保持分配 `ID1` 并保留系统分配的标识：

   **API 版本 2018-06-01**

   将 `null` 添加到要删除的用户分配的标识：

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API 版本 2017-12-01 及早期版本**

   在 `identityIds` 数组中仅保留要保持的用户分配的标识：   

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

如果虚拟机规模集同时具有系统分配的标识和用户分配的标识，则可使用以下命令切换为仅使用系统分配的标识，删除所有用户分配的标识：

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
如果虚拟机规模集只具有用户分配的标识并希望删除所有这些标识，请使用以下命令：

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>后续步骤

有关如何使用 REST 创建、列出或删除用户分配的标识，请参阅：

- [使用 REST API 调用创建、列出或删除用户分配的标识](how-to-manage-ua-identity-rest.md)