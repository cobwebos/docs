---
title: 将资源移动到新的订阅或资源组
description: 使用 Azure Resource Manager 将资源移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cd05fe045532ee1b1f1fb88e502d786daabf9365
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319548"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>将资源移到新的资源组或订阅

本文说明了如何将 Azure 资源移动到另一 Azure 订阅，或移动到同一订阅下的另一资源组。 可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 移动资源。

在移动操作过程中，源组和目标组都会锁定。 在完成移动之前，将阻止对资源组执行写入和删除操作。 此锁意味着将无法添加、更新或删除资源组中的资源。 这并不意味着资源已被冻结。 例如，如果将 SQL Server 及其数据库移到新的资源组中，使用数据库的应用程序体验不到停机， 仍可读取和写入到数据库。 锁定时间最长可达四小时，但大多数移动所需的完成时间要少得多。

移动资源只会将其移到新的资源组或订阅。 它不会更改资源的位置。

## <a name="checklist-before-moving-resources"></a>移动资源前需查看的清单

移动资源之前需执行的一些重要步骤。 验证这些条件可以避免错误。

1. 要移动的资源必须支持移动操作。 有关支持移动的资源列表，请参阅[资源的移动操作支持](move-support-resources.md)。

1. 某些服务在移动资源时有特定的限制或要求。 如果要移动以下任何服务，请在移动之前查看该指南。

   * 如果使用 Azure Stack Hub，则无法在组之间移动资源。
   * [应用程序服务移动指南](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services 移动指南](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [经典部署模型移动指南](./move-limitations/classic-model-move-limitations.md) - 经典计算、经典存储、经典虚拟网络和云服务
   * [网络移动指南](./move-limitations/networking-move-limitations.md)
   * [恢复服务移动指南](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [虚拟机移动指南](./move-limitations/virtual-machines-move-limitations.md)

1. 如果将已分配 Azure 角色的资源直接分配给资源 (或子资源) ，则不会移动角色分配，也不会将其变成孤立角色。 移动之后，必须重新创建角色分配。 最终，将自动删除孤立角色分配，但最佳做法是在移动资源之前删除角色分配。

    有关如何管理角色分配的信息，请参阅 [列出 azure 角色分配](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) 以及 [添加或删除 azure 角色分配](../../role-based-access-control/role-assignments-portal.md)。

1. 源订阅和目标订阅必须处于活动状态。 如果在启用已禁用的帐户时遇到问题，请[创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 选择“订阅管理”  作为问题类型。

1. 源订阅与目标订阅必须在同一个 [Azure Active Directory 租户](../../active-directory/develop/quickstart-create-new-tenant.md)中。 若要检查这两个订阅是否具有相同的租户 ID，请使用 Azure PowerShell 或 Azure CLI。

   对于 Azure PowerShell，请使用：

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   对于 Azure CLI，请使用：

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   如果源订阅和目标订阅的租户 ID 不相同，可使用以下方法协调租户 ID：

   * [将 Azure 订阅所有权转让给其他帐户](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [如何将 Azure 订阅关联或添加到 Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 必须针对要移动的资源的资源提供程序注册目标订阅。 否则，会收到错误，指明 **未针对资源类型注册订阅**。 将资源移到新的订阅时，可能会看到此错误，但该订阅从未配合该资源类型使用。

   对于 PowerShell，请使用以下命令来获取注册状态：

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   若要注册资源提供程序，请使用：

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   对于 Azure CLI，请使用以下命令来获取注册状态：

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   若要注册资源提供程序，请使用：

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. 移动资源的帐户至少需要具备下列权限：

   * 源资源组上的 Microsoft.Resources/subscriptions/resourceGroups/moveResources/action  权限。
   * 目标资源组上的 Microsoft.Resources/subscriptions/resourceGroups/write  权限。

1. 在移动资源之前，请检查要将资源移动到的订阅的订阅配额。 如果移动资源意味着订阅将超出其限制，则需要检查是否可以请求增加配额。 有关限制的列表及如何请求增加配额的信息，请参阅 [Azure 订阅和服务限制、配额与约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

1. **若要跨订阅移动，则资源及其依赖资源必须位于同一资源组中，并且必须一起移动。** 例如，如果 VM 带有托管磁盘，则 VM 和托管磁盘以及其他依赖资源必须一起移动。

   如果要将某个资源移到新的订阅，请进行检查，看看该资源是否有任何依赖资源，以及它们是否位于同一资源组中。 如果这些资源不在同一资源组中，请看看能否将这些资源组合到同一资源组中。 如果可以，请跨资源组使用一个移动操作，将所有这些资源并入同一资源组。

   有关详细信息，请参阅[跨订阅移动方案](#scenario-for-move-across-subscriptions)。

## <a name="scenario-for-move-across-subscriptions"></a>跨订阅移动方案

将资源从一个订阅移到另一个订阅分为三步：

![跨订阅移动方案](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

为了演示方便，我们只有一个依赖资源。

* 步骤 1：如果依赖资源分布在不同的资源组中，请先将它们移到一个资源组。
* 步骤 2：将资源和依赖资源一起从源订阅移到目标订阅。
* 步骤 3：也可将依赖资源重新分布到目标订阅中的不同资源组。

## <a name="validate-move"></a>验证移动

[验证移动操作](/rest/api/resources/resources/validatemoveresources)可以测试你的移动方案而无需实际移动资源。 使用此操作检查移动是否会成功。 发送移动请求时会自动调用验证。 仅当需要预先确定结果时才使用此操作。 若要运行此操作，需要：

* 源资源组的名称
* 目标资源组的资源 ID
* 要移动的每个资源的资源 ID
* 你的帐户的[访问令牌](/rest/api/azure/#acquire-an-access-token)

发送以下请求：

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

包含请求正文：

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

如果请求格式正确，则操作将返回：

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 状态代码指示已接受验证请求，但尚未确定移动操作是否会成功。 `location` 值包含用于检查长时间运行操作的状态的 URL。  

若要检查状态，请发送以下请求：

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

操作仍在运行时，会继续收到 202 状态代码。 请等待 `retry-after` 值中所示的秒数，然后重试。 如果移动操作验证成功，则会收到 204 状态代码。 如果移动验证失败，则会收到错误消息，例如：

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>使用门户

若要移动资源，请选择包含那些资源的资源组。

当你查看资源组时，移动选项处于禁用状态。

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="移动选项处于禁用状态&quot;:::

若要启用移动选项，请选择要移动的资源。 若要选择所有资源，请选中列表顶部的复选框。 或者，分别选择各个资源。 选择 &quot;资源&quot; 后，&quot;移动" 选项处于启用状态。

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="移动选项处于禁用状态&quot;:::

若要启用移动选项，请选择要移动的资源。 若要选择所有资源，请选中列表顶部的复选框。 或者，分别选择各个资源。 选择 &quot;资源&quot; 后，&quot;移动":::

选择“移动”按钮

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="移动选项处于禁用状态&quot;:::

若要启用移动选项，请选择要移动的资源。 若要选择所有资源，请选中列表顶部的复选框。 或者，分别选择各个资源。 选择 &quot;资源&quot; 后，&quot;移动":::

此按钮提供了三个选项：

* 移动到新的资源组。
* 移动到新的订阅。
* 移动到新的区域。 若要更改区域，请参阅 [从资源组) 在区域之间移动资源 (](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json)。

选择是要将资源移到新资源组还是新订阅。

选择目标资源组。 确认需要更新这些资源的脚本，选择“确定”  。 如果你选择了移动到新的订阅，则还必须选择目标订阅。

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="移动选项处于禁用状态&quot;:::

若要启用移动选项，请选择要移动的资源。 若要选择所有资源，请选中列表顶部的复选框。 或者，分别选择各个资源。 选择 &quot;资源&quot; 后，&quot;移动":::

验证资源是否可以移动后，你会看到一个通知，指示移动操作正在运行。

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="移动选项处于禁用状态&quot;:::

若要启用移动选项，请选择要移动的资源。 若要选择所有资源，请选中列表顶部的复选框。 或者，分别选择各个资源。 选择 &quot;资源&quot; 后，&quot;移动":::

操作完成后，你会获得结果通知。

如果出现错误，请参阅[排查将 Azure 资源移到新的资源组或订阅时遇到的问题](troubleshoot-move.md)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

要将现有资源移到另一个资源组或订阅，请使用 [Move-AzResource](/powershell/module/az.resources/move-azresource) 命令。 下面的示例演示了如何将多个资源移动到新的资源组。

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

若要移到新订阅，请包含 `DestinationSubscriptionId` 参数的值。

如果出现错误，请参阅[排查将 Azure 资源移到新的资源组或订阅时遇到的问题](troubleshoot-move.md)。

## <a name="use-azure-cli"></a>使用 Azure CLI

若要将现有资源移动到另一个资源组或订阅，请使用 [az resource move](/cli/azure/resource#az-resource-move) 命令。 提供要移动的资源的资源 ID。 下面的示例演示了如何将多个资源移动到新的资源组。 在 `--ids` 参数中，提供要移动的资源 ID 的空格分隔列表。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

若要移到新订阅，请提供 `--destination-subscription-id` 参数。

如果出现错误，请参阅[排查将 Azure 资源移到新的资源组或订阅时遇到的问题](troubleshoot-move.md)。

## <a name="use-rest-api"></a>使用 REST API

要将现有资源移到另一个资源组或订阅，请使用[移动资源](/rest/api/resources/Resources/MoveResources)操作。

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

在请求正文中，指定目标资源组和要移动的资源。

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

如果出现错误，请参阅[排查将 Azure 资源移到新的资源组或订阅时遇到的问题](troubleshoot-move.md)。

## <a name="frequently-asked-questions"></a>常见问题

**问：我的资源移动操作通常只需几分钟的时间，但这次已运行了几乎一个小时。出现了错误？**

移动资源是一项复杂的操作，包含不同的阶段。 它不仅仅涉及你尝试移动的资源的资源提供程序。 由于资源提供程序之间的依赖关系，Azure 资源管理器允许在 4 小时内完成操作。 此时间段为资源提供程序提供了从暂时性问题中恢复的机会。 如果你的移动请求还在四小时期间内，则操作会继续尝试完成，并且仍可能会成功。 源和目标资源组在这段时间内被锁定，以避免出现一致性问题。

**问：为什么我的资源组在资源移动期间锁定了四个小时？**

最多允许移动请求花费四个小时来完成。 为了防止对正在移动的资源进行修改，在资源移动期间，源资源组和目标资源组都将被锁定。

移动请求中有两个阶段。 在第一阶段中，将移动资源。 在第二阶段中，将向依赖于被移动资源的其他资源提供程序发送通知。 如果某个资源提供程序在任一阶段失败，则资源组可能会被锁定整整四个小时。 在允许的时间内，资源管理器将重试失败的步骤。

如果资源无法在四小时内移动，则资源管理器会解除对这两个资源组的锁定。 已成功移动的资源位于目标资源组中。 未能移动的资源留在源资源组中。

**问：资源移动过程中锁定的源和目标资源组有哪些影响？**

锁定会阻止你删除任一资源组，阻止在任一资源组中创建新资源或删除移动中涉及的任何资源。

下图显示了当用户尝试删除正在移动的资源组时来自 Azure 门户的错误消息。

![移动错误消息“正在尝试删除”](./media/move-resource-group-and-subscription/move-error-delete.png)

**问：错误代码“MissingMoveDependentResources”的含义是什么？**

移动资源时，依赖的资源必须存在于目标资源组或订阅中，或者包含在移动请求中。 当依赖的资源不满足此要求时，你将收到 MissingMoveDependentResources 错误代码。 错误消息包含有关需要在移动请求中包括的依赖资源的详细信息。

例如，移动某个虚拟机可能需要移动具有三个不同资源提供程序的七个资源类型。 这些资源提供程序和类型为：

* Microsoft.Compute

  * virtualMachines
  * disks
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

另一个常见示例涉及移动虚拟网络。 可能必须移动与该虚拟网络关联的多个其他资源。 移动请求可能要求移动公共 IP 地址、路由表、虚拟网络网关、网络安全组和其他项。

**问：为何无法移动 Azure 中的某个资源？**

目前，并非 Azure 中的所有资源都支持移动。 有关支持移动的资源列表，请参阅[资源的移动操作支持](move-support-resources.md)。

## <a name="next-steps"></a>后续步骤

有关支持移动的资源列表，请参阅[资源的移动操作支持](move-support-resources.md)。
