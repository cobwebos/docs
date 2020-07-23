---
title: 使用客户管理的密钥进行数据加密 - Azure Database for PostgreSQL - 单一服务器
description: Azure Database for PostgreSQL 单一服务器使用客户管理的密钥进行数据加密，让你能够创建自己的密钥 (BYOK) 来保护静态数据。 通过它，组织还可在管理密钥和数据时实现职责分离。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 1300ef64b6081135c400baa10aa73b8139aec170
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86025584"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>使用客户管理的密钥对 Azure Database for PostgreSQL 单一服务器进行数据加密

通过使用客户管理的密钥对 Azure Database for PostgreSQL 单一服务器进行数据加密，让你能够创建自己的密钥 (BYOK) 来保护静态数据。 通过它，组织还可在管理密钥和数据时实现职责分离。 通过客户托管的加密，密钥的生命周期、密钥使用权限以及对密钥操作的审核都由你负责和完全控制。

在服务器级别使用客户管理的密钥对 Azure Database for PostgreSQL 单一服务器进行数据加密。 客户管理的密钥被称为密钥加密密钥 (KEK)，它在给定的服务器中用于对该服务使用的数据加密密钥 (DEK) 进行加密。 KEK 是一种非对称密钥，它存储在客户自有和客户管理的 [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) 实例中。 本文稍后将更详细地描述密钥加密密钥 (KEK) 和数据加密密钥 (DEK)。

Key Vault 是一种基于云的外部密钥管理系统。 它具有高可用性，并为 RSA 加密密钥提供可扩展的安全存储，根据需要由 FIPS 140-2 级别 2 验证的硬件安全模块 (HSM) 提供支持。 它不允许直接访问存储的密钥，而是为已获授权的实体提供加密和解密服务。 Key Vault 可生成密钥并将其导入，或者[从本地 HSM 设备传输密钥](../key-vault/key-Vault-hsm-protected-keys.md)。

> [!NOTE]
> 此功能适用于所有 Azure 区域，其中 Azure Database for PostgreSQL 单一服务器支持“常规用途”和“内存优化”定价层。

## <a name="benefits"></a>优点

Azure Database for PostgreSQL 单一服务器的数据加密具有以下优势：

* 数据访问完全由你控制，你可删除密钥并使数据库无法访问 
*    可完全控制密钥生命周期，包括根据公司策略轮替密钥
*    在 Azure Key Vault 中集中管理和整理密钥
*    可实现安全专员与 DBA 和系统管理员之间的职责分离

## <a name="terminology-and-description"></a>术语和说明

**数据加密密钥 (DEK)** ：对称 AES256 密钥，用于加密数据分区或数据块。 使用不同的密钥加密每个数据块可以增加加密分析攻击的难度。 资源提供程序或应用程序实例需要 DEK 访问权限才能加密和解密特定的块。 将 DEK 替换为新密钥时，只需使用新密钥对其关联的块中的数据重新加密。

**密钥加密密钥 (KEK)** ：用于加密 DEK 的加密密钥。 KEK 始终在 Key Vault 中，这使得 DEK 本身能得到加密和控制。 具有 KEK 访问权限的实体可能不是需要 DEK 的实体。 由于解密 DEK 需要 KEK，因此 KEK 实际上构成了一个单点机制：删除 KEK 即可删除 DEK。

DEK 使用 KEK 加密且单独存储。 只有有权访问 KEK 的实体才能解密这些 DEK。 有关详细信息，请参阅[静态加密中的安全性](../security/fundamentals/encryption-atrest.md)。

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>使用客户管理的密钥进行数据加密的工作原理

