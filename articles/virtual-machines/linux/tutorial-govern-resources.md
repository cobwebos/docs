---
title: "使用 Azure CLI 控制 Azure 虚拟机 | Microsoft Docs"
description: "教程 - 通过使用 Azure CLI 应用 RBAC、策略、锁和标记管理 Azure 虚拟机"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 86ac3ec390c7aa9bc24a90ef2ee582f97f8b5407
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="virtual-machine-governance-with-azure-cli"></a>使用 Azure CLI 控制虚拟机

[!include[Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

若要在本地安装和使用 CLI，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。

## <a name="understand-scope"></a>了解范围

[!include[Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

在本教程中，你将所有管理设置应用于一个资源组，以便在完成后可以轻松地删除这些设置。

让我们创建该资源组。

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

目前，资源组为空。

## <a name="role-based-access-control"></a>基于角色的访问控制

你希望确保你的组织中的用户对这些资源具有合适级别的访问权限。 你不希望向用户授予不受限的访问权限，但还需要确保他们可以执行其工作。 使用[基于角色的访问控制](../../active-directory/role-based-access-control-what-is.md)，你可以管理哪些用户有权在某个范围内完成特定操作。

若要创建和删除角色分配，用户必须具有 `Microsoft.Authorization/roleAssignments/*` 访问权限。 此访问权限是通过“所有者”或“用户访问”管理员角色授权的。

若要管理虚拟机解决方案，可以使用三种特定于资源的角色来进行通常所需的访问：

* [虚拟机参与者](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [网络参与者](../../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [存储帐户参与者](../../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

通常情况下，与其向单个用户分配角色，不如为需要进行相似操作的用户[创建一个 Azure Active Directory 组](../../active-directory/active-directory-groups-create-azure-portal.md)， 然后向该组分配相应的角色。 为了简单起见，本文创建一个没有成员的 Azure Active Directory 组。 仍然可以为该组分配一个负责某个范围的角色。 

以下示例创建一个邮件别名为 *vmDemoGroup* 且名为 *VMDemoContributors* 的 Azure Active Directory 组。 邮件别名用作组的别名。

```azurecli-interactive
adgroupId=$(az ad group create --display-name VMDemoContributors --mail-nickname vmDemoGroup --query objectId --output tsv)
```

在命令提示返回后，组需要花费一段时间来在整个 Azure Active Directory 中传播。 等待 20 或 30 秒后，使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 命令将新的 Azure Active Directory 组分配到资源组的“虚拟机参与者”角色。  如果在它已传播之前运行以下命令，则会收到一个错误，指出**主体<guid>在目录中不存在**。 请尝试再次运行命令。

```azurecli-interactive
az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

通常情况下，请对*网络参与者*和*存储帐户参与者*重复执行此过程，确保分配用户来管理已部署的资源。 在本文中，可以跳过这些步骤。

## <a name="azure-policies"></a>Azure 策略

[!include[Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>应用策略

订阅已经有多个策略定义。 若要查看可用的策略定义，请使用 [az policy definition list](/cli/azure/policy/definition#az_policy_definition_list) 命令：

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

可以看到现有的策略定义。 策略类型为“内置”或“自定义”。 在这些定义中查找所述条件正是你要分配的条件的定义。 在本文中，分配的策略要符合以下条件：

* 限制所有资源的位置。
* 限制虚拟机的 SKU。
* 审核不使用托管磁盘的虚拟机。

在下面的示例中，你将基于显示名称检索三个策略定义。 并且使用 [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) 命令将这些定义分配到资源组。 对于某些策略，你将提供参数值来指定允许的值。

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

前面的示例假定你已知道了策略的参数。 如果需要查看参数，请使用：

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>部署虚拟机

分配角色和策略以后，即可部署解决方案。 默认大小为 Standard_DS1_v2，这是允许的 SKU 之一。 如果默认位置中不存在 SSH 密钥，则此命令会创建这些密钥。

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

部署完成后，可以对解决方案应用更多的管理设置。

## <a name="lock-resources"></a>锁定资源

[资源锁](../../azure-resource-manager/resource-group-lock-resources.md)可以防止组织中的用户意外删除或修改重要资源。 与基于角色的访问控制不同，资源锁对所有用户和角色应用限制。 可以将锁定级别设置为 *CanNotDelete* 或 *ReadOnly*。

若要创建或删除管理锁，必须有权执行 `Microsoft.Authorization/locks/*` 操作。 在内置角色中，只有“所有者”和“用户访问管理员”有权执行这些操作。

若要锁定虚拟机和网络安全组，请使用 [az lock create](/cli/azure/lock#az_lock_create) 命令：

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

若要测试锁，请尝试运行以下命令：

```azurecli-interactive 
az group delete --name myResourceGroup
```

将会显示一个错误，指出删除操作由于某个锁而无法执行。 只有在明确删除锁以后，才能删除资源组。 该步骤显示在[清理资源](#clean-up-resources)中。

## <a name="tag-resources"></a>标记资源

可以将[标记](../../azure-resource-manager/resource-group-using-tags.md)应用于 Azure 资源，以逻辑方式按类别对其进行组织。 每个标记包含一个名称和一个值。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

[!include[Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

若要将标记应用于虚拟机，请使用 [az resource tag](/cli/azure/resource#az_resource_tag) 命令。 资源上的任何现有标记都不会保留。

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>按标记查找资源

若要通过标记名称和值查找资源，请使用 [az resource list](/cli/azure/resource#az_resource_list) 命令：

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

可以将返回的值用于管理任务，例如停止带有某个标记值的所有虚拟机。

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>按标记值查看成本

[!include[Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>清理资源

在解除锁定之前，不能删除锁定的网络安全组。 若要删除锁，请检索锁的 ID，并将其提供给 [az lock delete](/cli/azure/lock#az_lock_delete) 命令：

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

如果不再需要资源组、VM 和所有相关的资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令将其删除。 退出 SSH 会话，返回 VM，然后删除资源，如下所示：

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>后续步骤

在本教程中，已创建自定义 VM 映像。 你已了解如何：

> [!div class="checklist"]
> * 为用户分配角色
> * 应用强制实施标准的策略
> * 使用锁保护重要资源
> * 标记用于计费和管理的资源

请转到下一教程，了解如何创建高度可用的虚拟机。

> [!div class="nextstepaction"]
> [监视虚拟机](tutorial-monitoring.md)

