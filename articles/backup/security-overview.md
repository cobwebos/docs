---
title: 安全功能概述
description: 了解 Azure 备份中的安全功能，这些功能可帮助您保护备份数据并满足企业的安全需求。
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 2eec3ee50f1de695b5432ee50b0900e35b81a6eb
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585816"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Azure 备份中安全功能概述

保护数据可以采取的最重要步骤之一是拥有可靠的备份基础架构。 但是，确保以安全的方式备份数据，并确保备份始终受到保护，这一点同样重要。 Azure 备份为备份环境提供了安全性 - 数据在传输过程中和静止时。 本文列出了 Azure 备份中的安全功能，这些功能可帮助您保护备份数据并满足企业的安全需求。

## <a name="management-and-control-of-identity-and-user-access"></a>身份和用户访问的管理和控制

Azure 备份使您能够使用[Azure 基于角色的访问控制 （RBAC）](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)管理细粒度访问。 RBAC 允许您在团队中隔离职责，并仅授予完成工作所需的用户访问权限。

Azure 备份提供了三个内置角色来控制备份管理操作：

* 备份参与者 - 创建和管理备份，但删除恢复服务保管库和授予其他人访问权限
* 备份操作员 - 参与者除删除备份和管理备份策略外所做的一切
* 备份读取器 - 查看所有备份管理操作的权限

详细了解[基于角色的访问控件以管理 Azure 备份](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)。

Azure 备份在服务中内置了多个安全控件，以防止、检测和响应安全漏洞。 详细了解[Azure 备份 的安全控件](https://docs.microsoft.com/azure/backup/backup-security-controls)。

## <a name="separation-between-guest-and-azure-storage"></a>来宾存储和 Azure 存储之间的分离

使用 Azure 备份（包括在 VM 备份中包括虚拟机备份和 SQL 和 SAP HANA），备份数据存储在 Azure 存储中，并且来宾无法直接访问备份存储或其内容。  使用虚拟机备份时，备份快照创建和存储由 Azure 结构完成，其中来宾除了静默应用程序一致的备份工作负载外，没有其他参与。  使用 SQL 和 SAP HANA，备份扩展可以临时访问以写入特定 Blob。  这样，即使在受危害的环境中，来宾也不能篡改或删除现有备份。

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Azure VM 备份不需要互联网连接

Azure VM 的备份需要将数据从虚拟机的磁盘转移到恢复服务保管库。 但是，所有必需的通信和数据传输仅在 Azure 骨干网络上进行，而无需访问虚拟网络。 因此，备份放置在安全网络中的 Azure VM 不需要您允许访问任何 IP 或 FQDN。

## <a name="private-endpoints-for-azure-backup"></a>用于 Azure 备份的专用终结点

现在，您可以使用[专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)安全地将数据从虚拟网络中的服务器备份到恢复服务保管库。 专用终结点使用来自 VNET 地址空间的 IP 来访问保管库，因此您无需将虚拟网络公开给任何公共 IP。 专用终结点可用于备份和还原在 Azure VM 中运行的 SQL 和 SAP HANA 数据库。 它还可用于使用 MARS 代理的本地服务器。

>[!NOTE]
> 此功能当前可用性有限。 如果您有兴趣使用专用终结点进行 Azure 备份，请填写[此调查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)并[发送电子邮件给我们](mailto:azbackupnetsec@microsoft.com)。 使用此功能的功能须经 Azure 备份服务批准。

## <a name="encryption-of-data-in-transit-and-at-rest"></a>传输和静态数据的加密

加密可保护您的数据，并帮助您履行组织安全和合规性承诺。 在 Azure 中，Azure 存储与保管库之间传输的数据受 HTTPS 保护。 此数据保留在 Azure 主干网络上。

* 备份数据使用 Microsoft 管理的密钥自动加密。 您还可以使用存储在 Azure 密钥保管库中[的客户托管密钥](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys)在恢复服务保管库中加密备份的托管磁盘 VM。 无需执行任何显式操作来启用此加密。 它适用于备份到恢复服务保管库的所有工作负载。

* Azure 备份支持使用 Azure 磁盘加密 （ADE） 加密其操作系统/数据磁盘的 Azure VM 的备份和恢复。 [了解有关加密 Azure VM 和 Azure 备份 的更多](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)详细信息。

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>保护备份数据免受意外删除

Azure 备份提供安全功能，即使在删除后也能帮助保护备份数据。 使用软删除，如果用户删除 VM 的备份，备份数据将再保留 14 天，从而可以恢复该备份项目，不会丢失任何数据。 在"软删除"状态下额外保留备份数据 14 天不会给客户带来任何成本。 [了解有关软删除 的更多](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete)。

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>监测和警报可疑活动

Azure 备份提供[内置监视和警报功能](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)，用于查看和配置与 Azure 备份相关的事件的操作。 [备份报告](https://docs.microsoft.com/azure/backup/configure-reports)是跟踪使用情况、审核备份和还原以及识别不同粒度级别的关键趋势的一站式目标。 使用 Azure 备份的监视和报告工具可以在任何未经授权的、可疑或恶意活动发生后立即提醒您。

## <a name="security-features-to-help-protect-hybrid-backups"></a>安全功能，帮助保护混合备份

Azure 备份服务使用 Microsoft Azure 恢复服务 （MARS） 代理从本地计算机备份和还原文件、文件夹以及卷或系统状态到 Azure。 MARS 现在提供安全功能来帮助保护混合备份。 这些功能包括：

* 执行关键操作（例如更改密码）时，会添加额外的身份验证层。 使用此验证，确保只有具有有效 Azure 凭据的用户才可执行此类操作。 [详细了解防止攻击的功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks)。

* 已删除的备份数据将从删除之日起再保留 14 天。 这可确保数据在给定时间段内的可恢复性，因此即使发生攻击，也会丢失数据。 此外，还保留了更多的最小恢复点，以防止数据损坏。 [了解有关恢复已删除的备份数据的更多。](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data)

* 对于使用 Microsoft Azure 恢复服务 （MARS） 代理备份的数据，密码用于确保数据在上载到 Azure 备份之前进行加密，并且仅在从 Azure 备份下载后解密。 密码详细信息仅对创建密码短语的用户和配置密码短语的代理可用。 不会与服务传输或共享任何内容。 这可确保数据的完整安全性，因为任何无意中公开的数据（如网络上的中间人攻击）在没有密码的情况下无法使用，并且密码不会在网络上发送。

## <a name="compliance-with-standardized-security-requirements"></a>符合标准化的安全要求

为了帮助组织遵守有关收集和使用个人数据的国家、区域和行业特定要求，Microsoft Azure & Azure 备份提供了一套全面的认证和证明。 [查看合规性认证列表](compliance-offerings.md)

## <a name="next-steps"></a>后续步骤

* [有助于保护使用 Azure 备份的云工作负荷的安全功能](backup-azure-security-feature-cloud.md)
* [有助于保护使用 Azure 备份的混合备份的安全功能](backup-azure-security-feature.md)