![显示“创建自己的密钥”概述的关系图](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

若要使 PostgreSQL 服务器使用存储在 Key Vault 中的客户管理的密钥对 DEK 进行加密，Key Vault 管理员需向服务器授予以下访问权限：

* **get**：用于检索 Key Vault 中密钥的公共部分和属性。
* **wrapKey**：可加密 DEK。
* **unwrapKey**：可解密 DEK。

Key Vault 管理员还可[启用 Key Vault 审核事件的日志记录](../azure-monitor/insights/azure-key-vault.md)，便于稍后对其进行审核。

当服务器配置为使用存储在 Key Vault 中的客户管理的密钥时，该服务器会将 DEK 发送到 Key Vault 进行加密。 Key Vault 返回存储在用户数据库中已加密的 DEK。 同样在必要时，服务器会将受保护的 DEK 发送到 Key Vault 进行解密。 如果启用了日志记录，审计可使用 Azure Monitor 查看 Key Vault 审核事件日志。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>为 Azure Database for PostgreSQL 单一服务器配置数据加密的要求

下面是 Key Vault 的配置要求：

* Key Vault 和 Azure Database for PostgreSQL 单一服务器必须属于同一个 Azure Active Directory (Azure AD) 租户。 不支持跨租户的 Key Vault 和服务器交互。 之后若要移动资源，需要重新配置数据加密。
* 启用 Key Vault 上的软删除功能，防止在意外删除密钥（或 Key Vault）时丢失数据。 被软删除的资源将保留 90 天，除非用户在此期间恢复或清除它们。 “恢复”和“清除”操作均自带与 Key Vault 访问策略关联的权限。 软删除功能默认关闭，但你可通过 PowerShell 或 Azure CLI 启用它（请注意，无法通过 Azure 门户启用）。
* 通过唯一托管标识，使用 get、wrapKey 和 unwrapKey 权限授权 Azure Database for PostgreSQL 单一服务器访问 Key Vault。 在 Azure 门户中，当 PostgreSQL 单一服务器上启用数据加密时，将自动创建唯一标识。 有关使用 Azure 门户时的详细分步说明，请参阅[通过 Azure 门户对 Azure Database for PostgreSQL 单一服务器进行数据加密](howto-data-encryption-portal.md)。

下面是客户管理的密钥的配置要求：

* 用于加密 DEK 的客户管理的密钥只能是非对称的 RSA 2048。
* 密钥激活日期（如果已设置）必须是过去的日期和时间。 到期日期（若已设置）必须是将来的日期和时间。
* 密钥必须处于“已启用”状态。
* 若要将现有密钥导入 Key Vault，请确保以受支持的文件格式（`.pfx`、`.byok`、`.backup`）提供该密钥。

## <a name="recommendations"></a>建议

通过客户管理的密钥使用数据加密时，请查看下列 Key Vault 配置建议：

* 在 Key Vault 中设置资源锁可控制谁能删除该关键资源，并防止意外或未经授权的删除。
* 对所有加密密钥启用审核和报告功能。 Key Vault 提供可轻松注入到其他安全信息和事件管理工具的日志。 Azure Monitor Log Analytics 就是一项已集成的服务。
* 确保 Key Vault 和 Azure Database for PostgreSQL 单一服务器位于同一区域，从而保证能更快地访问 DEK 的“包装”和“取消包装”操作。
* 锁定 Azure KeyVault，使其只能用于专用终结点和所选网络，且仅允许使用受信任的 Microsoft 服务来保护资源。

    ![trusted-service-with-AKV](media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png)

下面是客户管理的密钥的配置建议：

* 将客户管理的密钥副本保存在安全的位置，或将其托管到托管服务。

* 如果 Key Vault 生成密钥，请在首次使用该密钥之前创建密钥备份。 只能将备份还原到 Key Vault。 要详细了解备份命令，请参阅 [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)。

## <a name="inaccessible-customer-managed-key-condition"></a>无法访问客户管理的密钥的情形

在 Key Vault 中使用客户管理的密钥配置数据加密时，服务器必须保持联机状态才能持续访问该密钥。 如果服务器无法再访问 Key Vault 中客户管理的密钥，它将在 10 分钟内开始拒绝所有连接。 服务器会发出相应的错误消息，并将服务器状态更改为“无法访问”。 使服务器达到此状态的部分原因如下：

* 如果为 Azure Database for PostgreSQL 单一服务器创建“时间点还原”服务器，而前者启用了数据加密，则新创建的服务器将处于“无法访问”状态。 可通过 [Azure 门户](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers)或 [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) 修复服务器状态。
* 如果为 Azure Database for PostgreSQL 单一服务器创建只读副本，而该服务器启用了数据加密，则副本服务器将处于“无法访问”状态。 可通过 [Azure 门户](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers)或 [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) 修复服务器状态。
* 如果删除 KeyVault，Azure Database for PostgreSQL 单一服务器将无法访问密钥，并将转为“无法访问”状态。 请恢复 [Key Vault](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects) 并重新验证数据加密，使服务器的状态变为“可用”。
* 如果从 KeyVault 中删除密钥，Azure Database for PostgreSQL 单一服务器将无法访问密钥，并将转为“无法访问”状态。 请恢复[密钥](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects)并重新验证数据加密，使服务器的状态变为“可用”。
* 如果 Azure KeyVault 中存储的密钥过期，则该密钥将失效，且 Azure Database for PostgreSQL 单一服务器将变为“无法访问”状态。 请使用 [CLI](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-set-attributes) 将密钥到期日期延后，然后重新验证数据加密，使服务器的状态变为“可用”。

### <a name="accidental-key-access-revocation-from-key-vault"></a>从 Key Vault 意外撤消密钥访问

可能会发生这样的情况：对 Key Vault 具有足够访问权限的人员通过下列方式意外禁用了服务器对密钥的访问：

* 从服务器中撤消 Key Vault 的 get、wrapKey 和 unwrapKey 权限。
* 删除密钥。
* 删除 Key Vault。
* 更改 Key Vault 的防火墙规则。

* 删除 Azure AD 中服务器的托管标识。

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>在 Key Vault 中监视客户管理的密钥

若要监视数据库状态并在透明数据加密保护程序访问权限丢失时发出警报，请配置以下 Azure 功能：

* [Azure 资源运行状况](../service-health/resource-health-overview.md)：在与数据库的第一次连接遭到拒绝后，已失去客户密钥访问权限的无法访问的数据库将显示为“无法访问”。
* [活动日志](../service-health/alerts-activity-log-service-notifications.md)：对 Key Vault 中客户管理的密钥访问失败时，活动日志中会添加相应条目。 如果为这些事件创建警报，就可尽快恢复访问。

* [操作组](../azure-monitor/platform/action-groups.md)：定义这些组，使其根据首选项向你发送通知和警报。

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>在 Key Vault 中使用客户管理的密钥进行还原和复制

在使用 Key Vault 中存储的客户管理的密钥对 Azure Database for PostgreSQL 单一服务器进行加密后，还将所有新创建的服务器副本进行加密。 可通过本地或异地还原操作，或通过只读副本创建这个新副本。 可更改该副本，使其反映出用于加密的客户管理的新密钥。 当客户管理的密钥更改时，服务器的旧备份将开始使用最新的密钥。

为避免在还原或只读副本创建期间设置客户管理的数据加密时出现问题，有必要在主服务器和还原/副本服务器上执行以下步骤：

* 通过主要 Azure Database for PostgreSQL 单一服务器启动还原或只读副本创建过程。
* 使新创建的（还原/副本）服务器保持在无法访问的状态，因为其唯一标识尚无权访问 Key Vault。
* 在还原/副本服务器上，重新验证数据加密设置中客户管理的密钥。 这可确保为新创建的服务器授予对 Key Vault 中存储的密钥进行包装和取消包装的权限。

## <a name="limitations"></a>限制

对于 Azure Database for PostgreSQL，使用客户托管的密钥（CMK）对静态数据加密的支持有几个限制：

* 对此功能的支持仅限于**常规用途**和**内存优化**定价层。
* 此功能仅在支持高达 16 TB 的存储的区域和服务器上受支持。 有关支持存储最多16TB 的 Azure 区域列表，请参阅[此处](concepts-pricing-tiers.md#storage)文档中的 "存储" 部分

    > [!NOTE]
    > - 在上面列出的区域中创建的所有新 PostgreSQL 服务器都**提供**对使用客户管理器密钥的加密支持。 虽然在理论上的时间点还原（PITR）服务器或读取副本不合格，但它们是 "新的"。
    > - 若要验证预配的服务器是否支持最大16TB，可以在门户中访问 "定价层" 边栏选项卡，并查看预配服务器支持的最大存储大小。 如果可以将滑块向上移动到4TB，则服务器可能不支持通过客户托管的密钥进行加密。 但是，始终使用服务托管密钥对数据进行加密。 AskAzureDBforPostgreSQL@service.microsoft.com如果你有任何疑问，请联系。

* 只有 RSA 2048 加密密钥支持加密。

## <a name="next-steps"></a>后续步骤

了解如何[通过 Azure 门户设置使用 Azure Database for PostgreSQL 单一服务器的客户管理的密钥进行数据加密的操作](howto-data-encryption-portal.md)。

