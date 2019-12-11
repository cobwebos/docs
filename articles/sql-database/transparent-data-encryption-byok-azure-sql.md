---
title: 客户托管的透明数据加密（TDE）
description: SQL 数据库和数据仓库 Azure Key Vault 的透明数据加密 (TDE) 的“创建自己的密钥”(BYOK) 支持。 支持 BYOK 的 TDE 概述、优势、工作原理、注意事项和建议。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 11/19/2019
ms.openlocfilehash: c8a1e2a19fa3c8691cdb381669dc3d4db189c42d
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995841"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Azure SQL 透明数据加密与客户托管的密钥

Azure SQL[透明数据加密（TDE）](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)与客户托管的密钥可用于静态数据保护的创建自己的密钥（BYOK）方案，并使组织能够在密钥和数据的管理中实现职责分离。 通过客户托管的透明数据加密，客户负责和完全控制密钥生命周期管理（密钥创建、上传、旋转、删除）、密钥使用权限，以及对密钥的操作进行审核。

在此方案中，用于加密数据库加密密钥（DEK）（称为 TDE 保护程序）的密钥是客户托管的非对称密钥，存储在客户拥有的和客户管理的[Azure Key Vault （AKV）](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)中，后者是基于云的外部密钥管理系统。 Key Vault 是适用于 RSA 加密密钥的高度可用且可缩放的安全存储，可选择通过 FIPS 140-2 第2级验证的硬件安全模块（Hsm）进行支持。 它不允许直接访问存储的密钥，但使用授权实体的密钥提供加密/解密服务。 密钥保管库可以从本地 HSM 设备生成密钥保管库，将其导入或[传输到密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)。

对于 Azure SQL 数据库和 Azure SQL 数据仓库，将在逻辑服务器级别设置 TDE 保护程序，并将其继承给与该服务器关联的所有加密数据库。 对于 Azure SQL 托管实例，TDE 保护程序在实例级别设置，并由该实例上的所有加密数据库继承。 在整个文档中，术语 "*服务器*" 同时引用 SQL 数据库逻辑服务器和托管实例，除非另行说明。 

> [!IMPORTANT]
> 对于使用服务托管的 TDE 的用户，如果想要开始使用客户管理的 TDE，则在切换的过程中数据将保持加密，且不会造成停机，也不会重新加密数据库文件。 从服务托管的密钥切换到客户托管的密钥只需要重新加密 DEK，这是一项快速、联机的操作。

## <a name="benefits-of-the-customer-managed-tde"></a>客户托管的 TDE 的优点

客户托管的 TDE 为客户提供以下好处：

- 完全和精细控制 TDE 保护程序的使用情况和管理;

- TDE 保护程序使用情况的透明度;

- 能够实现组织中密钥和数据的管理中的职责分离;

- Key Vault 管理员可以撤消密钥访问权限，以使加密数据库不可访问;

- AKV 中密钥的集中管理;

- 比最终客户更好地信任，因为 AKV 的设计使 Microsoft 无法看到或提取加密密钥;

## <a name="how-customer-managed-tde-works"></a>客户托管的 TDE 的工作原理

