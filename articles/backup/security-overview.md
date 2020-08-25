---
title: 安全功能概述
description: 了解 Azure 备份中的安全功能，这些功能可帮助你保护备份数据并满足企业的安全需求。
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0354e319c82e02072a8771f9aae383bf0b6240f6
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88824588"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Azure 备份中的安全功能概述

保护数据所要采取的最重要措施之一是使用可靠的备份基础结构。 但是，确保以安全方式备份数据并随时保护备份也同样重要。 Azure 备份为备份环境中的传输中数据和静态数据提供安全措施。 本文列出了 Azure 备份中的安全功能，这些功能可帮助你保护备份数据并满足企业的安全需求。

## <a name="management-and-control-of-identity-and-user-access"></a>管理和控制标识与用户访问

恢复服务保管库使用的存储帐户是独立的，用户不能出于任何恶意目的对其进行访问。 仅允许通过 Azure 备份管理操作（例如还原）进行访问。 使用 azure 备份，可以通过使用 [AZURE RBAC)  (基于角色的访问控制 ](./backup-rbac-rs-vault.md)精细访问控制托管操作。 RBAC 可以在团队内部实现职责分离，仅向用户授予其履行自己的职责所需的访问权限级别。

Azure 备份提供了三个[内置角色](../role-based-access-control/built-in-roles.md)来控制备份管理操作：

* 备份参与者 - 可以创建和管理备份，但无法删除恢复服务保管库，也不能授予他人访问权限
* 备份操作员 - 拥有参与者的所有权限，但无法删除备份，也不能管理备份策略
* 备份读取者 - 拥有查看所有备份管理操作的权限

详细了解[用于管理 Azure 备份的基于角色的访问控制](./backup-rbac-rs-vault.md)。

Azure 备份服务中内置了多个安全控制机制，用于防止、检测和应对安全漏洞。 详细了解 [Azure 备份的安全控制](./backup-security-controls.md)。

## <a name="separation-between-guest-and-azure-storage"></a>在来宾与 Azure 存储之间进行隔离

使用 Azure 备份（包括虚拟机备份，以及 VM 备份中的 SQL 和 SAP HANA）时，备份数据会存储在 Azure 存储中，来宾无法直接访问备份存储或其内容。  使用虚拟机备份时，备份快照创建和存储操作由 Azure 结构执行，来宾不会干预此过程，只是使工作负荷静止，以创建应用程序一致性备份。  使用 SQL 和 SAP HANA 时，备份扩展会暂时获得对特定 Blob 的写入访问权限。  这样，即使在遭到入侵的环境中，现有备份也不会遭到篡改或者被来宾删除。

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>无需建立 Internet 连接即可创建 Azure VM 备份

备份 Azure VM 需要将虚拟机磁盘中的数据移到恢复服务保管库。 但是，所需的通信和数据传输全部只在 Azure 主干网络中发生，无需访问虚拟网络。 因此，你无需允许访问任何 IP 或 FQDN 即可备份受保护的网络中的 Azure VM。

## <a name="private-endpoints-for-azure-backup"></a>Azure 备份的专用终结点

现在可以使用[专用终结点](../private-link/private-endpoint-overview.md)将数据从虚拟网络内的服务器安全地备份到恢复服务保管库。 专用终结点使用保管库的 VNET 地址空间中的 IP，因此，无需将虚拟网络公开给任何公共 Ip。 专用终结点可用于备份和还原在 Azure VM 中运行的 SQL 数据库和 SAP HANA 数据库。 它还可以通过 MARS 代理用于本地服务器。

在[此处](./private-endpoints.md)详细了解 Azure 备份的专用终结点。

## <a name="encryption-of-data"></a>数据加密

加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。 数据加密发生在 Azure 备份的多个阶段：

