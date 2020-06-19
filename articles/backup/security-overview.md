---
title: 安全功能概述
description: 了解 Azure 备份中可帮助你保护备份数据并满足你企业的安全需求的安全功能。
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 178518f9f04a789f3cb634797cab650e24864337
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653797"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Azure 备份中安全功能的概述

要保护数据，可采取的最重要的步骤之一是使用可靠的备份基础结构。 但同样重要的是确保数据以安全的方式进行备份，且备份始终受到保护。 无论是传输中的数据还是静态数据，Azure 备份都确保备份环境的安全。 本文列出了 Azure 备份中可帮助你保护备份数据并满足你企业的安全需求的安全功能。

## <a name="management-and-control-of-identity-and-user-access"></a>管理和控制标识及用户访问

恢复服务保管库使用的存储帐户是独立的，用户无法出于任何恶意目的访问它们。 仅允许通过 Azure 备份管理操作（例如还原）来进行此访问。 借助 Azure 备份，可使用[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault) 通过精细访问权限来控制托管的操作。 通过 RBAC，可在团队中将各职责分开，仅向用户授予执行作业所需的访问权限。

Azure 备份提供了 3 个[内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)用于控制备份管理操作：

* 备份参与者 - 可创建和管理备份，但无法创建恢复服务保管库和向他人授予访问权限
* 备份操作员 - 可执行参与者能做的所有操作，只是无法删除备份和管理备份策略
* 备份读取者 - 有权查看所有备份管理操作

详细了解[使用基于角色的访问控制来管理 Azure 备份](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)。

Azure 备份在服务中内置了多个安全控件，用于防止、检测和响应安全漏洞。 详细了解 [Azure 备份的安全控件](https://docs.microsoft.com/azure/backup/backup-security-controls)。

## <a name="separation-between-guest-and-azure-storage"></a>将来宾和 Azure 存储隔开

借助 Azure 备份（包括虚拟机备份以及 VM 备份中的 SQL 和 SAP HANA），备份数据存储在 Azure 存储中，来宾无法直接访问备份存储或其内容。  通过虚拟机备份，备份快照创建和存储由 Azure 结构来完成，来宾只暂停工作负载以进行应用程序一致备份，不参与其他操作。  通过 SQL 和 SAP HANA，备份扩展可获取临时访问权限以写入特定 Blob。  这样，即使环境遭到攻击，来宾也无法篡改或删除现有备份。

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>无需 Internet 连接即可进行 Azure VM 备份

Azure VM 的备份需要将数据从虚拟机的磁盘移动到恢复服务保管库。 但是，所有必需通信和数据传输都仅在 Azure 主干网络上发生，无需访问虚拟网络。 因此，你无需允许访问任何 IP 或 FQDN 即可备份受保护的网络中的 Azure VM。

## <a name="private-endpoints-for-azure-backup"></a>Azure 备份的专用终结点

现在可以使用[专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)将数据从虚拟网络内的服务器安全地备份到恢复服务保管库。 专用终结点对保管库使用来自 VNET 地址空间的 IP，因此你无需向任何公共 IP 公开你的虚拟网络。 专用终结点可用于备份和还原在 Azure VM 中运行的 SQL 数据库和 SAP HANA 数据库。 它还可以通过 MARS 代理用于本地服务器。

在[此处](https://docs.microsoft.com/azure/backup/private-endpoints)详细了解 Azure 备份的专用终结点。

## <a name="encryption-of-data-in-transit-and-at-rest"></a>加密传输中的数据和静态数据

加密可保护数据，并帮助你履行组织的安全性和符合性承诺。 在 Azure 中，Azure 存储与保管库之间传输的数据受 HTTPS 保护。 此数据保留在 Azure 主干网络上。

* 备份数据使用 Microsoft 托管密钥自动加密。 你还可使用 Azure Key Vault 中存储的[客户管理的密钥](backup-encryption.md#encryption-of-backup-data-using-customer-managed-keys)在恢复服务保管库中加密已备份的托管磁盘 VM。 无需执行任何明确操作即可实现此加密。 它适用于正在备份到恢复服务保管库的所有工作负载。

* Azure 备份支持备份和还原已使用 Azure 磁盘加密 (ADE) 加密操作系统/数据磁盘的 Azure VM。 [详细了解已加密的 Azure VM 和 Azure 备份](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>防止意外删除备份数据

Azure 备份提供了安全功能来帮助保护备份数据，即使删除后也能提供保护。 通过软删除，如果用户删除了某个 VM 的备份，备份数据将额外保留 14 天，让用户能在不丢失数据的情况下恢复该备份项。 备份数据在“软删除”状态中额外保留 14 天不会对客户产生任何费用。 [详细了解软删除](backup-azure-security-feature-cloud.md)。

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>监视和提醒可疑活动

Azure 备份提供了[内置的监视和警报功能](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)来查看和配置与 Azure 备份相关的事件的操作。 可通过[备份报表](https://docs.microsoft.com/azure/backup/configure-reports)一步到位地跟踪使用情况、审核备份和还原，以及确定不同粒度级别的关键趋势。 可使用 Azure 备份的监视和报告工具在任何未经授权、可疑或恶意活动发生时立即向你发送警报。

## <a name="security-features-to-help-protect-hybrid-backups"></a>可帮助保护混合备份的安全功能

Azure 备份服务使用 Microsoft Azure 恢复服务 (MARS) 代理将本地计算机中的文件、文件夹和卷/系统状态备份和还原到 Azure。 MARS 现提供了安全功能来帮助保护混合备份。 这些功能包括：

* 执行关键操作（例如更改密码）时，会添加额外的身份验证层。 使用此验证，确保只有具有有效 Azure 凭据的用户才可执行此类操作。 [详细了解可阻止攻击的功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks)。

* 删除的备份数据将自删除之日起额外保留 14 天。 这可确保能够在给定的时间段内恢复数据，因此即使受到攻击，也不会丢失数据。 此外，还保留了更多的最小恢复点，以防止数据损坏。 [详细了解如何恢复已删除的备份数据](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data)。

* 对于使用 Microsoft Azure 恢复服务 (MARS) 代理备份的数据，使用密码来确保数据在上传到 Azure 备份之前被加密，且仅在从 Azure 备份下载后才被解密。 密码详细信息仅提供给创建密码的用户以及使用该密码配置的代理。 不传输也不与服务共享任何内容。 这可确保数据的完整安全性，因为即使任何数据被意外公开（例如受到网络上的中间人攻击），没有密码的话也无法使用，而密码不会在网络上发送。

## <a name="compliance-with-standardized-security-requirements"></a>符合标准化安全要求

为了帮助组织符合监管个人数据收集和使用的国家、地区和行业特定要求，Microsoft Azure 和 Azure 备份提供一套全面的认证和证明。 [查看符合性认证列表](compliance-offerings.md)

## <a name="next-steps"></a>后续步骤

* [有助于保护使用 Azure 备份的混云工作负载的安全功能](backup-azure-security-feature-cloud.md)
* [有助于保护使用 Azure 备份的混合备份的安全功能](backup-azure-security-feature.md)
