---
title: 如何移动 Azure 备份恢复服务保管库
description: 有关如何跨 Azure 订阅和资源组移动恢复服务保管库的说明。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: fed42c578da2e4f27f42e11d5ac67d698bbcd939
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120725"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>跨 Azure 订阅和资源组移动恢复服务保管库

本文介绍如何跨 Azure 订阅移动针对 Azure 备份配置的恢复服务保管库，或者将其移到同一订阅中的另一个资源组。 可以使用 Azure 门户或 PowerShell 移动恢复服务保管库。

## <a name="supported-regions"></a>支持的区域

澳大利亚东部、澳大利亚东南部、加拿大中部、加拿大东部、南东亚、东亚、美国中部、美国中北部、美国东部、美国东部、美国中南部、美国西部、美国东部、美国西部、美国西部、美国西部、美国西部、美国西部、美国西部、美国西部、美国东部、美国西部、美国西部、印度中部、印度南部、日本东部、日本西部、韩国中部、韩国南部、北欧、西欧、南非北部、南非西部、英国南部和英国西部。

## <a name="unsupported-regions"></a>不受支持的区域

法国中部、法国南部、德国东北部、德国中部、US Gov 爱荷华州、中国北部、中国 North2、中国东部、中国东部2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>移动恢复服务保管库的先决条件

- 在保管库跨资源组移动时，源资源组和目标资源组将被锁定，阻止写入和删除操作。 有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。
- 只有管理员订阅有权移动保管库。
- 对于跨订阅移动保管库，目标订阅必须位于与源订阅相同的租户中，并且应启用其状态。
- 必须有权对目标资源组执行写入操作。
- 移动保管库只会更改资源组。 恢复服务保管库将位于同一位置，并且无法更改。
- 一次只能移动一个恢复服务保管库，每个区域。
- 如果 VM 不会跨订阅移动到恢复服务保管库或新资源组，则当前 VM 恢复点将在保管库中保持不变，直到它们过期。
- 不管 VM 是否连同保管库一起移动，都始终可以从保管库中保留的备份历史记录还原该 VM。
- Azure 磁盘加密要求 key vault 和 Vm 位于同一个 Azure 区域和订阅中。
- 若要移动包含托管磁盘的虚拟机，请参阅[此文](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/)。
- 移动通过经典模型部署的资源时，其选项各不相同，具体取决于是在订阅中移动资源，还是将资源移到新订阅。 有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。
- 跨订阅移动保管库或将其移到新资源组后，为保管库定义的备份策略将会保留。
- 不支持跨订阅和资源组将保管库与 Azure 文件、Azure 文件同步或 SQL 一起移动到 IaaS Vm。
- 如果在多个订阅之间移动包含 VM 备份数据的保管库，则必须将 Vm 移到相同的订阅，并使用同一目标 VM 资源组名称（与旧订阅相同）来继续备份。

> [!NOTE]
>
> 目前无法移动配置为与 **Azure Site Recovery** 配合使用的恢复服务保管库。 如果使用 **Azure Site Recovery** 为灾难恢复配置了任何 VM（Azure IaaS、Hyper-V、VMware）或物理机，移动操作将受阻止。 针对 Site Recovery 服务的资源移动功能尚未推出。

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>使用 Azure 门户将恢复服务保管库移动到不同的资源组

将恢复服务保管库及其关联的资源移到不同的资源组

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 打开“恢复服务保管库”列表，并选择要移动的保管库。 当保管库仪表板打开时，其外观如下图所示。

   ![打开恢复服务保管库](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   如果未看到保管库的“概要”信息，请单击下拉图标。 现在，应会看到保管库的“概要”信息。

   ![“概要”信息选项卡](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 在保管库概述菜单中，单击“资源组”旁边的“更改”打开“移动资源”边栏选项卡。

   ![更改资源组](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. 在“移动资源”边栏选项卡中，对于选定的保管库，建议通过选中下图所示的复选框，移动可选的相关资源。

   ![移动订阅](./media/backup-azure-move-recovery-services/move-resource.png)

5. 若要添加目标资源组，请在“资源组”下拉列表中选择现有的资源组，或单击“创建新组”选项。

   ![创建资源](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. 添加资源组后，确认“我了解与已移动资源关联的工具和脚本在更新为使用新资源 ID 之前将不可用”选项，然后单击“确定”完成保管库移动操作。

   ![确认消息](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>使用 Azure 门户将恢复服务保管库移动到不同的订阅

可将恢复服务保管库及其关联的资源移到不同的订阅

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 打开“恢复服务保管库”列表，并选择要移动的保管库。 当保管库仪表板打开时，其外观如下图所示。

    ![打开恢复服务保管库](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    如果未看到保管库的“概要”信息，请单击下拉图标。 现在，应会看到保管库的“概要”信息。

    ![“概要”信息选项卡](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 在保管库概述菜单中，单击“订阅”旁边的“更改”打开“移动资源”边栏选项卡。

   ![更改订阅](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. 选择要移动的资源。此处我们建议使用“全选”选项来选择列出的所有可选资源。

   ![移动资源](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. 在“订阅”下拉列表中选择目标订阅，保管库将移到该订阅。
6. 若要添加目标资源组，请在“资源组”下拉列表中选择现有的资源组，或单击“创建新组”选项。

   ![添加订阅](./media/backup-azure-move-recovery-services/add-subscription.png)

7. 单击“我了解与已移动资源关联的工具和脚本在更新为使用新资源 ID 之前将不可用”选项以确认，然后单击“确定”。

> [!NOTE]
> 不支持跨订阅的备份（RS 保管库和受保护 VM 位于不同的订阅中）。 此外，在执行保管库移动操作期间，无法将存储冗余选项从本地冗余存储 (LRS) 修改为全局冗余存储 (GRS)，反之亦然。
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>使用 PowerShell 移动恢复服务保管库

若要将恢复服务保管库移到另一个资源组，请使用 `Move-AzureRMResource` cmdlet。 `Move-AzureRMResource` 需要资源名称和资源类型。 可以通过 `Get-AzureRmRecoveryServicesVault` cmdlet 获取这两项信息。

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

若要将资源移到不同的订阅，请包含 `-DestinationSubscriptionId` 参数。

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

执行上述 cmdlet 后，系统会要求你确认是否要移动指定的资源。 键入 **Y** 确认。 验证成功后，资源将会移动。

## <a name="use-cli-to-move-recovery-services-vault"></a>使用 CLI 移动恢复服务保管库

若要将恢复服务保管库移到另一个资源组，请使用以下 cmdlet：

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

若要移到新订阅，请提供 `--destination-subscription-id` 参数。

## <a name="post-migration"></a>迁移之后

1. 设置/验证资源组的访问控制。  
2. 完成移动操作后，需要为保管库重新配置备份报告和监视功能。 在移动操作期间，以前的配置将会丢失。

## <a name="next-steps"></a>后续步骤

可以在资源组和订阅之间移动许多不同类型的资源。

有关详细信息，请参阅[将资源移到新资源组或订阅](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。
