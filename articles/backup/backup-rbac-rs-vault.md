---
title: 使用 Azure 基于角色的访问控制管理备份
description: 使用基于角色的访问控制来管理对恢复服务保管库中的备份管理操作的访问。
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: trinadhk
ms.openlocfilehash: ed3797183e13a00d2c5381fa6449c111c3bc9ab9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60253724"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>使用基于角色的访问控制管理 Azure 备份恢复点
Azure 基于角色的访问控制 (RBAC) 可用于对 Azure 进行细致的访问管理。 使用 RBAC，可以在团队中实现职责分离，仅向用户授予执行作业所需的访问权限。

> [!IMPORTANT]
> Azure 备份提供的角色仅限于执行可在 Azure 门户中执行的操作，或者通过 REST API 或恢复服务保管库 PowerShell 或 CLI cmdlet 执行的操作。 这些角色对在 Azure 备份代理客户端 UI、System Center Data Protection Manager UI 或 Azure 备份服务器 UI 中执行的操作不具有控制。

Azure 备份提供了三个用于控制备份管理操作的内置角色。 详细了解 [Azure RBAC 内置角色](../role-based-access-control/built-in-roles.md)

* [备份参与者](../role-based-access-control/built-in-roles.md#backup-contributor) - 此角色具有创建和管理备份方面的所有权限，除了创建恢复服务保管库和授予他人访问权限。 可以把该角色想象成可执行每个备份管理操作的备份管理的管理员。
* [备份操作员](../role-based-access-control/built-in-roles.md#backup-operator) - 此角色具有除删除备份和管理备份策略之外的针对参与者操作的所有权限。 此角色等效于参与者，但它不能执行破坏性操作，例如通过删除数据或删除本地资源的注册来停止备份。
* [备份读取器](../role-based-access-control/built-in-roles.md#backup-reader) - 此角色具有查看所有备份管理操作的权限。 可以把该角色想象成一位监视者。

若要定义自己的角色以便进一步控制，请参阅如何在 Azure RBAC 中 [生成自定义角色](../role-based-access-control/custom-roles.md)。



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>将备份内置角色映射到备份管理操作
下表包含了备份管理操作和执行这些操作所需的最低 RBAC 角色。

| 管理操作 | 所需的最低 RBAC 角色 | 所需的范围 |
| --- | --- | --- |
| 创建恢复服务保管库 | 参与者 | 包含保管库的资源组 |
| 启用 Azure VM 备份 | 备份操作员 | 包含保管库的资源组 |
| | 虚拟机参与者 | VM 资源 |
| 按需 VM 备份 | 备份操作员 | 恢复保管库资源 |
| 还原 VM | 备份操作员 | 恢复服务保管库 |
| | 参与者 | 将在其中部署 VM 的资源组 |
| | 虚拟机参与者 | 得到备份的源 VM |
| 还原非托管磁盘 VM 备份 | 备份操作员 | 恢复保管库资源 |
| | 虚拟机参与者 | 得到备份的源 VM |
| | 存储帐户参与者 | 要还原磁盘的存储帐户资源 |
| 从 VM 备份还原托管磁盘 | 备份操作员 | 恢复保管库资源 |
| | 虚拟机参与者 | 得到备份的源 VM |
| | 存储帐户参与者 | 在还原过程中选择的临时存储帐户，用于在将保管库转换为托管磁盘之前保存保管库中的数据 |
| | 参与者 | 托管磁盘将还原到的资源组 |
| 从 VM 备份还原单个文件 | 备份操作员 | 恢复保管库资源 |
| | 虚拟机参与者 | 得到备份的源 VM |
| 创建 Azure VM 备份的备份策略 | 备份参与者 | 恢复保管库资源 |
| 修改 Azure VM 备份的备份策略 | 备份参与者 | 恢复保管库资源 |
| 删除 Azure VM 备份的备份策略 | 备份参与者 | 恢复保管库资源 |
| 停止在 VM 备份上备份（通过保留数据或删除数据） | 备份参与者 | 恢复保管库资源 |
| 注册本地 Windows Server/客户端/SCDPM 或 Azure 备份服务器 | 备份操作员 | 恢复保管库资源 |
| 删除已注册的本地 Windows Server/客户端/SCDPM 或 Azure 备份服务器 | 备份参与者 | 恢复保管库资源 |

> [!IMPORTANT]
> 如果在 VM 资源范围内指定 VM 参与者并单击“备份”作为 VM 设置的一部分，则即使已经备份 VM，它也将打开“启用备份”屏幕，因为验证备份状态的调用仅在订阅级别起作用。 若要避免此问题，请转到保管库并打开 VM 的备份项视图，或者在订阅级别指定“VM 参与者”角色。

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Azure 文件共享备份的最小角色要求
下表列出了备份管理操作和执行的 Azure 文件共享操作所需的相应角色。

| 管理操作 | 所需的角色 | 资源 |
| --- | --- | --- |
| 启用 Azure 文件共享的备份 | 备份参与者 | 恢复服务保管库 |
| | 存储帐户 | 参与者存储帐户资源 |
| 按需 VM 备份 | 备份操作员 | 恢复服务保管库 |
| 还原文件共享 | 备份操作员 | 恢复服务保管库 |
| | 存储帐户参与者 | 还原源和目标文件共享存在的存储帐户资源 |
| 还原单个文件 | 备份操作员 | 恢复服务保管库 |
| | 存储帐户参与者 |   还原源和目标文件共享存在的存储帐户资源 |
| 停止保护 | 备份参与者 | 恢复服务保管库 |      
| 取消注册保管库中的存储帐户 |   备份参与者 | 恢复服务保管库 |
| | 存储帐户参与者 | 存储帐户名称|


## <a name="next-steps"></a>后续步骤
* [基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)：在 Azure 门户中开始使用 RBAC。
* 了解如何通过以下方式管理访问权限：
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [基于角色的访问控制故障排除](../role-based-access-control/troubleshooting.md)：获取解决常见问题的建议。
