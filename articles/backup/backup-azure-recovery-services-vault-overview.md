---
title: 恢复服务保管库概述
description: 恢复服务保管库概述。
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: cc09c1ffa84bc66e8fe2c48c80b8aea0977762ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90975143"
---
# <a name="recovery-services-vaults-overview"></a>恢复服务保管库概述

本文介绍恢复服务保管库的功能。 恢复服务保管库是 Azure 中用于存储数据的存储实体。 数据通常是虚拟机 (VM)、工作负荷、服务器或工作站的数据或配置信息的副本。 可以使用恢复服务保管库为各种 Azure 服务（例如 IaaS VM（Linux 或 Windows））和 Azure SQL 数据库存储备份数据。 恢复服务保管库支持 System Center DPM、Windows Server、Azure 备份服务器等。 使用恢复服务保管库可以方便地组织备份数据，并将管理开销降至最低。 恢复服务保管库基于 Azure 的 Azure 资源管理器模型，该模型提供如下功能：

- **有助于确保备份数据安全的增强功能**：使用恢复服务保管库时，Azure 备份提供用于保护云备份的安全功能。 这些安全功能确保可以保护备份并安全地恢复数据，即使生产服务器和备份服务器受到危害。 [了解详细信息](backup-azure-security-feature.md)

- **针对混合 IT 环境进行集中监视**：使用恢复服务保管库时，可以通过中心门户监视 [Azure IaaS VM](backup-azure-manage-vms.md) 和[本地资产](backup-azure-manage-windows-server.md#manage-backup-items)。 [了解详细信息](backup-azure-monitoring-built-in-monitor.md)

- **基于角色的访问控制 (RBAC)** ：RBAC 在 Azure 中提供精细的访问管理控制。 [Azure 提供各种内置角色](../role-based-access-control/built-in-roles.md)，而 Azure 备份具有三个[用于管理恢复点的内置角色](backup-rbac-rs-vault.md)。 恢复服务保管库与 RBAC 兼容，后者会限制对已定义用户角色集的备份和还原访问权限。 [了解详细信息](backup-rbac-rs-vault.md)

- 软删除：在使用软删除的情况下，即使恶意行动者删除了备份（或用户意外删除了备份数据），备份数据也仍会保留 14 天，因此可以恢复该备份项，而不会丢失数据。 以“软删除”状态将备份数据额外保留 14 天不会向你收取任何费用。 [了解详细信息](backup-azure-security-feature-cloud.md)。

- **跨区域还原**：跨区域还原 (CRR) 使你可以在辅助区域（即 Azure 配对区域）中还原 azure vm。 如果 Azure 在主要区域中声明灾难，则辅助区域中复制的数据可以在次要区域中进行还原，以减轻其环境的主要区域中的实时停机灾难。 [了解详细信息](backup-azure-arm-restore-vms.md#cross-region-restore)。

## <a name="storage-settings-in-the-recovery-services-vault"></a>恢复服务保管库中的存储设置

恢复服务保管库是用于存储在不同时间创建的备份和恢复点的实体。 恢复服务保管库还包含与受保护虚拟机关联的备份策略。

- Azure 备份会自动处理保管库的存储。 查看如何[更改存储设置](./backup-create-rs-vault.md#set-storage-redundancy)。

- 若要了解有关[存储冗余的](../storage/common/storage-redundancy.md#zone-redundant-storage)详细信息，请参阅以下文章：[地域](../storage/common/storage-redundancy.md#geo-zone-redundant-storage)、[本地和区域](../storage/common/storage-redundancy.md#locally-redundant-storage)冗余。

## <a name="encryption-settings-in-the-recovery-services-vault"></a>恢复服务保管库中的加密设置

本部分介绍可用于加密恢复服务保管库中存储的备份数据的选项。

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>使用平台托管的密钥加密备份数据

默认情况下，所有数据将使用平台托管的密钥进行加密。 无需从你的终端执行任何明确操作即可实现此加密。 这种加密适用于要备份到恢复服务保管库的所有工作负荷。

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客户托管密钥加密备份数据

可以选择使用所拥有和管理的加密密钥来加密数据。 Azure 备份允许你使用存储在 Azure 密钥保管库中的 RSA 密钥对备份进行加密。 用于加密备份的加密密钥可能与用于源的加密密钥不同。 数据受到基于 AES 256 的数据加密密钥 (DEK) 的保护，而 DEK 又受到你的密钥的保护。 这使你可以完全控制数据和密钥。 若要允许加密，必须向恢复服务保管库授予对 Azure Key Vault 中的加密密钥的访问权限。 可以根据需要禁用密钥或撤销访问权限。 但是，在你尝试保护保管库中的任何项目之前，必须先使用你的密钥启用加密。

阅读有关如何 [使用客户管理的密钥](encryption-at-rest-with-cmk.md)加密备份数据的详细信息。

## <a name="azure-advisor"></a>Azure 顾问

[Azure 顾问](../advisor/index.yml)是个性化的云顾问，可帮助优化 Azure 的使用。 它会分析 Azure 的使用情况，并提供及时的建议来帮助优化和保护部署。 它提供四个类别的建议：高可用性、安全性、性能和成本。

Azure 顾问为未备份的 VM 提供每小时[建议](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion)，因此，你永远不会错过备份重要的 VM。 你还可以通过推迟建议来控制建议。  可选择建议，然后通过指定保管库（将在其中存储备份）和备份策略（备份计划和备份副本保留期）来在 VM 上启用内联备份。

![Azure 顾问](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>其他资源

- [支持和不支持保管库的方案](backup-support-matrix.md#vault-support)
- [保管库常见问题解答](backup-azure-backup-faq.md)

## <a name="next-steps"></a>后续步骤

使用以下文章了解相关操作：

- [备份 IaaS VM](backup-azure-arm-vms-prepare.md)
- [备份 Azure 备份服务器](backup-azure-microsoft-azure-backup.md)
- [备份 Windows Server](backup-windows-with-mars-agent.md)
