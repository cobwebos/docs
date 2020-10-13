---
title: 迁移到 Azure 基于角色的访问控制 |Microsoft Docs
description: 了解如何从保管库访问策略迁移到 Azure 角色。
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: e06a7a759c712b47f3a725a3c49a660226da6a09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90064118"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-preview-permission-model"></a>从保管库访问策略迁移到 Azure 基于角色的访问控制 (预览) 权限模型

保管库访问策略模型是 Key Vault 中内置的现有授权系统，用于提供对密钥、机密和证书的访问权限。 可以通过在 Key Vault 范围内将单独的权限分配给安全主体 (用户、组、服务主体、托管标识) 来控制访问权限。 

Azure 基于角色的访问控制 (Azure RBAC) 是在 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)基础上构建的授权系统，针对 Azure 资源提供精细的访问权限管理。 用于 Key Vault 密钥、机密和证书访问管理的 Azure RBAC 当前提供公共预览版。 使用 Azure RBAC，你可以通过创建角色分配来控制对资源的访问权限，这些分配由三个元素组成：安全主体、角色定义 (预定义的权限集) 和作用域 (资源组或单个资源) 。 有关详细信息，请参阅 [Azure 基于角色的访问控制 (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)。

在迁移到 Azure RBAC 之前，请务必了解其优点和限制。

与保管库访问策略相比，Azure RBAC 密钥的优点是：
- 为 Azure 资源提供统一的访问控制模型-跨 Azure 服务的相同 API
- 集中式访问管理-管理员-在一个视图中管理所有 Azure 资源
- 与基于时间的访问控制的 [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) 集成
- 拒绝分配-排除特定范围内的安全主体的能力。 有关信息，请参阅 [了解 Azure 拒绝分配](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)

Azure RBAC 缺点：
- 角色分配延迟-应用角色分配可能需要几分钟时间。 保管库访问策略会立即分配。
- 有限数量的角色分配-每个订阅2000个角色分配，每个 Key Vault 有1024个访问策略

## <a name="access-policies-to-azure-roles-mapping"></a>访问 Azure 角色映射的策略

Azure RBAC 具有多个 Azure 内置角色，你可以将这些角色分配给用户、组、服务主体和托管标识。 如果内置角色不能满足组织的特定需求，你可以创建自己的 [Azure 自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。

Key Vault 密钥、证书和密钥访问管理的内置角色：
- Key Vault 管理员(预览版)
- Key Vault 读取者(预览版)
-  (预览版 Key Vault 证书安全官) 
- Key Vault 加密管理人员(预览版)
- Key Vault 加密用户(预览版)
- Key Vault 机密管理人员(预览版)
- Key Vault 机密用户(预览版)

有关现有内置角色的详细信息，请参阅 [Azure 内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)

可以通过单独选择的权限或预定义的权限模板来分配保管库访问策略。

访问策略预定义的权限模板：
- 密钥、机密和证书管理
- 密钥 & 密钥管理
- 机密 & 证书管理
- 密钥管理
- 机密管理
- 证书管理
- SQL Server 连接器
- Azure Data Lake Storage 或 Azure 存储
- Azure 备份
- Exchange Online 客户密钥
- SharePoint Online 客户密钥
- Azure 信息 BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>访问策略模板到 Azure 角色的映射
| 访问策略模板 | 操作 | Azure 角色 |
| --- | --- | --- |
| 密钥、机密和证书管理 | 密钥：所有操作 <br>证书：所有操作<br>机密：所有操作 | 密钥保管库管理员（预览版） |
| 密钥 & 密钥管理 | 密钥：所有操作 <br>机密：所有操作| Key Vault 加密管理人员(预览版)<br> Key Vault 机密管理人员(预览版)|
| 机密 & 证书管理 | 证书：所有操作 <br>机密：所有操作| Key Vault 证书管理人员(预览版)<br> Key Vault 机密管理人员(预览版)|
| 密钥管理 | 密钥：所有操作| Key Vault 加密管理人员(预览版)|
| 机密管理 | 机密：所有操作| Key Vault 机密管理人员(预览版)|
| 证书管理 | 证书：所有操作 | Key Vault 证书管理人员(预览版)|
| SQL Server 连接器 | 密钥： get、list、wrap key、解包密钥 | Key Vault 加密服务加密(预览版)|
| Azure Data Lake Storage 或 Azure 存储 | 密钥：获取、列出、解包密钥 | 不适用<br> 需要自定义角色|
| Azure 备份 | 密钥：获取、列出、备份<br> 证书：获取、列出、备份 | 不适用<br> 需要自定义角色|
| Exchange Online 客户密钥 | 密钥： get、list、wrap key、解包密钥 | Key Vault 加密服务加密(预览版)|
| Exchange Online 客户密钥 | 密钥： get、list、wrap key、解包密钥 | Key Vault 加密服务加密(预览版)|
| Azure 信息 BYOK | 密钥：获取、解密、签名 | 不适用<br>需要自定义角色|


## <a name="assignment-scopes-mapping"></a>分配范围映射  

适用于 Key Vault 的 Azure RBAC 允许在以下范围内分配角色：
- 管理组
- 订阅
- 资源组
- Key Vault 资源
- 单个密钥、机密和证书

保管库访问策略权限模型限制为仅分配策略 Key Vault 资源级别，这 

通常，每个应用程序都有一个密钥保管库，并在 key vault 级别管理访问权限。 在某些情况下，管理其他作用域的访问权限可以简化访问管理。

- * * 基础结构、安全管理员和操作员：使用保管库访问策略管理管理组、订阅或资源组级别的密钥保管库组需要维护每个密钥保管库的策略。 Azure RBAC 允许在管理组、订阅或资源组中创建一个角色分配。 该分配将应用于在同一作用域中创建的任何新的密钥保管库。 在这种情况下，建议使用 Privileged Identity Management 与提供永久访问权限的实时访问。
 
- * * 应用程序：在某些情况下，应用程序需要与其他应用程序共享机密。 如果使用保管库访问策略，则必须创建单独的密钥保管库，以避免向所有机密提供访问权限。 Azure RBAC 允许使用单个密钥保管库为单独机密的作用域分配角色。

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Azure RBAC 迁移步骤的保管库访问策略
Azure RBAC 和保管库访问策略权限模型之间存在很多差异。 为了避免迁移过程中出现中断，建议执行以下步骤。
 
1. **标识并分配角色**：根据以上映射表标识内置角色并在需要时创建自定义角色。 根据范围映射指南，在范围内分配角色。 有关如何将角色分配到密钥保管库的详细信息，请参阅 [使用 Azure 基于角色的访问控制提供对 Key Vault 的访问权限 (预览版) ](rbac-guide.md)
1. **验证角色分配**： Azure RBAC 中的角色分配可能需要几分钟才能传播。 有关如何检查角色分配的详细信息，请参阅 [在范围内列出角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope)
1. **在密钥保管库上配置监视和警报**：启用日志记录并针对拒绝访问异常启用日志记录很重要。 有关详细信息，请参阅 [Azure Key Vault 的监视和警报](https://docs.microsoft.com/azure/key-vault/general/alert)
1. **Key Vault 上设置 azure 基于角色的访问控制权限模型**：启用 azure RBAC 权限模型将使所有现有的访问策略失效。 如果出现错误，则可以使用保持不变的所有现有访问策略来切换回权限模型。

> [!NOTE]
> 启用 Azure RBAC 权限模型后，尝试更新访问策略的所有脚本都将失败。 更新这些脚本以使用 Azure RBAC 非常重要。

## <a name="troubleshooting"></a>疑难解答
-  几分钟后角色分配不工作-在某些情况下，角色分配可能需要更长时间。 在代码中编写重试逻辑以涵盖这些情况非常重要。
- 删除 Key Vault (软删除) 和已恢复时，角色分配将消失-当前所有 Azure 服务中的软删除功能的限制。 在恢复后，必须重新创建所有角色分配。    

## <a name="learn-more"></a>了解更多

- [Azure RBAC 概述](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [自定义角色教程](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-cli)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