* 在 Azure 中，Azure 存储和保管库之间传输的数据 [受 HTTPS 的保护](backup-support-matrix.md#network-traffic-to-azure)。 此数据保留在 Azure 主干网络上。

* 使用 [平台托管密钥](backup-encryption.md)自动对备份数据进行加密，并且无需执行任何显式操作即可启用。 你还可以使用存储在 Azure Key Vault 中的 [客户托管密钥](encryption-at-rest-with-cmk.md) 来加密备份的数据。 这种加密适用于要备份到恢复服务保管库的所有工作负荷。

* Azure 备份支持备份和还原使用 Azure 磁盘加密进行加密的 Azure Vm 的备份和还原 [ (使用 Azure 磁盘加密 ](backup-azure-vms-encryption.md#encryption-support-using-ade) 进行加密的数据磁盘) 和 [具有 CMK 加密磁盘的 vm](backup-azure-vms-encryption.md#encryption-using-customer-managed-keys)。 有关详细信息，请 [参阅有关加密的 Azure vm 和 Azure 备份的详细](./backup-azure-vms-encryption.md)信息。

* 当使用 MARS 代理从本地服务器备份数据时，将使用密码对数据进行加密，然后再将其上载到 Azure 备份，并在从 Azure 备份下载后进行解密。 阅读有关 [安全功能的详细信息，帮助保护混合备份](#security-features-to-help-protect-hybrid-backups)。

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>防止意外删除备份数据

Azure 备份提供安全功能来帮助保护备份数据，即使是删除了备份数据，也能予以恢复。 启用软删除后，如果用户删除了 VM 的备份，备份数据将额外保留 14 天，因此可以恢复该备份项，而不会丢失数据。 以“软删除”状态将备份数据额外保留 14 天不会向客户收取任何费用。 [详细了解软删除](backup-azure-security-feature-cloud.md)。

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>可疑活动的监视和警报

Azure 备份提供[内置监视和警报功能](./backup-azure-monitoring-built-in-monitor.md)，用于查看和配置 Azure 备份相关事件的操作。 [备份报表](./configure-reports.md)充当一站式目标，在其中能够以不同的粒度级别跟踪使用情况、审核备份和还原，以及识别关键趋势。 使用 Azure 备份的监视和报告工具可以在发生任何未经授权的、可疑的或恶意的活动后立即获得警报。

## <a name="security-features-to-help-protect-hybrid-backups"></a>可帮助保护混合备份的安全功能

Azure 备份服务使用 Microsoft Azure 恢复服务 (MARS) 代理将本地计算机中的文件、文件夹和卷/系统状态备份和还原到 Azure。 MARS 现提供了安全功能来帮助保护混合备份。 这些功能包括：

* 执行关键操作（例如更改密码）时，会添加额外的身份验证层。 使用此验证，确保只有具有有效 Azure 凭据的用户才可执行此类操作。 [详细了解用于防止攻击的功能](./backup-azure-security-feature.md#prevent-attacks)。

* 删除的备份数据自删除之日起会额外保留 14 天。 这可以确保能够在给定的时间段内恢复数据，因此即使遭到攻击，也不会丢失数据。 此外，还保留了更多的最小恢复点，以防止数据损坏。 [详细了解如何恢复已删除的备份数据](./backup-azure-security-feature.md#recover-deleted-backup-data)。

* 对于使用 Microsoft Azure 恢复服务 (MARS) 代理备份的数据，使用密码来确保数据在上传到 Azure 备份之前被加密，且仅在从 Azure 备份下载后才被解密。 密码详细信息仅提供给创建密码的用户以及使用该密码配置的代理。 不会通过服务传输任何信息，也不会与服务共享任何信息。 这可以全面确保数据的安全性，因为在没有通行短语的情况下，无法使用无意中公开的任何数据（例如，在网络中出现中间人攻击时就是如此），而通行短语不会在网络中发送。

## <a name="compliance-with-standardized-security-requirements"></a>符合标准化安全要求

为了帮助组织符合监管个人数据收集和使用的国家、地区和行业特定要求，Microsoft Azure 和 Azure 备份提供一套全面的认证和证明。 [查看符合性认证列表](compliance-offerings.md)

## <a name="next-steps"></a>后续步骤

* [有助于保护使用 Azure 备份的云工作负荷的安全功能](backup-azure-security-feature-cloud.md)
* [有助于保护使用 Azure 备份的混合备份的安全功能](backup-azure-security-feature.md)