![客户管理的 TDE 的设置和功能](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

为了使服务器能够使用存储在 AKV 中的 TDE 保护程序来加密 DEK，key vault 管理员需要使用其唯一 AAD 标识向服务器授予以下访问权限：

- **获取**-用于检索 Key Vault 中的密钥的公共部分和属性

- **wrapKey** -能够保护（加密） DEK

- **unwrapKey** -能够取消对 DEK 的保护（解密）

Key vault 管理员还可以[对 key vault 审核事件启用日志记录](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)，以便以后可以对其进行审核。

当服务器配置为使用来自 AKV 的 TDE 保护程序时，服务器会将每个启用了 TDE 的数据库的 DEK 发送到密钥保管库以进行加密。 Key vault 返回已加密的 DEK，然后将其存储在用户数据库中。

如果需要，服务器会将受保护的 DEK 发送到密钥保管库进行解密。

如果启用了日志记录，审核员可以使用 Azure Monitor 查看 key vault AuditEvent 日志。


## <a name="requirements-for-configuring-customer-managed-tde"></a>配置客户管理的 TDE 的要求

### <a name="requirements-for-configuring-akv"></a>配置 AKV 的要求

- Key vault 和 SQL 数据库/托管实例必须属于同一个 Azure Active Directory 租户。 不支持跨租户的密钥保管库和服务器交互。 若要以后移动资源，则必须重新配置使用 AKV 的 TDE。 了解有关[移动资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)的详细信息。

- 必须在密钥保管库上启用[软删除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)功能，以防止意外删除意外密钥（或密钥保管库）。 软删除的资源将保留90天，除非客户在此期间恢复或清除。 “恢复”和“清除”操作在密钥保管库访问策略中各自具有相关联的权限。 软删除功能在默认情况下处于关闭状态，可通过[Powershell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete)或[CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete)启用。 不能通过 Azure 门户启用此功能。  

- 使用 Azure Active Directory 标识向 SQL 数据库服务器或托管实例授予对密钥保管库的访问权限（get、wrapKey、unwrapKey）。 使用 Azure 门户时，会自动创建 Azure AD 标识。 使用 PowerShell 或 CLI 时，必须显式创建 Azure AD 标识并且应验证完成。 有关使用 PowerShell 进行配置的详细分步说明，请参阅[配置支持 BYOK 的 TDE](transparent-data-encryption-byok-azure-sql-configure.md) 和[配置支持托管实例 BYOK 的 TDE](https://aka.ms/sqlmibyoktdepowershell)。

- 使用带有 AKV 的防火墙时，必须启用 "*允许受信任的 Microsoft 服务" 选项来绕过防火墙*。

### <a name="requirements-for-configuring-tde-protector"></a>配置 TDE 保护程序的要求

- TDE 保护程序只能为非对称、RSA 2048 或 RSA HSM 2048 密钥。

- 密钥激活日期（如果已设置）必须是过去的日期和时间。 到期日期（如果已设置）必须是将来的日期和时间。

- 密钥必须处于 "*已启用*" 状态。

- 如果要将现有密钥导入到密钥保管库中，请确保以支持的文件格式（.pfx、byok 或 backup）提供该密钥。

## <a name="recommendations-when-configuring-customer-managed-tde"></a>配置客户托管的 TDE 时的建议

### <a name="recommendations-when-configuring-akv"></a>配置 AKV 时的建议

- 在单个订阅中，最多可将500常规用途或200业务关键数据库关联到一个密钥保管库，以确保服务器访问 key vault 中的 TDE 保护程序时的高可用性。 这些数字以[密钥保管库服务限制](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)中的经验和文档为基础。 此处的目的是在服务器故障转移后防止出现问题，因为它会触发对保管库的关键操作，因为该服务器中有数据库。 

- 设置密钥保管库上的资源锁，以控制谁可以删除此重要资源并防止意外或未经授权的删除。 详细了解[资源锁](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)。

- 对所有加密密钥启用审核和报告： Key vault 提供了可轻松注入到其他安全信息和事件管理工具的日志。 Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)是已集成的服务的一个示例。

- 将每个服务器与驻留在不同区域的两个密钥保管库相链接，并保存相同的密钥材料，以确保加密数据库的高可用性。 将密钥保管库中的密钥仅标记为与 TDE 保护程序位于同一区域。 系统将使用

### <a name="recommendations-when-configuring-tde-protector"></a>配置 TDE 保护程序时的建议
- 将 TDE 保护程序的副本保存在安全的位置，或将其托管到托管服务。 

- 如果在密钥保管库中生成密钥，请在第一次在 AKV 中使用该密钥之前创建密钥备份。 只能将备份还原到 Azure Key Vault。 了解有关[AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)命令的详细信息。

- 无论何时对密钥进行了任何更改（例如，密钥属性、标记、Acl），都要创建一个新备份。

- 轮换密钥时保留 Key Vault 中密钥的**先前版本**，以便可以还原旧数据库备份。 当数据库的 TDE 保护程序更改时，数据库的旧备份**不会更新**为使用最新的 TDE 保护程序。 在还原时，每个备份都需要在创建时对它进行加密的 TDE 保护程序。 可以遵照[使用 PowerShell 轮换透明数据加密保护器](transparent-data-encryption-byok-azure-sql-key-rotation.md)中的说明执行密钥轮换。

- 即使在切换到服务托管的密钥后，仍在 AKV 中保留所有以前使用的密钥。 它可确保在 AKV 中存储的 TDE 保护程序中还原数据库备份。  必须保留使用 Azure Key Vault 创建的 TDE 保护程序，直到使用服务托管的密钥创建了所有剩余的存储备份。 使用[AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)创建这些密钥的可恢复副本。

- 若要在安全事件期间删除可能泄露的密钥，而不会造成数据丢失的风险，请执行[删除可能已泄露的密钥](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)中的步骤。

## <a name="inaccessible-tde-protector"></a>无法访问 TDE 保护程序

如果将透明数据加密配置为使用客户管理的密钥，则数据库保持联机状态需要持续访问 TDE 保护程序。 如果服务器在 AKV 中失去了对客户托管的 TDE 保护程序的访问权限，则在最多10分钟内，数据库将开始拒绝所有连接与相应的错误消息，并将其状态更改为 "*不可访问*"。 不能访问的数据库中允许的唯一操作是将其删除。

> [!NOTE]
> 如果数据库由于间歇性网络中断而无法访问，则无需执行任何操作，数据库也会自动返回到联机状态。

在恢复对密钥的访问权限后，使数据库重新联机需要额外的时间和步骤，这可能会因所用时间而异，但不需要访问数据库中数据的密钥和大小：

- 如果在8小时内还原密钥访问，数据库将在接下来的一小时内自动修复。

- 如果在超过8小时后恢复密钥访问权限，则无法进行自动修复，并使数据库恢复工作可能需要很长时间，具体取决于数据库的大小，需要打开支持票证。 数据库重新联机后，以前配置的服务器级别设置（如[故障转移组](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group)配置、时间点还原历史记录和标记）将丢失。 因此，建议实施通知系统，以便在8小时内确定并解决基础密钥访问问题。

### <a name="accidental-tde-protector-access-revocation"></a>意外的 TDE 保护程序访问吊销

有权访问密钥保管库的用户可能会意外地禁用对密钥的服务器访问权限：

- 从服务器撤消 key vault 的*get*、 *wrapKey*、 *unwrapKey*权限

- 删除密钥

- 删除密钥保管库

- 更改密钥保管库的防火墙规则

- 正在删除 Azure Active Directory 中服务器的托管标识

了解更多有关[数据库无法访问的常见原因](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)的信息。

## <a name="monitoring-of-the-customer-managed-tde"></a>监视客户托管的 TDE

若要监视数据库状态并为丢失 TDE 保护程序访问启用警报，请配置以下 Azure 功能：
- [Azure 资源运行状况](https://docs.microsoft.com/azure/service-health/resource-health-overview)。 在第一次连接到数据库后，无法访问的数据库将显示为 "不可用"。
- [活动日志](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications)当客户管理的密钥保管库中的 TDE 保护程序发生故障时，会将条目添加到活动日志。  通过为这些事件创建警报，你可以尽快恢复访问权限。
- [操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)可以定义为根据你的偏好发送通知和警报，例如电子邮件/短信/推送/语音、逻辑应用、WEBHOOK、ITSM 或自动化 Runbook。

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>通过客户托管的 TDE 进行数据库备份和还原

使用 Key Vault 中的密钥对数据库进行加密后，所有新生成的备份也将使用相同的 TDE 保护程序进行加密。 更改 TDE 保护程序时，**不会更新**数据库的旧备份以使用最新的 TDE 保护程序。

若要使用 Key Vault 中的 TDE 保护程序还原加密的备份，请确保密钥材料可供目标服务器使用。 因此，建议你将所有旧版本的 TDE 保护程序保留在密钥保管库中，以便可以还原数据库备份。 

> [!IMPORTANT]
> 在任何时候，为服务器设置的 TDE 保护程序都不能超过一个。 这是在 Azure 门户边栏选项卡中标记为 "使密钥成为默认的 TDE 保护程序" 的密钥。 但是，可以将多个附加密钥链接到服务器，而无需将其标记为 TDE 保护程序。 这些密钥不用于保护 DEK，但如果备份文件是使用具有相应指纹的密钥加密的，则可以在从备份还原期间使用。

如果还原备份所需的密钥对于目标服务器不再可用，则还原尝试： "目标服务器 `<Servername>` 无权访问在 \<时间戳之间创建的所有 AKV Uri #1 > 和 \<时间戳 #2 >。 请在还原所有 AKV Uri 后重试操作。 "

若要缓解这种情况，请运行面向目标的 SQL 数据库逻辑服务器的[AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet 或目标托管实例的[AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) ，以返回可用密钥列表并标识缺失的密钥列表。 若要确保可以还原所有备份，请确保还原目标服务器有权访问所需的所有密钥。 这些密钥无需标记为 TDE 保护程序。

若要详细了解 SQL 数据库的备份恢复，请参阅[恢复 Azure SQL 数据库](sql-database-recovery-using-backups.md)。 若要详细了解 SQL 数据仓库的备份恢复，请参阅[恢复 Azure SQL 数据仓库](../sql-data-warehouse/backup-and-restore.md)。 有关使用托管实例进行 SQL Server 的本机备份/还原，请参阅[快速入门：将数据库还原到托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) 

日志文件的其他注意事项：已备份的日志文件仍然使用原始 TDE 保护程序进行加密，即使它已被旋转并且数据库现在使用新的 TDE 保护程序。  还原时，需要使用这两个密钥来还原数据库。  如果日志文件使用存储在 Azure Key Vault 中的 TDE 保护程序，则还原时需要此密钥，即使数据库在此期间已更改为使用服务托管的 TDE。

## <a name="high-availability-with-customer-managed-tde"></a>通过客户托管的 TDE 实现高可用性

即使在没有为服务器配置异地冗余的情况下，也强烈建议将服务器配置为在两个不同的区域中使用两个不同的密钥保管库。 可以通过使用与服务器相同的区域中的主密钥保管库创建 TDE 保护程序来完成此操作，并将密钥克隆到另一个 Azure 区域中的密钥保管库，使服务器有权访问第二个密钥保管库，以确保 primary key vault exper当数据库启动并运行时 ience 中断。 

使用 AzKeyVaultKey cmdlet 从主密钥保管库中检索加密格式的密钥，然后使用 AzKeyVaultKey cmdlet 并在第二个区域中指定密钥保管库来克隆该密钥。 或者，使用 Azure 门户来备份和还原密钥。 Se 其他区域的辅助密钥保管库中的密钥不应标记为 TDE 保护程序，甚至不允许这样做。

 如果中断影响主密钥保管库，并且只有这样，系统会自动切换到辅助密钥保管库中具有相同指纹的其他链接密钥（如果存在）。 请注意，如果 TDE 保护程序由于吊销访问权限而无法访问，或者由于删除了密钥或密钥保管库，则不会发生此开关，因为这可能表明客户有意要限制服务器访问密钥。

![单服务器 HA](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>异地灾难恢复和客户管理的 TDE

在[活动异地复制](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)和[故障转移组](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group)方案中，所涉及的每个服务器都需要一个单独的密钥保管库，该密钥保管库必须与服务器共存于同一 Azure 区域中。 客户负责使密钥保管库中的密钥材料保持一致，以便异地辅助副本处于同步状态，并且如果由于区域中的服务中断而无法访问主保管库中的密钥，则可以接管该密钥，并触发故障转移. 最多可配置四个辅助数据库，并且不支持链接（辅助副本）。

若要避免在建立或进行地域复制的过程中因密钥材料不完整而发生的问题，请务必在配置客户托管的 TDE 时遵循这些规则：

- 涉及的所有密钥保管库必须具有相同的属性，并且具有与各自服务器相同的访问权限。

- 涉及的所有密钥保管库必须包含相同的密钥材料。 它不仅适用于当前的 TDE 保护程序，还适用于在备份文件中使用的所有以前的 TDE 保护程序。

- TDE 保护程序的初始设置和旋转必须首先在辅助数据库上完成，然后在主副本上执行。

![故障转移组和异地灾难恢复](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

若要测试故障转移，请按照[活动异地复制概述](sql-database-geo-replication-overview.md)中的步骤进行操作。 应定期完成此操作，以确认已维护对两个密钥保管库的访问权限。

## <a name="next-steps"></a>后续步骤

你可能还需要查看以下 PowerShell 示例脚本，以了解如何通过客户托管的 TDE 执行常见操作：

- [使用 PowerShell 为 SQL 数据库旋转透明数据加密保护程序](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [使用 PowerShell 删除用于 SQL 数据库的透明数据加密（TDE）保护程序](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [使用 PowerShell 管理托管实例中的透明数据加密](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
