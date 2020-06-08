---
title: 使用客户托管密钥进行数据加密 - Azure Database for MySQL
description: 使用客户托管密钥进行 Azure Database for MySQL 数据加密，可创建自己的密钥 (BYOK) 来保护静态数据。 它还允许组织在管理密钥和数据时实现职责分离。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 24b52042e037e998069550599ca006eded70d1c4
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849717"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>使用客户托管密钥进行 Azure Database for MySQL 数据加密

> [!NOTE]
> 此时，必须请求访问权限才能使用此功能。 为此，请联系 AskAzureDBforMySQL@service.microsoft.com。

针对 Azure Database for MySQL 使用客户托管密钥进行数据加密，可创建自己的密钥 (BYOK) 来保护静态数据。 它还允许组织在管理密钥和数据时实现职责分离。 通过客户托管的加密，密钥的生命周期、密钥使用权限以及对密钥操作的审核都由你负责和完全控制。

在服务器级别使用客户托管密钥为 Azure Database for MySQL 进行数据加密。 对于给定的服务器，客户托管密钥（称为密钥加密密钥 (KEK)）用于加密服务使用的数据加密密钥 (DEK)。 KEK 是存储在客户拥有和客户托管的 [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) 实例中的非对称密钥。 本文稍后将更详细地描述密钥加密密钥 (KEK) 和数据加密密钥 (DEK)。

Key Vault 是一种基于云的外部密钥管理系统。 具有高可用性，并为 RSA 加密密钥提供可缩放的安全存储，可选地由 FIPS 140-2 级别 2 验证的硬件安全模块 (HSM) 支持。 它不允许直接访问存储的密钥，但为授权实体提供加密和解密服务。 Key Vault 可以生成密钥，并将其导入，或者[从本地 HSM 设备传输密钥](../key-vault/key-Vault-hsm-protected-keys.md)。

> [!NOTE]
> 此功能适用于所有 Azure 区域，其中 Azure Database for MySQL 支持“常规用途”和“内存优化”定价层。

## <a name="benefits"></a>优点

适用于 Azure Database for MySQL 的数据加密提供了以下优势：

* 数据访问完全由你控制，你可以删除密钥并使数据库无法访问 
* 完全控制密钥生命周期，包括根据公司策略轮换密钥
* Azure Key Vault 中密钥的集中管理和组织
* 能够实现安全专员、DBA 和系统管理员之间的职责分离


## <a name="terminology-and-description"></a>术语和说明

**数据加密密钥 (DEK)** ：对称 AES256 密钥，用于加密数据分区或块。 使用不同的密钥加密每个数据块可以增加加密分析攻击的难度。 资源提供程序或应用程序实例需要 DEK 访问权限才能加密和解密特定的块。 将 DEK 替换为新密钥时，仅其关联的块中的数据需要使用新密钥重新加密。

**密钥加密密钥 (KEK)** ：用于加密 DEK 的加密密钥。 永不离开 Key Vault 的 KEK 允许对 DEK 本身进行加密和控制。 具有 KEK 访问权限的实体可能不同于需要 DEK 的实体。 由于解密 DEK 需要 KEK，因此 KEK 实际上构成了一个单点机制：删除 KEK 即可删除 DEK。

使用 KEK 加密的 DEK 单独存储。 只有有权访问 KEK 的实体才能解密这些 DEK。 有关详细信息，请参阅[静态加密中的安全性](../security/fundamentals/encryption-atrest.md)。

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>使用客户托管密钥进行数据加密的工作原理

