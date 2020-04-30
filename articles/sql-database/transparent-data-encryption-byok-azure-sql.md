---
title: 客户管理的透明数据加密 (TDE)
description: 使用 SQL 数据库和 Azure Synapse 的 Azure Key Vault 为透明数据加密 (TDE) 提供创建自己的密钥 (BYOK) 支持。 支持 BYOK 的 TDE 概述、优势、工作原理、注意事项和建议。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: d72a1057c359127eb70f0a82fbf2637409535dce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131166"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>使用客户管理的密钥进行 Azure SQL 透明数据加密

Azure SQL [透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) 与客户管理的密钥共同实现了“创建自己的密钥”(BYOK) 方案，凭此可以实现静态数据保护，并使组织能够在密钥和数据管理方面实现职责分离。 使用客户管理的透明数据加密时，客户需要负责并可全面控制密钥生命周期管理（密钥创建、上传、轮换、删除）、密钥使用权限，以及密钥操作的审核。

在此方案中，用于加密数据库加密密钥 (DEK) 的密钥（称作 TDE 保护器）是客户管理的非对称密钥，该密钥存储在客户自有的且由其管理的 [Azure Key Vault (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)（一个基于云的外部密钥管理系统）中。 Key Vault 是适用于 RSA 加密密钥的高度可用且可缩放的安全存储，可选择通过 FIPS 140-2 第2级验证的硬件安全模块（Hsm）进行支持。 它不允许直接访问存储的密钥，但会向已获授权的实体提供使用该密钥进行加密/解密的服务。 密钥保管库可以从本地 HSM 设备生成密钥保管库，将其导入或[传输到密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)。

对于 Azure SQL 数据库和 Azure Synapse，TDE 保护器在逻辑服务器级别设置，并由该服务器关联的所有已加密数据库继承。 对于 Azure SQL 托管实例，TDE 保护器是在实例级别设置的，并由该实例上所有加密的数据库继承。 除非另有说明，否则术语“服务器”在整个文档中指的是 SQL 数据库逻辑服务器和托管实例。 

> [!IMPORTANT]
> 对于当前正在使用服务托管的 TDE 并想要开始使用客户管理的 TDE 的用户，在切换过程中数据将保持加密状态，且不会造成停机，也不需要重新加密数据库文件。 从服务托管的密钥切换到客户管理的密钥只需重新加密 DEK，此操作非常快捷且可在线完成。

## <a name="benefits-of-the-customer-managed-tde"></a>客户管理的 TDE 的优势

客户管理的 TDE 为客户提供以下优势：

- 全面精细控制 TDE 保护器的使用和管理；

- TDE 保护器的使用透明性；

- 可以在组织中密钥和数据的管理方面实现职责分离；

- Key Vault 管理员可以撤消密钥访问权限，使加密的数据库不可访问；

- 集中管理 AKV 中的密钥；

- 比最终客户更好地信任，因为 AKV 的设计使 Microsoft 无法看到或提取加密密钥;

## <a name="how-customer-managed-tde-works"></a>客户管理的 TDE 的工作原理

![客户管理的 TDE 的设置和运行](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

要使服务器能够使用 AKV 中存储的 TDE 保护器来加密 DEK，Key Vault 管理员需要使用该服务器的唯一 AAD 标识向其授予以下访问权限：

- **get** - 用于检索 Key Vault 中密钥的公共部分和属性

- **wrapKey** - 用于保护（加密）DEK

- **unwrapKey** - 用于取消保护（解密）DEK

Key Vault 管理员还可以[启用 Key Vault 审核事件的日志记录](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)，以便以后可以审核这些事件。

将服务器配置为使用 AKV 中的 TDE 保护器后，该服务器会将每个启用了 TDE 的数据库的 DEK 发送到 Key Vault 用于加密。 Key Vault 返回已加密的 DEK，该 DEK 随后将存储在用户数据库中。

如果需要，服务器会将受保护的 DEK 发送到 Key Vault 用于解密。

如果已启用日志记录，审核员可以使用 Azure Monitor 查看 Key Vault 审核事件日志。

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>配置客户管理的 TDE 的要求

### <a name="requirements-for-configuring-akv"></a>配置 AKV 的要求

- Key Vault 和 SQL 数据库/托管实例必须属于同一个 Azure Active Directory 租户。 不支持 Key Vault 与服务器进行跨租户的交互。 以后若要移动资源，必须重新配置 TDE 和 AKV。 详细了解如何[移动资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。

- 必须对 Key Vault 启用[软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)功能，以防止意外删除密钥（或 Key Vault）时发生数据丢失。 软删除的资源将保留 90 天，除非客户在此期间恢复或清除这些资源。 “恢复”和“清除”操作在 Key Vault 访问策略中各自具有相关联的权限   。 软删除功能在默认情况下处于关闭状态，可通过[PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete)或[CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete)启用。 无法通过 Azure 门户启用此功能。  

- 使用 SQL 数据库服务器或托管实例的 Azure Active Directory 标识向其授予对 Key Vault 的访问权限（get、wrapKey、unwrapKey）。 使用 Azure 门户时，会自动创建 Azure AD 标识。 使用 PowerShell 或 CLI 时，必须显式创建 Azure AD 标识，并且应验证创建是否完成。 有关使用 PowerShell 进行配置的详细分步说明，请参阅[配置支持 BYOK 的 TDE](transparent-data-encryption-byok-azure-sql-configure.md) 和[配置支持托管实例 BYOK 的 TDE](https://aka.ms/sqlmibyoktdepowershell)。

- 将防火墙与 AKV 配合使用时，必须启用“允许信任的 Microsoft 服务绕过防火墙”选项。 

### <a name="requirements-for-configuring-tde-protector"></a>配置 TDE 保护器的要求

- TDE 保护程序只能是非对称、RSA 或 RSA HSM 密钥。 支持的密钥长度为2048到3072个字节。

- 密钥激活日期（如果已设置）必须是过去的日期和时间。 过期日期（如果已设置）必须是将来的日期和时间。

- 密钥必须处于“已启用”状态。**

- 如果将现有的密钥导入 Key Vault，请确保以支持的文件格式（.pfx、.byok 或 .backup）提供该密钥。

## <a name="recommendations-when-configuring-customer-managed-tde"></a>有关配置客户管理的 TDE 的建议

### <a name="recommendations-when-configuring-akv"></a>配置 AKV 时的建议

- 将最多 500 个“常规用途”或 200 个“业务关键”数据库关联到单个订阅中的一个 Key Vault，以确保在服务器访问 Key Vault 中的 TDE 保护器时实现高可用性。 这些数字是根据经验以及 [Key Vault 服务限制](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)中的描述得出的。 此项建议旨在防止服务器故障转移后出现问题，因为故障转移过程对保管库触发的密钥操作数目与该服务器中的数据库数目相同。

- 在 Key Vault 中设置资源锁可以控制谁能删除此关键资源，并防止意外或未经授权的删除。 详细了解[资源锁](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)。

- 对所有加密密钥启用审核和报告： Key vault 提供了可轻松注入到其他安全信息和事件管理工具的日志。 Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) 是已集成的服务的一个示例。

- 将每个服务器链接到位于不同区域中包含相同密钥材料的两个 Key Vault，以确保已加密数据库的高可用性。 仅将同一区域的 Key Vault 中的密钥标记为 TDE 保护器。 如果中断影响同一区域中的密钥保管库，系统会自动切换到远程区域中的密钥保管库。

### <a name="recommendations-when-configuring-tde-protector"></a>配置 TDE 保护器时的建议
- 将 TDE 保护器的副本保存在安全位置，或将其托管到托管服务。

- 如果密钥是在 Key Vault 中生成的，请在首次使用 AKV 中的密钥之前创建密钥备份。 备份只能还原到 Azure Key Vault。 详细了解 [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) 命令。

- 每次对密钥进行了任何更改（例如，密钥属性、标记、ACL）后，都创建新的备份。

- 轮换密钥时保留 Key Vault 中密钥的**先前版本**，以便可以还原旧数据库备份。 当数据库的 TDE 保护程序更改时，数据库的旧备份**不会更新**为使用最新的 TDE 保护程序。 在还原时，每个备份需要包含创建该备份时用于加密该备份的 TDE 保护器。 可以遵照[使用 PowerShell 轮换透明数据加密保护器](transparent-data-encryption-byok-azure-sql-key-rotation.md)中的说明执行密钥轮换。

- 即使是在切换到服务管理的密钥之后，也应该保留 AKV 中以前使用的所有密钥。 这可以确保能够使用 AKV 中存储的 TDE 保护器还原数据库备份。  通过 Azure Key Vault 创建的 TDE 保护器必须一直保留到使用服务托管的密钥创建所有剩余存储的备份为止。 使用 [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) 创建这些密钥的可恢复备份副本。

- 若要在出现安全事件期间删除可能已泄露的密钥并避免数据丢失的风险，请遵循[删除可能已泄露的密钥](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)中所述的步骤。

## <a name="inaccessible-tde-protector"></a>不可访问的 TDE 保护器

如果将透明数据加密配置为使用客户管理的密钥，必须持续访问 TDE 保护器才能使数据库保持联机状态。 如果服务器失去了对 AKV 中客户管理的 TDE 保护器的访问权限，则在最长 10 分钟后，数据库将开始拒绝所有连接，同时显示相应的错误消息，并将其状态更改为“不可访问”。** 对于处于“不可访问”状态的数据库，唯一允许的操作是将其删除。

> [!NOTE]
> 如果数据库由于间歇性网络中断而不可访问，则无需采取任何措施，数据库即可自动恢复联机状态。

还原对密钥的访问权限后，使数据库重新联机需要额外的时间并执行其他步骤，具体取决于无法访问密钥的持续间和数据库中的数据大小：

- 如果在 8 小时内还原了密钥访问权限，数据库将在下一小时自动修复。

- 如果在超过8小时后恢复密钥访问权限，则无法进行自动修复，并使数据库恢复需要门户上的其他步骤，并且可能需要很长时间，具体取决于数据库的大小。 数据库重新联机后，以前配置的服务器级别设置（如[故障转移组](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group)配置、时间点还原历史记录和标记）**将丢失**。 因此，我们建议实施一个通知系统，用于在 8 小时内识别和解决基础密钥访问问题。

### <a name="accidental-tde-protector-access-revocation"></a>意外的 TDE 保护器访问权限吊销

对 Key Vault 拥有足够访问权限的某人可能会意外地通过以下方式禁用服务器对密钥的访问权限：

- 在服务器中撤消 Key Vault 的 *get*、*wrapKey* 或 *unwrapKey* 权限

- 删除密钥

- 删除 Key Vault

- 更改 Key Vault 的防火墙规则

- 在 Azure Active Directory 中删除服务器的托管标识

详细了解[数据库不可访问的常见原因](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)。

## <a name="monitoring-of-the-customer-managed-tde"></a>监视客户管理的 TDE

若要监视数据库的状态并针对 TDE 保护器访问权限的丢失启用警报，请配置以下 Azure 功能：
- [Azure 资源运行状况](https://docs.microsoft.com/azure/service-health/resource-health-overview)。 首次与失去 TDE 保护器访问权限的不可访问的数据库建立连接遭到拒绝后，该数据库将显示为“不可用”。
- [活动日志](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications)。访问客户管理的 Key Vault 中的 TDE 保护器失败时，会将相应的条目添加到活动日志。  为这些事件创建警报可以尽快恢复访问权限。
- [操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)可以定义为根据你的偏好发送通知和警报，例如电子邮件/短信/推送/语音、逻辑应用、WEBHOOK、ITSM 或自动化 Runbook。

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>使用客户管理的 TDE 进行数据库备份和还原

使用 Key Vault 中的密钥通过 TDE 加密数据库后，所有新生成的备份也会使用同一个 TDE 保护器进行加密。 更改 TDE 保护器后，数据库的旧备份**不会更新**为使用最新的 TDE 保护器。

若要还原使用 Key Vault 中的 TDE 保护器加密的备份，请确保密钥材料可供目标服务器使用。 因此，我们建议在 Key Vault 中保留所有旧版 TDE 保护器，以便可以还原数据库备份。

> [!IMPORTANT]
> 无论何时，为服务器设置的 TDE 保护器都不能超过一个。 此保护器在 Azure 门户边栏选项卡中标记为“使该密钥成为默认的 TDE 保护器”。 但是，可将其他多个密钥链接到服务器，而无需将其标记为 TDE 保护器。 这些密钥不是用于保护 DEK，而是在从备份还原期间使用（如果备份文件是使用具有相应指纹的密钥加密的）。

如果还原备份所需的密钥对于目标服务器不再可用，则还原尝试： "目标服务器`<Servername>`无法访问在时间戳 #1> 和\< \<时间戳 #2> 之间创建的所有 AKV uri。 请在还原所有 AKV URI 之后重试操作。”

若要缓解此问题，请对目标 SQL 数据库逻辑服务器运行 [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet，或对目标托管实例运行 [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey)，以返回可用密钥的列表并标识缺少的密钥。 为了确保可以还原所有备份，请确保要还原的目标服务器有权访问全部所需的密钥。 这些密钥无需标记为 TDE 保护器。

若要详细了解 SQL 数据库的备份恢复，请参阅[恢复 Azure SQL 数据库](sql-database-recovery-using-backups.md)。 若要了解有关 SQL 池备份恢复的详细信息，请参阅[恢复 Sql 池](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)。 有关使用托管实例进行 SQL Server 的本机备份/还原，请参阅[快速入门：将数据库还原到托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)

日志文件的其他注意事项：已备份的日志文件仍然使用原始 TDE 保护程序进行加密，即使它已被旋转并且数据库现在使用新的 TDE 保护程序。  还原时，需要使用这两个密钥来还原数据库。  如果日志文件使用 Azure Key Vault 中存储的 TDE 保护器，则还原时需要此密钥，即使数据库同时已改用服务托管的 TDE。

## <a name="high-availability-with-customer-managed-tde"></a>使用客户管理的 TDE 实现高可用性

即使没有为服务器配置异地冗余，我们也强烈建议将服务器配置为使用两个不同区域中的包含相同密钥材料的两个不同 Key Vault。 若要实现此目的，可以使用与服务器共置在同一区域中的主要 Key Vault 来创建一个 TDE 保护器，并将密钥克隆到位于不同 Azure 区域中的 Key Vault，以便在主要 Key Vault 遇到服务中断时，服务器能够访问另一个 Key Vault，同时让数据库保持正常运行。

使用 Backup-AzKeyVaultKey cmdlet 从主要 Key Vault 检索加密格式的密钥，然后使用 Restore-AzKeyVaultKey cmdlet 并指定第二个区域中的 Key Vault 来克隆密钥。 或者，使用 Azure 门户来备份和还原密钥。 不应将另一区域的辅助 Key Vault 中的密钥标记为 TDE 保护器，甚至不允许这样做。

 如果发生了影响主要 Key Vault 的服务中断，只有在满足上述条件时，系统才会自动切换到辅助 Key Vault 中具有相同指纹的另一个已链接密钥（如果存在）。 请注意，如果由于撤销了访问权限或者由于删除了密钥或 Key Vault 而无法访问 TDE 保护器，则不会发生这种切换，因为这可能意味着客户有意地想要限制服务器访问密钥。

![单服务器高可用性](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>异地灾难恢复和客户管理的 TDE

在[活动异地复制](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)和[故障转移组](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group)方案中，涉及的每个服务器都需要一个单独的 Key Vault，该 Key Vault 必须与服务器共置在同一个 Azure 区域中。 客户负责使各个 Key Vault 中的密钥材料保持一致，使异地辅助节点保持同步，并在主要节点由于区域发生服务中断或者触发故障转移而不可访问时，辅助节点可以使用客户本地 Key Vault 中的同一密钥接管工作。 最多可以配置四个辅助节点，不支持链接（辅助节点的辅助节点）。

为了避免由于密钥材料不完整而在建立异地复制或者在异地复制期间出现问题，请务必在配置客户管理的 TDE 时遵循以下规则：

- 涉及的所有 Key Vault 必须具有相同的属性，并为相应的服务器提供相同的访问权限。

- 涉及的所有 Key Vault 必须包含相同的密钥材料。 这不仅适用于当前的 TDE 保护器，而且还适用于以前在备份文件中使用的所有 TDE 保护器。

- TDE 保护器的初始设置和轮换必须先在辅助节点上执行，然后在主要节点上执行。

![故障转移组和异地灾难恢复](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

若要测试故障转移，请遵循[活动异地复制概述](sql-database-geo-replication-overview.md)中的步骤。 应定期执行此操作，以确认 SQL 对两个 Key Vault 保持访问权限。

## <a name="next-steps"></a>后续步骤

建议查看以下 PowerShell 示例脚本，以了解可对客户管理的 TDE 执行的常见操作：

- [使用 PowerShell 轮换 SQL 数据库的透明数据加密保护器](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [使用 PowerShell 删除 SQL 数据库的透明数据加密 (TDE) 保护器](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [使用 PowerShell 通过自己的密钥管理托管实例中的透明数据加密](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
