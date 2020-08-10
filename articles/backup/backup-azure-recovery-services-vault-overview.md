---
title: 恢复服务保管库概述
description: 恢复服务保管库和 Azure 备份保管库的概述和比较。
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 0e1d061c6baf31fad2e937a604098f0baff6086d
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88041895"
---
# <a name="recovery-services-vaults-overview"></a>恢复服务保管库概述

本文介绍恢复服务保管库的功能。 恢复服务保管库是 Azure 中用于存储数据的存储实体。 数据通常是虚拟机 (VM)、工作负荷、服务器或工作站的数据或配置信息的副本。 可以使用恢复服务保管库为各种 Azure 服务（例如 IaaS VM（Linux 或 Windows））和 Azure SQL 数据库存储备份数据。 恢复服务保管库支持 System Center DPM、Windows Server、Azure 备份服务器等。 使用恢复服务保管库可以方便地组织备份数据，并将管理开销降至最低。 恢复服务保管库基于 azure 的 Azure 资源管理器型号，提供如下功能：

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>比较恢复服务保管库和备份保管库

- **有助于确保备份数据安全的增强功能**：使用恢复服务保管库时，Azure 备份提供用于保护云备份的安全功能。 这些安全功能确保可以保护备份并安全地恢复数据，即使生产服务器和备份服务器受到危害。 [了解详细信息](backup-azure-security-feature.md)

- **针对混合 IT 环境进行集中监视**：使用恢复服务保管库时，可以通过中心门户监视 [Azure IaaS VM](backup-azure-manage-vms.md) 和[本地资产](backup-azure-manage-windows-server.md#manage-backup-items)。 [了解详细信息](backup-azure-monitoring-built-in-monitor.md)

- **基于角色的访问控制 (RBAC)** ：RBAC 在 Azure 中提供精细的访问管理控制。 [Azure 提供各种内置角色](../role-based-access-control/built-in-roles.md)，而 Azure 备份具有三个[用于管理恢复点的内置角色](backup-rbac-rs-vault.md)。 恢复服务保管库与 RBAC 兼容，后者会限制对已定义用户角色集的备份和还原访问权限。 [了解详细信息](backup-rbac-rs-vault.md)

- **软删除**：对于软删除，即使恶意执行组件删除了备份 (或意外删除了备份数据) ，备份数据仍将保留14天，允许恢复该备份项目且不会丢失数据。 "软删除" 状态中的备份数据保留期额外的14天不会对客户产生任何费用。 [了解详细信息](backup-azure-security-feature-cloud.md)。

- **跨区域还原**：跨区域还原 (CRR) 使你可以在辅助区域（即 Azure 配对区域）中还原 azure vm。 如果 Azure 在主要区域中声明灾难，则辅助区域中复制的数据可以在次要区域中进行还原，以减轻其环境的主要区域中的实时停机灾难。 [了解详细信息](backup-azure-arm-restore-vms.md#cross-region-restore)。

## <a name="storage-settings-in-the-recovery-services-vault"></a>恢复服务保管库中的存储设置

恢复服务保管库是用于存储在不同时间创建的备份和恢复点的实体。 恢复服务保管库还包含与受保护虚拟机关联的备份策略。

- Azure 备份会自动处理保管库的存储。 查看如何[更改存储设置](./backup-create-rs-vault.md#set-storage-redundancy)。

- 若要详细了解存储冗余，请参阅有关[异地](../storage/common/storage-redundancy.md)冗余和[本地](../storage/common/storage-redundancy.md)冗余的这些文章。

### <a name="additional-resources"></a>其他资源

- [保管库支持和不支持的方案](backup-support-matrix.md#vault-support)
- [保管库常见问题](backup-azure-backup-faq.md)

## <a name="azure-advisor"></a>Azure 顾问

[Azure 顾问](../advisor/index.yml)是个性化的云顾问，可帮助优化 Azure 的使用。 它会分析 Azure 的使用情况，并提供及时的建议来帮助优化和保护部署。 它提供四个类别的建议：高可用性、安全性、性能和成本。

Azure 顾问为未备份的 VM 提供每小时[建议](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion)，因此，你永远不会错过备份重要的 VM。 你还可以通过推迟建议来控制建议。  可以通过指定存储备份的保管库 (来选择建议并在 Vm 上启用备份，方法是：指定存储备份的保管库) 和备份策略 (备份的备份和保留) 计划。

![Azure 顾问](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="next-steps"></a>后续步骤

使用以下文章了解相关操作：</br>
[备份 IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[备份 Azure 备份服务器](backup-azure-microsoft-azure-backup.md)</br>
[备份 Windows Server](backup-windows-with-mars-agent.md)
