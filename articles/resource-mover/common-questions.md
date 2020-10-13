---
title: 有关 Azure 资源移动器的常见问题
description: 获取有关 Azure 资源移动器的常见问题解答
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: raynew
ms.openlocfilehash: 68e5f937b8ad8367abf488598bda311a39d462c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600647"
---
# <a name="common-questions"></a>常见问题

本文解答了有关 [Azure 资源移动器](overview.md)的常见问题。

## <a name="general"></a>常规

### <a name="is-resource-mover-generally-available"></a>资源移动器是否已公开发布？

资源移动器当前为公共预览版。 支持生产工作负荷。



## <a name="moving-across-regions"></a>跨区域移动

### <a name="can-i-move-resources-across-any-regions"></a>是否可以跨任何区域移动资源？

目前，可以将资源从任何源公共区域移到任何目标公共区域，具体取决于 [该区域中可用的资源类型](https://azure.microsoft.com/global-infrastructure/services/)。 当前不支持在 Azure 政府区域中移动资源。

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>可以使用资源移动器跨区域移动哪些资源？

使用资源转移器，当前可以在区域之间移动以下资源：

- Azure VM 和关联的磁盘
- NIC
- 可用性集 
- Azure 虚拟网络 
- 公共 IP 地址
- 网络安全组 (NSG)：
- 内部和公共负载均衡器 
- Azure SQL 数据库和弹性池


### <a name="can-i-move-resources-across-subscriptions-when-i-move-them-across-regions"></a>在多个区域之间移动资源时，是否可以在多个订阅之间移动资源？

将资源移动到目标区域后，可以更改订阅。 [详细了解](../azure-resource-manager/management/move-resource-group-and-subscription.md) 如何将资源移到其他订阅。 

### <a name="where-is-the-metadata-for-moving-across-regions-stored"></a>用于在存储的区域之间移动的元数据位于何处？

它存储在 [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) 数据库中，并且存储在 Microsoft 订阅中的 [azure blob 存储](../storage/common/storage-service-encryption.md)中。 当前元数据存储在美国东部2和北欧中。 我们会将此覆盖范围扩展到其他区域。 这并不局限于跨任何公共区域移动资源。

### <a name="is-the-collected-metadata-encrypted"></a>收集的元数据是否已加密？

是的，两者都在传输和静止。
- 传输过程中，使用 HTTPS 通过 internet 安全地将元数据发送到资源移动器服务。
- 在存储中，元数据已加密。

### <a name="how-is-managed-identity-used-in-resource-mover"></a>资源移动器中如何使用托管标识？

[托管标识](../active-directory/managed-identities-azure-resources/overview.md) (以前称为托管服务标识 (MIS) # A3 为 Azure 服务提供 Azure AD 中的自动托管标识。
- 资源移动器使用托管标识，以便它可以访问 Azure 订阅以跨区域移动资源。
- 移动集合需要系统分配的标识，并可访问包含你要移动的资源的订阅。

- 如果在门户中跨区域移动资源，则会自动执行此过程。
- 如果使用 PowerShell 移动资源，请运行 cmdlet 将系统分配的标识分配给集合，然后将具有正确订阅权限的角色分配给标识主体。 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>资源移动器需要哪些托管标识权限？

Azure 资源转移器托管标识至少需要以下权限： 

- 在用户订阅中写入/创建资源的权限，可与 *参与者* 角色一起使用。 
- 创建角色分配的权限。 通常与 " *所有者* " 或 " *用户访问管理员* " 角色一起使用，或具有分配了 *Microsoft. Authorization/role 分配/写入权限* 的自定义角色。 如果数据共享资源的托管标识已被授予对 Azure 数据存储的访问权限，则不需要此权限。 
 
当你在门户中的资源移动器中心添加资源时，只要用户具有上述权限，权限就会自动处理。 如果通过 PowerShell 添加资源，请手动分配权限。

> [!IMPORTANT]
> 我们强烈建议你不要修改或删除标识角色分配。 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>如果我没有分配角色标识的权限，我该怎么办？

可能的原因  | 建议
--- | ---
首次添加资源时，不是 *参与者* 和 *用户访问管理员* (或 *所有者*) 。 | 将帐户与 *参与者* 和 *用户访问管理员* 一起使用 (或 *所有者*) 订阅的权限。
资源移动器托管标识不具有所需的角色。 | 添加 "参与者" 和 "用户访问管理员" 角色。
资源移动器托管标识已重置为 *无*。 | 在移动集合 > **标识**中重新启用系统分配的标识。 此外，也可以在 " **添加资源**" 中再次添加资源，这会执行相同的操作。  
已将订阅移到其他租户。 | 禁用并启用移动集合的托管标识。

### <a name="how-can-i-do-multiple-moves-together"></a>如何将多个移动操作组合在一起？

使用门户中的 "更改" 选项，根据需要更改源/目标组合。

## <a name="next-steps"></a>后续步骤

[详细了解](about-move-process.md)资源转移器组件和移动过程。
