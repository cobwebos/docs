---
title: 使用客户管理的密钥 Azure Database for PostgreSQL 单服务器数据加密
description: 使用客户管理的密钥 Azure Database for PostgreSQL 单服务器数据加密可以为静态数据保护创建自己的密钥（BYOK）。 它还允许组织在密钥和数据的管理中实现职责分离。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 5516bfcb3ed32ba6635943298db2a7773db0a622
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198694"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>使用客户管理的密钥 Azure Database for PostgreSQL 单服务器数据加密

> [!NOTE]
> 此时，你必须请求访问权限才能使用此功能。 为此，请联系 AskAzureDBforPostgreSQL@service.microsoft.com。

通过客户托管的密钥进行数据加密 Azure Database for PostgreSQL 单个服务器，使你可以自带密钥（BYOK）来保护静态数据。 它还允许组织在密钥和数据的管理中实现职责分离。 通过客户托管的加密，你负责和完全控制密钥的生命周期、密钥使用权限，以及对密钥的操作进行审核。

在服务器级别设置 Azure Database for PostgreSQL 单个服务器的客户托管密钥的数据加密。 对于给定服务器，客户托管的密钥称为密钥加密密钥（KEK），用于对服务使用的数据加密密钥（DEK）进行加密。 KEK 是存储在客户拥有的和客户管理的[Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md)实例中的非对称密钥。 本文稍后将更详细地介绍密钥加密密钥（KEK）和数据加密密钥（DEK）。

Key Vault 是一种基于云的外部密钥管理系统。 它高度可用，并为 RSA 加密密钥提供可缩放的安全存储，可以选择通过 FIPS 140-2 第2级验证的硬件安全模块（Hsm）进行支持。 它不允许直接访问存储的密钥，但会向授权实体提供加密和解密服务。 Key Vault 可以生成密钥，导入密钥，或者[从本地 HSM 设备传输](../key-vault/key-Vault-hsm-protected-keys.md)密钥。

> [!NOTE]
> 此功能在所有 Azure Database for PostgreSQL 单一服务器支持 "常规用途" 和 "内存优化" 定价层的 Azure 区域中提供。

## <a name="benefits"></a>优点

Azure Database for PostgreSQL 单一服务器的数据加密具有以下优势：

* 数据访问由你完全控制，可以删除密钥并使数据库无法访问 
*   完全控制密钥生命周期，包括对密钥的轮替以与公司政策保持一致
*   Azure Key Vault 中的集中管理和密钥组织
*   能够在安全专员之间实现职责分离，以及 DBA 和系统管理员

## <a name="terminology-and-description"></a>术语和说明

**数据加密密钥（DEK）** ：用于对分区或数据块进行加密的对称 AES256 密钥。 使用不同的密钥加密每个数据块可以增加加密分析攻击的难度。 资源提供程序或应用程序实例需要 DEK 访问权限才能加密和解密特定的块。 将 DEK 替换为新密钥时，必须使用新密钥重新加密其关联块中的数据。

**密钥加密密钥（KEK）** ：用于对 dek 进行加密的加密密钥。 永远不会离开 Key Vault 的 KEK 允许对 Dek 进行加密和控制。 有权访问 KEK 的实体可能不同于需要 DEK 的实体。 由于解密 DEK 需要 KEK，因此 KEK 实际上构成了一个单点机制：删除 KEK 即可删除 DEK。

用 Kek 加密的 Dek 单独存储。 只有有权访问 KEK 的实体才能解密这些 Dek。 有关详细信息，请参阅[静态加密中的安全性](../security/fundamentals/encryption-atrest.md)。

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>使用客户托管的密钥进行数据加密的方式