![显示“创建自己的密钥”概述的关系图](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

若要使 MySQL 服务器使用存储在 Key Vault 中的客户托管密钥对 DEK 进行加密，Key Vault 管理员将授予服务器以下访问权限：

* **get**：用于检索密钥保管库中密钥的公共部分和属性。
* **wrapKey**：能够加密 DEK。
* **unwrapKey**：能够解密 DEK。

密钥保管库管理员还可以[启用 Key Vault 审核事件的日志记录](../azure-monitor/insights/azure-key-vault.md)，以便以后可以对其进行审核。

当服务器配置为使用存储在密钥保管库中的客户托管密钥时，服务器将 DEK 发送到密钥保管库进行加密。 Key Vault 返回存储在用户数据库中的已加密 DEK。 同样，如果需要，服务器将受保护的 DEK 发送到密钥库进行解密。 如果启用了日志记录，则审核员可以使用 Azure Monitor 查看 Key Vault 审核事件日志。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 配置数据加密的要求

下面是配置 Key Vault 的要求：

* Key Vault 和 Azure Database for MySQL 必须属于同一个 Azure Active Directory (Azure AD) 租户。 不支持跨租户的 Key Vault 和服务器交互。 以后移动资源需要重新配置数据加密。
* 启用密钥保管库上的软删除功能，以防止在意外删除密钥（或 Key Vault）时丢失数据。 被软删除的资源将保留 90 天，除非用户同时恢复或清除这些资源。 “恢复”和“清除”操作具有与 Key Vault 访问策略相关的各自权限。 默认情况下，软删除功能处于关闭状态，但你可以通过 PowerShell 或 Azure CLI 启用它（请注意，无法通过 Azure 门户启用此功能）。
* 通过使用其唯一的托管标识授予具有 get、wrapKey 和 unwrapKey 权限的密钥保管库的 Azure Database for MySQL 访问权限。 在 Azure 门户中，在 MySQL 上启用数据加密时，将自动创建唯一标识。 有关使用 Azure 门户时的详细分步说明，请参阅[为 MySQL 配置数据加密](howto-data-encryption-portal.md)。

下面是配置客户托管密钥的要求：

* 用于加密 DEK 的客户托管密钥只能是非对称的 RSA 2048。
* 密钥激活日期（如果已设置）必须是过去的日期和时间。 到期日期（如果已设置）必须是将来的日期和时间。
* 此密钥必须处于“启用”状态。
* 如果要将现有密钥导入密钥保管库，请确保以受支持的文件格式（`.pfx`、`.byok`、`.backup`）提供该密钥。

## <a name="recommendations"></a>建议

通过使用客户托管密钥使用数据加密时，以下是配置 Key Vault 的建议：

* 在 Key Vault 中设置资源锁可以控制谁能删除此关键资源，并防止意外或未经授权的删除。
* 对所有加密密钥启用审核和报告。 Key Vault 提供可以轻松注入到其他安全信息和事件管理工具的日志。 Azure Monitor Log Analytics 是已集成的服务的一个示例。
* 确保 Key Vault 和 Azure Database for MySQL 位于同一区域，以确保对 DEK 包装和解包操作的更快访问。
* 将 Azure KeyVault 锁定为仅“专用终结点和所选网络”，并仅允许“受信任的 Microsoft”服务保护资源。

    ![trusted-service-with-AKV](media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png)

以下是配置客户托管密钥的建议：

* 将客户托管密钥的副本保存在安全的位置，或者将其托管到托管服务。

* 如果 Key Vault 生成密钥，请在首次使用密钥之前创建密钥备份。 只能将备份还原到 Key Vault。 有关备份命令的详细信息，请参阅 [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)。

## <a name="inaccessible-customer-managed-key-condition"></a>无法访问客户托管密钥的条件

在 Key Vault 中使用客户托管密钥配置数据加密时，服务器需要持续访问此密钥才能保持联机。 如果服务器在 Key Vault 中失去了对客户托管密钥的访问权限，则服务器将在 10 分钟内开始拒绝所有连接。 服务器会发出相应的错误消息，并将服务器状态更改为“无法访问”。 服务器达到此状态的一些原因包括：

* 如果我们为 Azure Database for MySQL 创建已启用数据加密的时间点还原服务器，则新创建的服务器将处于“无法访问”状态。 可以通过 [Azure 门户](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers)或 [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) 修复此问题。
* 如果我们为 Azure Database for MySQL 创建已启用数据加密的只读副本，则副本服务器将处于“无法访问”状态。 可以通过 [Azure 门户](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers)或 [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) 修复此问题。
* 如果删除 KeyVault，Azure Database for MySQL 将无法访问密钥，并将转到“无法访问”状态。 恢复 [Key Vault](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects) 并重新验证数据加密，以使服务器“可用”。
* 如果我们从 KeyVault 中删除密钥，Azure Database for MySQL 将无法访问密钥，并将转到“无法访问”状态。 恢复[密钥](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects)并重新验证数据加密，以使服务器“可用”。
* 如果存储在 Azure KeyVault 中的密钥过期，则该密钥将变为无效，并且 Azure Database for MySQL 将转换为“无法访问”状态。 使用 [CLI](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-set-attributes) 延长密钥到期日期，然后重新验证数据加密以使服务器“可用”。

### <a name="accidental-key-access-revocation-from-key-vault"></a>从 Key Vault 意外撤消密钥访问

可能会发生这样的情况：对 Key Vault 具有足够访问权限的人意外地通过以下方式禁用了服务器对密钥的访问：

* 从服务器中撤消密钥保管库的 get、wrapKey 和 unwrapKey 权限。
* 正在删除密钥。
* 正在删除密钥保管库。
* 正在更改密钥保管库的防火墙规则。
* 正在删除 Azure AD 中服务器的托管标识。

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>在 Key Vault 中监视客户托管密钥

若要监视数据库状态并启用对丢失透明数据加密保护程序访问的警报，请配置以下 Azure 功能：

* [Azure 资源运行状况](../service-health/resource-health-overview.md)：在拒绝与数据库的第一个连接后，无法访问客户密钥的不可访问数据库显示为“不可访问”。
* [活动日志](../service-health/alerts-activity-log-service-notifications.md)：访问客户托管的 Key Vault 中的客户密钥失败时，会将条目添加到活动日志中。 如果为这些事件创建警报，则可以尽快恢复访问。

* [操作组](../azure-monitor/platform/action-groups.md)：定义这些组以根据首选项向你发送通知和警报。

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>在 Key Vault 中使用客户托管密钥进行还原和复制

在使用客户存储在 Key Vault 中的托管密钥对 Azure Database for MySQL 进行加密后，还将对服务器的任何新创建的副本进行加密。 可以通过本地或异地还原操作，或通过只读副本创建此新副本。 但是，可以更改副本以反映用于加密的新客户的托管密钥。 客户托管密钥更改后，服务器的旧备份将开始使用最新密钥。

若要避免在还原或只读副本创建期间设置客户托管的数据加密时出现问题，在主服务器和还原/副本服务器上执行以下步骤非常重要：

* 从主 Azure Database for MySQL 启动还原或只读副本创建过程。
* 使新创建的服务器（还原/副本）保持无法访问状态，因为其唯一标识尚未授予 Key Vault 的权限。
* 在还原/副本服务器上，重新验证数据加密设置中客户托管密钥，以确保为新创建的服务器提供对存储在 Key Vault 中的密钥的包装和解包权限。

## <a name="next-steps"></a>后续步骤

了解如何[通过 Azure 门户使用客户托管密钥为 Azure database for MySQL 设置数据加密](howto-data-encryption-portal.md)。
