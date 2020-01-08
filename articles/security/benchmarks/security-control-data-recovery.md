---
title: Azure 安全控制-数据恢复
description: 安全控制数据恢复
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 1cd3e39fd504b5095a83192a4a6314c71d3ca980
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564291"
---
# <a name="security-control-data-recovery"></a>安全控制：数据恢复

确保所有系统数据、配置和机密均定期自动备份。

## <a name="91-ensure-regular-automated-back-ups"></a>9.1：确保定期自动备份

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 9.1 | 10.1 | 客户 |

启用 Azure 备份并配置备份源（Azure Vm、SQL Server 或文件共享）以及所需的频率和保持期。

如何启用 Azure 备份： https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：执行完整的系统备份并备份任何客户托管的密钥

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 9.2 | 10.2 | 客户 |

启用 Azure 备份和目标 VM，以及所需的频率和保留期。 Azure Key Vault 中的备份客户托管密钥。

如何启用 Azure 备份： https://docs.microsoft.com/azure/backup/

如何在 Azure 中备份 key vault 密钥： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：验证包括客户托管密钥在内的所有备份

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 9.3 | 10.3 | 客户 |

确保能够定期在 Azure 备份中执行内容数据还原。 如有必要，请将测试还原到隔离的 VLAN 中。 测试已备份客户托管密钥的还原。

如何从 Azure 虚拟机备份恢复文件：

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

如何在 Azure 中还原 key vault 密钥：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：确保保护备份和客户管理的密钥

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 9.4 | 10.4 | 客户 |

对于本地备份，使用在备份到 Azure 时提供的密码提供静态加密。 对于 Azure VM，使用存储服务加密 (SSE) 对数据进行静态加密。 可以在 Key Vault 中启用软删除，以防止意外或恶意删除密钥。

如何在 Key Vault 中启用软删除：

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>后续步骤

请参阅下一个安全控制：[事件响应](security-control-incident-response.md)