![显示创建自己的密钥概述的关系图](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

要使 PostgreSQL 服务器使用存储在 Key Vault 中的客户托管密钥来加密 DEK，Key Vault 管理员向服务器提供以下访问权限：

* **get**：用于检索密钥保管库中密钥的公共部分和属性。
* **wrapKey**：能够对 DEK 进行加密。
* **unwrapKey**：能够解密 DEK。

Key vault 管理员还可以对[Key Vault 审核事件启用日志记录](../azure-monitor/insights/azure-key-vault.md)，以便以后可以对这些事件进行审核。

如果将服务器配置为使用密钥保管库中存储的客户托管密钥，则服务器会将 DEK 发送到加密的密钥保管库。 Key Vault 返回存储在用户数据库中的加密 DEK。 同样，如果需要，服务器会将受保护的 DEK 发送到密钥保管库进行解密。 如果启用了日志记录，审核员可以使用 Azure Monitor 查看 Key Vault 审核事件日志。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>为 Azure Database for PostgreSQL 单个服务器配置数据加密的要求

下面是配置 Key Vault 的要求：

* Key Vault 和 Azure Database for PostgreSQL 单一服务器必须属于同一 Azure Active Directory （Azure AD）租户。 不支持跨租户 Key Vault 和服务器交互。 以后移动资源要求您重新配置数据加密。
* 你必须在密钥保管库上启用软删除功能，以便在发生意外的密钥（或 Key Vault）删除时防止数据丢失。 软删除的资源将保留90天，除非用户同时恢复或清除这些资源。 "恢复" 和 "清除" 操作在 Key Vault 访问策略中具有相关联的权限。 软删除功能在默认情况下处于关闭状态，但你可以通过 PowerShell 或 Azure CLI 启用它（请注意，你无法通过 Azure 门户启用此功能）。
* 使用 get、wrapKey 和 unwrapKey 权限，通过使用其唯一的托管标识，授予 Azure Database for PostgreSQL 单一服务器对 key vault 的访问权限。 在 Azure 门户中，当在 PostgreSQL 单一服务器上启用数据加密时，将自动创建唯一标识。 请参阅使用[Azure 门户 Azure Database for PostgreSQL 单个服务器的数据加密](howto-data-encryption-portal.md)，以获取使用 Azure 门户时的详细分步说明。

* 使用 Key Vault 的防火墙时，必须启用 "**允许受信任的 Microsoft 服务" 选项，以绕过防火墙**。

以下是配置客户管理的密钥的要求：

* 用于加密 DEK 的客户托管密钥只能是非对称的，RSA 2028。
* 密钥激活日期（如果已设置）必须是过去的日期和时间。 到期日期（如果已设置）必须是将来的日期和时间。
* 密钥必须处于 "*已启用*" 状态。
* 如果要将现有密钥导入到密钥保管库中，请确保以支持的文件格式（`.pfx`、`.byok`、`.backup`）提供该密钥。

## <a name="recommendations"></a>建议

使用客户托管的密钥进行数据加密时，以下是配置 Key Vault 的建议：

* 设置 Key Vault 的资源锁，以控制谁可以删除此重要资源并防止意外或未经授权的删除。
* 对所有加密密钥启用审核和报告功能。 Key Vault 提供了可轻松注入到其他安全信息和事件管理工具中的日志。 Azure Monitor Log Analytics 是已集成的服务的一个示例。

* 确保 Key Vault 和 Azure Database for PostgreSQL 单一服务器位于同一区域，以确保更快地访问 DEK wrap 和解包操作。

以下是配置客户管理的密钥的建议：

* 将客户托管的密钥副本保存在安全的位置，或将其托管到托管服务。

* 如果 Key Vault 生成密钥，请在首次使用该密钥之前创建密钥备份。 只能将备份还原到 Key Vault。 有关 backup 命令的详细信息，请参阅[AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)。

## <a name="inaccessible-customer-managed-key-condition"></a>无法访问客户托管的密钥条件

在 Key Vault 中使用客户管理的密钥配置数据加密时，服务器需要持续访问此密钥才能保持联机。 如果服务器在 Key Vault 中失去了对客户管理的密钥的访问权限，则服务器将在10分钟内开始拒绝所有连接。 服务器会发出相应的错误消息，并将服务器状态更改为 "*不可访问*"。 在处于此状态的数据库上唯一允许的操作是将其删除。

### <a name="accidental-key-access-revocation-from-key-vault"></a>Key Vault 的意外密钥访问吊销

可能会出现以下情况：具有足够的访问权限 Key Vault 意外禁用了对密钥的服务器访问的用户：

* 正在从服务器撤消 key vault 的 get、wrapKey 和 unwrapKey 权限。
* 删除密钥。
* 正在删除密钥保管库。
* 更改密钥保管库的防火墙规则。

* 正在删除 Azure AD 中服务器的托管标识。

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>在 Key Vault 中监视客户托管的密钥

若要监视数据库状态，并为丢失透明数据加密保护程序访问启用警报，请配置以下 Azure 功能：

* [Azure 资源运行状况](../service-health/resource-health-overview.md)：在第一次连接到数据库后，无法访问的数据库将显示为 "无法访问"。
* [活动日志](../service-health/alerts-activity-log-service-notifications.md)：当对客户管理的 Key Vault 中的客户密钥的访问失败时，条目将添加到活动日志中。 如果为这些事件创建警报，你可以尽快恢复访问权限。

* [操作组](../azure-monitor/platform/action-groups.md)：定义这些组以根据你的偏好向你发送通知和警报。

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>在 Key Vault 中使用客户的托管密钥进行还原和复制

使用在 Key Vault 中存储的客户托管密钥对 Azure Database for PostgreSQL 单个服务器进行加密后，任何新创建的服务器副本也会加密。 可以通过本地或异地还原操作或通过读取副本建立此新副本。 但是，可以更改副本以反映新客户的加密的托管密钥。 当客户管理的密钥更改时，服务器的旧备份将使用最新密钥开始。

若要避免在还原或读取副本创建过程中设置客户管理的数据加密时出现问题，请务必在主服务器和还原/副本服务器上执行以下步骤：

* 从主 Azure Database for PostgreSQL 单一服务器启动还原或读取副本创建过程。
* 将新创建的服务器（还原/副本）保持为不可访问的状态，因为它的唯一标识尚未获得 Key Vault 的权限。
* 在还原/副本服务器上，重新验证数据加密设置中客户管理的密钥。 这可确保向新创建的服务器提供对存储在 Key Vault 中的密钥的换行和解包权限。

## <a name="next-steps"></a>后续步骤

了解如何[通过使用 Azure 门户，使用适用于 PostgreSQL 单一服务器的 Azure 数据库的客户托管密钥设置数据加密](howto-data-encryption-portal.md)。

