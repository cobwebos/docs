---
title: 在 Azure 资源移动器中删除移动集合中的资源
description: 了解如何在 Azure 资源移动器中删除移动集合中的资源。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 38a633a7a11ac29271231679e7075920e1f33a70
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945937"
---
# <a name="manage-move-collections-and-resource-groups"></a>管理移动集合和资源组

本文介绍如何从移动集合中删除资源，或在 [Azure 资源移动器](overview.md)中删除移动集合/资源组。 移动集合在 Azure 区域之间移动 Azure 资源时使用。

## <a name="remove-a-resource-portal"></a> (门户中删除资源) 

可以删除移动集合中的资源，如下所示：

1. 在 " **跨区域**" 中，选择想要从集合中删除的所有资源，然后选择 " **删除**"。 

    ![要选择删除的按钮](./media/remove-move-resources/portal-select-resources.png)

2. 在 " **删除资源**" 中，单击 " **删除**"。

    ![用于从移动集合中删除资源的按钮](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a> (门户中删除移动集合/资源组) 

可以在门户中删除移动集合/资源组。

1. 按照上述过程中的说明从集合中删除资源。 如果要删除资源组，请确保它不包含任何资源。
2. 删除移动集合或资源组。  

## <a name="remove-a-resource-powershell"></a> (PowerShell 中删除资源) 

删除资源 (在我们的示例中，PSDemoVM 机使用 PowerShell 从集合) ，如下所示：

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus - Name PSDemoVM
```
**预期输出** 
 ![从移动集合中删除资源后的输出文本](./media/remove-move-resources/remove-resource.png)



## <a name="remove-a-collection-powershell"></a> (PowerShell 中删除集合) 

使用 PowerShell 删除整个移动集合，如下所示：

1. 请按照上面的说明使用 PowerShell 删除集合中的资源。
2. 运行：

    ```azurepowershell-interactive
    # Remove a resource using the resource ID
    Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus 
    ```
    **预期输出** ![删除移动集合后的输出文本](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>删除后的 VM 资源状态

从移动集合中删除 VM 资源时，会发生什么情况取决于资源状态，如表中所述。

###  <a name="remove-vm-state"></a>删除 VM 状态
**资源状态** | **VM** | **联网**
--- | --- | --- 
**已添加到移动集合** | 从移动集合中删除。 | 从移动集合中删除。 
**依赖关系已解决/准备挂起** | 从移动集合中删除  | 从移动集合中删除。 
**准备正在进行**<br/> 正在进行 (或任何其他状态)  | 删除操作失败，出现错误。  | 删除操作失败，出现错误。
**准备失败** | 从移动集合中删除。<br/>删除在目标区域中创建的任何内容，包括副本磁盘。 <br/><br/> 需要手动删除在移动过程中创建的基础结构资源。 | 从移动集合中删除。  
**启动移动挂起** | 从移动集合中删除。<br/><br/> 删除在目标区域中创建的任何内容，包括 VM、副本磁盘等。  <br/><br/> 需要手动删除在移动过程中创建的基础结构资源。 | 从移动集合中删除。
**启动移动失败** | 从移动集合中删除。<br/><br/> 删除在目标区域中创建的任何内容，包括 VM、副本磁盘等。  <br/><br/> 需要手动删除在移动过程中创建的基础结构资源。 | 从移动集合中删除。
**提交挂起** | 建议你放弃移动以便先删除目标资源。<br/><br/> 资源将返回到 " **启动移动暂停** " 状态，你可以从此处继续。 | 建议你放弃移动以便先删除目标资源。<br/><br/> 资源将返回到 " **启动移动暂停** " 状态，你可以从此处继续。 
**提交失败** | 建议你放弃，以便先删除目标资源。<br/><br/> 资源将返回到 " **启动移动暂停** " 状态，你可以从此处继续。 | 建议你放弃移动以便先删除目标资源。<br/><br/> 资源将返回到 " **启动移动暂停** " 状态，你可以从此处继续。
**放弃完成** | 资源将返回到 " **启动移动挂起** " 状态。<br/><br/> 它从移动集合中删除，以及在目标 VM、副本磁盘、保管库等中创建的任何内容。  <br/><br/> 需要手动删除在移动过程中创建的基础结构资源。 <br/><br/> 需要手动删除在移动过程中创建的基础结构资源。 |  资源将返回到 " **启动移动挂起** " 状态。<br/><br/> 它从移动集合中删除。
**放弃失败** | 建议你放弃移动以便先删除目标资源。<br/><br/> 之后，该资源会恢复为 " **启动移动" 挂起** 状态，你可以从此处继续。 | 建议你放弃移动以便先删除目标资源。<br/><br/> 之后，该资源会恢复为 " **启动移动" 挂起** 状态，你可以从此处继续。
**删除挂起的源** | 从移动集合中删除。<br/><br/> 它不会删除在目标区域中创建的任何内容。  | 从移动集合中删除。<br/><br/> 它不会删除在目标区域中创建的任何内容。
**删除源失败** | 从移动集合中删除。<br/><br/> 它不会删除在目标区域中创建的任何内容。 | 从移动集合中删除。<br/><br/> 它不会删除在目标区域中创建的任何内容。

## <a name="sql-resource-state-after-removing"></a>删除后的 SQL 资源状态

从移动集合中删除 Azure SQL 资源时所发生的情况取决于资源状态，如表中所汇总。

**资源状态** | **SQL** 
--- | --- 
**已添加到移动集合** | 从移动集合中删除。 
**依赖关系已解决/准备挂起** | 从移动集合中删除 
**准备正在进行**<br/> 正在进行 (或任何其他状态)   | 删除操作失败，出现错误。 
**准备失败** | 从移动集合中删除<br/><br/>删除在目标区域中创建的任何内容。 
**启动移动挂起** |  从移动集合中删除<br/><br/>删除在目标区域中创建的任何内容。 此时，SQL 数据库已存在，将被删除。 
**启动移动失败** | 从移动集合中删除<br/><br/>删除在目标区域中创建的任何内容。 此点上存在 SQL 数据库，必须将其删除。 
**提交挂起** | 建议你放弃移动以便先删除目标资源。<br/><br/> 资源将返回到 " **启动移动暂停** " 状态，你可以从此处继续。
**提交失败** | 建议你放弃移动以便先删除目标资源。<br/><br/> 资源将返回到 " **启动移动暂停** " 状态，你可以从此处继续。 
**放弃完成** |  资源将返回到 " **启动移动挂起** " 状态。<br/><br/> 它从移动集合中删除，以及在目标上创建的任何内容，包括 SQL 数据库。 
**放弃失败** | 建议你放弃移动以便先删除目标资源。<br/><br/> 之后，该资源会恢复为 " **启动移动" 挂起** 状态，你可以从此处继续。 
**删除挂起的源** | 从移动集合中删除。<br/><br/> 它不会删除在目标区域中创建的任何内容。 
**删除源失败** | 从移动集合中删除。<br/><br/> 它不会删除在目标区域中创建的任何内容。 

## <a name="next-steps"></a>后续步骤

尝试使用资源移动器将 [VM 移](tutorial-move-region-virtual-machines.md) 到另一个区域。