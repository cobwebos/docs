---
title: 备份保管库概述
description: 备份保管库概述。
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: c189997ecc4814917182246b35003649d317ac77
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92091281"
---
# <a name="backup-vaults-overview"></a>备份保管库概述

本文介绍备份保管库的功能。 备份保管库是 Azure 中的一个存储实体，用于承载 Azure 备份支持的某些较新工作负荷的备份数据。 你可以使用备份保管库来保存各种 Azure 服务（例如 Azure Database for PostgreSQL 服务器和 Azure 备份将支持的较新工作负荷）的备份数据。 备份保管库可以方便地组织备份数据，同时将管理开销降至最低。 备份保管库基于 azure 的 Azure 资源管理器型号，提供如下功能：

- **用于帮助保护备份数据的增强功能**：通过备份保管库，Azure 备份提供安全功能来保护云备份。 这些安全功能确保可以保护备份并安全地恢复数据，即使生产服务器和备份服务器受到危害。 [了解详细信息](backup-azure-security-feature.md)

- Azure **rbac) 的 azure 基于角色的访问控制 (**： azure Rbac 在 azure 中提供精细的访问管理控制。 [Azure 提供各种内置角色](../role-based-access-control/built-in-roles.md)，而 Azure 备份具有三个[用于管理恢复点的内置角色](backup-rbac-rs-vault.md)。 备份保管库与 Azure RBAC 兼容，后者会限制对已定义用户角色集的备份和还原访问权限。 [了解详细信息](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>备份保管库中的存储设置

备份保管库是存储一段时间内创建的备份和恢复点的实体。 备份保管库还包含与受保护虚拟机关联的备份策略。

- Azure 备份会自动处理保管库的存储。 创建备份保管库时，请选择符合你的业务需求的存储冗余。

- 若要详细了解存储冗余，请参阅有关[异地](../storage/common/storage-redundancy.md#geo-redundant-storage)冗余和[本地](../storage/common/storage-redundancy.md#locally-redundant-storage)冗余的这些文章。

## <a name="encryption-settings-in-the-backup-vault"></a>备份保管库中的加密设置

本部分介绍可用于对备份保管库中存储的备份数据进行加密的选项。

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>使用平台托管的密钥加密备份数据

默认情况下，所有数据将使用平台托管的密钥进行加密。 无需从你的终端执行任何明确操作即可实现此加密。 它适用于备份到备份保管库的所有工作负荷。

## <a name="create-a-backup-vault"></a>创建备份保管库

备份保管库是存储一段时间内创建的恢复点的管理实体，并提供用于执行与备份相关的操作的接口。 其中包括按需备份、执行还原和创建备份策略。

若要创建备份保管库，请执行以下步骤。

### <a name="sign-in-to-azure"></a>登录 Azure

通过 <https://portal.azure.com> 登录到 Azure 门户。

### <a name="create-backup-vault"></a>创建备份保管库

1. 在搜索框中键入 " **备份保管库** "。
1. 在 " **服务**" 下，选择 " **备份保管库**"。
1. 在 " **备份保管库** " 页中，选择 " **添加**"。
1. 在 " **基本信息" 选项卡**中的 " **项目详细信息**" 下，确保选择了正确的订阅，然后选择 " **创建新** 资源组"。 对于名称，请键入 *myResourceGroup*。

  ![新建资源组](./media/backup-vault-overview/new-resource-group.png)

1. 在 "**实例详细信息**" 下，键入*myVault*作为**备份保管库名称**，然后选择你所选的区域，在此示例中，你的**区域**为*美国东部*。
1. 现在，选择你的 **存储冗余**。 在保护保管库的项目后，无法更改存储冗余。
1. 如果使用 Azure 作为主要备份存储终结点，则我们建议继续使用默认的“异地冗余”设置。
1. 如果不使用 Azure 作为主要的备份存储终结点，则请选择“本地冗余”，减少 Azure 存储费用。
1. 详细了解[异地冗余](../storage/common/storage-redundancy.md#geo-redundant-storage)和[本地冗余](../storage/common/storage-redundancy.md#locally-redundant-storage)。

  ![选择存储冗余](./media/backup-vault-overview/storage-redundancy.png)

1. 选择页面底部的 "查看" 和 "创建" 按钮。

    ![选择 "查看 + 创建"](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>删除备份保管库

本部分介绍如何删除备份保管库。 其中分别说明了如何删除依赖项，以及如何删除保管库。

### <a name="before-you-start"></a>开始之前

不能删除具有以下任何依赖项的备份保管库：

- 不能删除包含受保护数据源的保管库 (例如，Azure database for PostgreSQL 服务器) 。
- 不能删除包含备份数据的保管库。

如果在不删除依赖项的情况下尝试删除保管库，将遇到以下错误消息：

>无法删除备份保管库，因为保管库中存在现有的备份实例或备份策略。 删除保管库中存在的所有备份实例和备份策略，然后尝试删除保管库。

### <a name="proper-way-to-delete-a-vault"></a>删除保管库的正确方式

>[!WARNING]
以下操作是破坏性的，无法撤消。 与受保护服务器关联的所有备份数据和备份项将被永久删除。 请谨慎操作。

若要正确删除保管库，必须按以下顺序执行步骤：

- 必须检查以验证是否有任何受保护的项：
  - 在左侧导航栏中，参阅 " **备份实例** "。 此处列出的所有项目都必须先删除。

完成这些步骤后，你可以继续删除保管库。

### <a name="delete-the-backup-vault"></a>删除备份保管库

当保管库中没有其他任何项时，在保管库仪表板上选择“删除”。 你将看到一条确认文本，询问你是否要删除该保管库。

![删除保管库](./media/backup-vault-overview/delete-vault.png)

1. 选择“是”确认要删除该保管库。 随即会删除该保管库。 门户返回到“新建”服务菜单。

## <a name="monitor-and-manage-the-backup-vault"></a>监视和管理备份保管库

本部分介绍如何使用备份保管库 **概述** 面板来监视和管理备份保管库。 概述窗格包含两个磁贴： **作业** 和 **实例**。

![“概述”仪表板](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>管理备份实例

在 " **作业** " 磁贴中，可以获取备份保管库中的所有备份和还原相关作业的汇总视图。 选择此磁贴中的任何数字可查看有关特定数据源类型、操作类型和状态的详细信息。

![备份实例](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>管理备份作业

在 " **备份实例** " 磁贴中，可以获取备份保管库中所有备份实例的汇总视图。 选择此磁贴中的任何数字可查看有关特定数据源类型和保护状态的备份实例的详细信息。

![备份作业](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>后续步骤

- [在 Azure PostgreSQL 数据库上配置备份](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
