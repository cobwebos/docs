---
title: Azure 安全基准 V2-备份和恢复
description: Azure 安全基准 V2 备份和恢复
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b2e54545fb79120a3f9d66067da267df3b151b3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322115"
---
# <a name="security-control-v2-backup-and-recovery"></a>安全控制 V2：备份和恢复

备份和恢复涵盖了控制，以确保执行、验证和保护不同服务层上的数据和配置备份。

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1：确保定期自动备份

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| BR-1 | 10.1 | CP-2、CP4、CP 6、CP 9 |

确保在发生意外事件后，备份系统和数据以保持业务连续性。 这应由恢复点目标 (RPO) 和恢复时间目标 (RTO) 的任何目标定义。

启用 Azure 备份并配置备份源 (例如，Azure Vm、SQL Server、HANA 数据库或文件共享) ，以及所需的频率和保留期。  

为了获得更高的保护级别，可以启用异地冗余存储选项，将备份数据复制到次要区域，并使用跨区域还原进行恢复。

- [企业规模业务连续性和灾难恢复](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [如何启用 Azure 备份](/azure/backup/)

- [如何启用跨区域还原](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [策略和标准](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2：加密备份数据

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| BR-2 | 10.2 | CP-9 |

确保你的备份受到防范攻击的威胁。 这应该包括备份的加密，以防止机密性丢失。   

对于使用 Azure 备份的本地备份，将使用所提供的密码提供静态加密。 对于常规 Azure 服务备份，将使用 Azure 平台管理的密钥自动对备份数据进行加密。 你可以选择使用客户托管密钥对备份进行加密。 在这种情况下，请确保密钥保管库中此客户托管的密钥也在备份范围内。 

在 Azure 备份、Azure Key Vault 或其他资源中使用基于角色的访问控制，以保护备份和客户管理的密钥。 此外，可以在更改或删除备份之前，启用高级安全功能来要求进行 MFA。

- [Azure 备份中的安全功能概述](/azure/backup/security-overview)

- [使用客户托管密钥加密备份数据](/azure/backup/encryption-at-rest-with-cmk) 

- [如何在 Azure 中备份 Key Vault 密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [有助于保护混合备份免受攻击的安全功能](/azure/backup/backup-azure-security-feature#prevent-attacks)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR：验证包括客户托管密钥在内的所有备份

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| BR-3 | 10.3 | CP-4，CP-9 |

定期执行备份的数据还原。 请确保可以还原已备份的客户管理的密钥。

- [如何从 Azure 虚拟机备份恢复文件](/azure/backup/backup-azure-restore-files-from-vm)

- [如何在 Azure 中还原 Key Vault 密钥](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4：降低丢失密钥的风险

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| BR-4 | 10.4 | CP-9 |

确保已准备好措施来防止密钥丢失和从密钥丢失中恢复。 在 Azure Key Vault 中启用软删除和清除保护，以防止意外或恶意删除密钥。  

- [如何在 Key Vault 中启用软删除和清除保护](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**责任**：客户

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [数据安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

