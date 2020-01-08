---
title: 将资源移到新的订阅或资源组
description: 使用 Azure 资源管理器将资源移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 60c2046b1bbdc92c9b63c748c7d758659abfbe46
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659423"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>将资源移到新的资源组或订阅

本文说明了如何将 Azure 资源移动到另一 Azure 订阅，或移动到同一订阅下的另一资源组。 可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 移动资源。

在移动操作过程中，源组和目标组都会锁定。 在完成移动之前，将阻止对资源组执行写入和删除操作。 此锁意味着无法添加、更新或删除资源组中的资源。 这并不意味着资源已冻结。 例如，如果将 SQL Server 及其数据库移动到新的资源组中，则使用该数据库的应用程序将不会遇到停机的情况。 它仍可读取和写入到数据库。 锁定时间最长可达四小时，但大多数移动完成的时间将少得多。

移动资源仅能够将其移动到新的资源组或订阅中。 但不会更改该资源的位置。

## <a name="checklist-before-moving-resources"></a>移动资源前需查看的清单

移动资源之前需执行的一些重要步骤。 验证这些条件可以避免错误。

1. 要移动的资源必须支持移动操作。 有关支持移动的资源的列表，请参阅[移动操作对资源的支持](move-support-resources.md)。

1. 某些服务在移动资源时有特定的限制或要求。 如果你移动了以下任何服务，请在移动之前查看此指南。

   * [应用服务移动指南](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services 移动指南](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [经典部署模型移动指南](./move-limitations/classic-model-move-limitations.md)-经典计算、经典存储、经典虚拟网络和云服务
   * [网络移动指南](./move-limitations/networking-move-limitations.md)
   * [恢复服务移动指南](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [虚拟机移动指南](./move-limitations/virtual-machines-move-limitations.md)

1. 源订阅和目标订阅必须处于活动状态。 如果在启用已禁用的帐户时遇到问题，请[创建 Azure 支持请求](../../azure-supportability/how-to-create-azure-support-request.md)。 选择“订阅管理”作为问题类型。

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

   * [将 Azure 订阅所有权转让给其他帐户](../../billing/billing-subscription-transfer.md)
   * [如何将 Azure 订阅关联或添加到 Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 必须针对要移动的资源的资源提供程序注册目标订阅。 否则会出现错误“未针对资源类型注册订阅”。 将资源移到新的订阅时，可能会看到此错误，但该订阅从未配合该资源类型使用。

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

   * 源资源组上的 Microsoft.Resources/subscriptions/resourceGroups/moveResources/action 权限。
   * 目标资源组上的 Microsoft.Resources/subscriptions/resourceGroups/write 权限。

1. 在移动资源之前，请检查要将资源移动到的订阅的订阅配额。 如果移动资源意味着订阅将超出其限制，则需要检查是否可以请求增加配额。 有关限制的列表及如何请求增加配额的信息，请参阅 [Azure 订阅和服务限制、配额与约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

1. **对于跨订阅移动，资源及其从属资源必须位于同一资源组中，并且必须一起移动。** 例如，具有托管磁盘的 VM 需要将 VM 和托管磁盘与其他依赖资源一起移动。

   如果要将资源移到新订阅，请检查该资源是否有任何依赖资源，以及这些资源是否位于同一资源组中。 如果资源不在同一资源组中，请检查资源是否可以合并到同一个资源组中。 如果是这样，请使用跨资源组的移动操作将所有这些资源置于同一资源组中。

   有关详细信息，请参阅[跨订阅移动方案](#scenario-for-move-across-subscriptions)。

## <a name="scenario-for-move-across-subscriptions"></a>跨订阅移动方案

将资源从一个订阅移到另一个订阅的过程分为三个步骤：

![跨订阅移动方案](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

出于说明目的，我们只有一个从属资源。

* 步骤1：如果从属资源分布在不同的资源组中，请先将它们移到一个资源组中。
* 步骤2：将资源和相关资源与源订阅一起移动到目标订阅。
* 步骤3：（可选）将从属资源重新分发给目标订阅中的不同资源组。 

## <a name="validate-move"></a>验证移动

[验证移动操作](/rest/api/resources/resources/validatemoveresources)可以测试你的移动方案而无需实际移动资源。 使用此操作检查移动是否成功。 发送移动请求时，会自动调用验证。 仅当需要预先确定结果时，才使用此操作。 若要运行此操作，需要：

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

若要移动资源，请选择包含这些资源的资源组，然后选择“移动”按钮。

![移动资源](./media/move-resource-group-and-subscription/select-move.png)

选择是要将资源移到新资源组还是新订阅。

选择要移动的资源和目标资源组。 确认需要更新这些资源的脚本，选择“确定”。 如果在上一步中已选择“编辑订阅”图标，则还必须选择目标订阅。

![选择目标](./media/move-resource-group-and-subscription/select-destination.png)

在“通知”中，可以看到移动操作正在运行。

![显示移动状态](./media/move-resource-group-and-subscription/show-status.png)

操作完成后，你会获得结果通知。

![显示移动结果](./media/move-resource-group-and-subscription/show-result.png)

如果遇到错误，请参阅[故障转移 Azure 资源到新的资源组或订阅](troubleshoot-move.md)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

要将现有资源移到另一个资源组或订阅，请使用 [Move-AzResource](/powershell/module/az.resources/move-azresource) 命令。 下面的示例演示了如何将多个资源移动到新的资源组。

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

若要移到新订阅，请包含 `DestinationSubscriptionId` 参数的值。

如果遇到错误，请参阅[故障转移 Azure 资源到新的资源组或订阅](troubleshoot-move.md)。

## <a name="use-azure-cli"></a>使用 Azure CLI

若要将现有资源移动到另一个资源组或订阅，请使用 [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) 命令。 提供要移动的资源的资源 ID。 下面的示例演示了如何将多个资源移动到新的资源组。 在 `--ids` 参数中，提供要移动的资源 ID 的空格分隔列表。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

若要移到新订阅，请提供 `--destination-subscription-id` 参数。

如果遇到错误，请参阅[故障转移 Azure 资源到新的资源组或订阅](troubleshoot-move.md)。

## <a name="use-rest-api"></a>使用 REST API

若要将现有资源移到另一个资源组或订阅，请使用 "[移动资源](/rest/api/resources/Resources/MoveResources)" 操作。

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

如果遇到错误，请参阅[故障转移 Azure 资源到新的资源组或订阅](troubleshoot-move.md)。

## <a name="frequently-asked-questions"></a>常见问题

**问：我的资源移动操作（通常只需几分钟）已运行了约1小时。是否有问题？**

移动资源是一种包含不同阶段的复杂操作。 它不仅仅涉及您尝试移动的资源的资源提供程序。 由于资源提供程序之间的依赖关系，Azure 资源管理器允许在4小时内完成操作。 此时间段为资源提供程序提供了从暂时性问题中恢复的机会。 如果移动请求在4小时内，则操作将继续尝试完成，但仍可能会成功。 源和目标资源组在这段时间内被锁定，以避免出现一致性问题。

**问：为什么在资源移动期间，我的资源组锁定了4个小时？**

4小时的窗口是资源移动允许的最大时间。 若要防止对移动的资源进行修改，源和目标资源组在资源移动期间都处于锁定状态。

移动请求中分为两个阶段。 在第一阶段中，将移动资源。 在第二阶段中，通知将发送到依赖于所移动资源的其他资源提供程序。 资源提供程序在阶段失败时，可以为整个4小时时间窗口锁定资源组。 在允许的时间内资源管理器重试失败的步骤。

如果无法在4小时内移动资源，资源管理器会解除对这两个资源组的锁定。 已成功移动的资源位于目标资源组中。 无法移动的资源将保留源资源组。

**问：在资源移动过程中，源和目标资源组的锁定有哪些？**

锁定会阻止你删除任何资源组、在资源组中创建新资源或删除移动中涉及的任何资源。

下图显示了当用户尝试删除属于正在进行的移动的资源组时，Azure 门户的错误消息。

![移动错误消息 "正在尝试删除"](./media/move-resource-group-and-subscription/move-error-delete.png)

**问：错误代码 "MissingMoveDependentResources" 的含义是什么？**

移动资源时，其从属资源必须存在于目标资源组或订阅中，或者包含在移动请求中。 当从属资源不满足此要求时，会收到 MissingMoveDependentResources 错误代码。 错误消息包含有关需要包含在移动请求中的依赖资源的详细信息。

例如，移动虚拟机可能需要使用三个不同的资源提供程序迁移七个资源类型。 这些资源提供程序和类型为：

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

另一个常见示例涉及移动虚拟网络。 可能需要移动多个与该虚拟网络关联的资源。 移动请求可能需要移动公共 IP 地址、路由表、虚拟网络网关、网络安全组和其他。

**问：为什么我无法在 Azure 中移动某些资源？**

目前，Azure 中的所有资源不支持移动。 有关支持移动的资源的列表，请参阅[对资源的移动操作支持](move-support-resources.md)。

## <a name="next-steps"></a>后续步骤

有关支持移动的资源的列表，请参阅[移动操作对资源的支持](move-support-resources.md)。
