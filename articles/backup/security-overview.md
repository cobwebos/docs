---
title: 安全功能概述
description: 了解 Azure 备份中的安全功能，这些功能可帮助你保护你的备份数据并满足你的业务的安全需求。
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 688573b571c6ce4473f06d4c194795a38a33244b
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82743656"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Azure 备份中的安全功能概述

若要保护数据，最重要的一步是使用可靠的备份基础结构。 不过，确保以安全的方式备份你的数据，并且始终保护你的备份，这一点也同样重要。 Azure 备份为你的备份环境提供安全性-无论你的数据在传输中还是静态。 本文列出了 Azure 备份中的安全功能，这些功能可帮助你保护你的备份数据并满足业务的安全需求。

## <a name="management-and-control-of-identity-and-user-access"></a>管理和控制标识和用户访问

恢复服务保管库使用的存储帐户是隔离的，恶意用户无法对其进行访问。 仅允许通过 Azure 备份管理操作（例如还原）进行访问。 使用 azure 备份可以通过使用[Azure 基于角色的访问控制（RBAC）](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)进行细粒度访问来控制托管的操作。 RBAC 允许您在团队内分隔职责，只向用户授予执行作业所需的访问权限量。

Azure 备份提供了三种[内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)来控制备份管理操作：

* 备份参与者-创建和管理备份，但删除恢复服务保管库，并向其他人授予访问权限
* Backup Operator-除删除备份和管理备份策略之外的所有内容
* 备份读取器-查看所有备份管理操作的权限

详细了解[用于管理 Azure 备份的基于角色的访问控制](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)。

Azure 备份有多个内置于服务中的安全控件，可以防止、检测和响应安全漏洞。 详细了解[Azure 备份的安全控制](https://docs.microsoft.com/azure/backup/backup-security-controls)。

## <a name="separation-between-guest-and-azure-storage"></a>来宾与 Azure 存储之间的分离

使用 Azure 备份（包括虚拟机备份和 SQL 以及 VM 备份中的 SAP HANA），备份数据将存储在 Azure 存储中，并且来宾不能直接访问备份存储或其内容。  对于虚拟机备份，备份快照的创建和存储是通过 Azure fabric 完成的，在此过程中，来宾除了暂停工作负荷以进行应用程序一致的备份以外，没有其他参与。  使用 SQL 和 SAP HANA，备份扩展会获得对特定 blob 的写入访问权限。  这样，即使在遭到攻击的环境中，也不能被来宾篡改或删除现有备份。

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Azure VM 备份不需要 Internet 连接

备份 Azure Vm 需要将数据从虚拟机的磁盘移动到恢复服务保管库。 但是，所有必需的通信和数据传输只发生在 Azure 主干网络上，而无需访问虚拟网络。 因此，将 Azure Vm 备份到受保护的网络中不需要你允许访问任何 Ip 或 Fqdn。

## <a name="private-endpoints-for-azure-backup"></a>Azure 备份的专用终结点

你现在可以使用[专用终结点](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)将数据从虚拟网络内的服务器安全地备份到恢复服务保管库中。 专用终结点使用保管库的 VNET 地址空间中的 IP，因此你无需将虚拟网络公开给任何公共 Ip。 专用终结点可用于备份和还原在 Azure Vm 中运行的 SQL 和 SAP HANA 数据库。 它还可用于使用 MARS 代理的本地服务器。

>[!NOTE]
> 此功能目前的可用性有限。 如果你有兴趣使用 Azure 备份的专用终结点，请填写[此调查](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)并向[我们发送电子邮件](mailto:azbackupnetsec@microsoft.com)。 使用此功能的功能可从 Azure 备份服务进行审批。

## <a name="encryption-of-data-in-transit-and-at-rest"></a>传输和静态数据的加密

加密可保护你的数据，并可帮助你满足组织的安全性和符合性承诺。 在 Azure 中，Azure 存储与保管库之间传输的数据受 HTTPS 保护。 此数据保留在 Azure 主干网络上。

* 使用 Microsoft 托管密钥自动对备份数据进行加密。 你还可以使用存储在 Azure Key Vault 中的[客户托管密钥](backup-encryption.md#encryption-of-backup-data-using-customer-managed-keys)来加密恢复服务保管库中备份的托管磁盘 vm。 您无需执行任何显式操作即可启用这种加密。 它适用于所有备份到恢复服务保管库的工作负荷。

* Azure 备份支持备份和还原使用 Azure 磁盘加密（ADE）加密的 OS/data 磁盘的 Azure Vm。 [了解有关加密的 Azure vm 和 Azure 备份的详细信息](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>防止无意中删除备份数据

Azure 备份提供安全功能来帮助保护备份数据，即使在删除后也是如此。 使用软删除时，如果用户删除 VM 的备份，备份数据将保留14天，允许恢复该备份项目且不会丢失数据。 "软删除" 状态中的备份数据保留时间额外的14天不会对客户产生任何费用。 [详细了解软删除](backup-azure-security-feature-cloud.md)。

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>可疑活动的监视和警报

Azure 备份提供[内置的监视和警报功能](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)，可用于查看和配置与 Azure 备份相关的事件的操作。 [备份报表](https://docs.microsoft.com/azure/backup/configure-reports)作为一站式目标，用于跟踪使用情况、审核备份和还原，以及确定不同粒度级别的关键趋势。 使用 Azure 备份的监视和报告工具，可以在任何未经授权、可疑或恶意活动发生时向你发出警报。

## <a name="security-features-to-help-protect-hybrid-backups"></a>有助于保护混合备份的安全功能

Azure 备份服务使用 Microsoft Azure 恢复服务（MARS）代理将本地计算机上的文件、文件夹和卷或系统状态备份和还原到 Azure。 MARS 现在提供了保护混合备份的安全功能。 这些功能包括：

* 执行关键操作（例如更改密码）时，会添加额外的身份验证层。 使用此验证，确保只有具有有效 Azure 凭据的用户才可执行此类操作。 [了解有关阻止攻击的功能的详细信息](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks)。

* 删除的备份数据将保留14天的删除日期。 这可确保在给定时间段内数据的可恢复性，因此即使发生攻击，也不会丢失数据。 此外，还保留了更多的最小恢复点，以防止数据损坏。 [详细了解如何恢复已删除的备份数据](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data)。

* 对于使用 Microsoft Azure 恢复服务（MARS）代理备份的数据，将使用一个密码来确保数据在上载到 Azure 备份之前进行加密，并在从 Azure 备份下载后进行解密。 通行短语详细信息仅适用于创建通行短语的用户，以及使用它配置的代理。 不传输任何内容或与服务共享任何内容。 这可确保数据的完整安全性（例如，在网络上进行中间人攻击）在没有密码的情况下无法使用，并且不会在网络上发送密码。

## <a name="compliance-with-standardized-security-requirements"></a>符合标准化安全要求

为了帮助组织遵守管理个人数据收集和使用的国家、地区和行业特定要求，Microsoft Azure & Azure 备份提供了一套全面的认证和证明。 [请参阅符合性认证列表](compliance-offerings.md)

## <a name="next-steps"></a>后续步骤

* [有助于保护使用 Azure 备份的云工作负荷的安全功能](backup-azure-security-feature-cloud.md)
* [有助于保护使用 Azure 备份的混合备份的安全功能](backup-azure-security-feature.md)
