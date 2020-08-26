---
title: 使用客户托管密钥进行数据加密 - Azure Database for MySQL
description: 使用客户托管密钥进行 Azure Database for MySQL 数据加密，可创建自己的密钥 (BYOK) 来保护静态数据。 它还允许组织在管理密钥和数据时实现职责分离。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 8fca0195c2941e4ed1a859c3201adfc2a4a0a2ed
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067437"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>使用客户托管密钥进行 Azure Database for MySQL 数据加密

针对 Azure Database for MySQL 使用客户托管密钥进行数据加密，可创建自己的密钥 (BYOK) 来保护静态数据。 它还允许组织在管理密钥和数据时实现职责分离。 通过客户托管的加密，密钥的生命周期、密钥使用权限以及对密钥操作的审核都由你负责和完全控制。

在服务器级别使用客户托管密钥为 Azure Database for MySQL 进行数据加密。 对于给定的服务器，客户托管密钥（称为密钥加密密钥 (KEK)）用于加密服务使用的数据加密密钥 (DEK)。 KEK 是一种非对称密钥，它存储在客户自有和客户管理的 [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) 实例中。 本文稍后将更详细地描述密钥加密密钥 (KEK) 和数据加密密钥 (DEK)。

Key Vault 是一种基于云的外部密钥管理系统。 它具有高可用性，并为 RSA 加密密钥提供可扩展的安全存储，根据需要由 FIPS 140-2 级别 2 验证的硬件安全模块 (HSM) 提供支持。 它不允许直接访问存储的密钥，但为授权实体提供加密和解密服务。 Key Vault 可以生成密钥，并将其导入，或者[从本地 HSM 设备传输密钥](../key-vault/key-Vault-hsm-protected-keys.md)。

> [!NOTE]
> 此功能适用于所有 Azure 区域，其中 Azure Database for MySQL 支持“常规用途”和“内存优化”定价层。 有关其他限制，请参阅[限制](concepts-data-encryption-mysql.md#limitations)部分。

## <a name="benefits"></a>优点

用于 Azure Database for MySQL 的客户托管密钥的数据加密具有以下优势：

* 数据访问完全由你控制，你可删除密钥并使数据库无法访问 
* 可完全控制密钥生命周期，包括根据公司策略轮替密钥
* 在 Azure Key Vault 中集中管理和整理密钥
* 可实现安全专员与 DBA 和系统管理员之间的职责分离


## <a name="terminology-and-description"></a>术语和说明

**数据加密密钥 (DEK)** ：对称 AES256 密钥，用于加密数据分区或数据块。 使用不同的密钥加密每个数据块可以增加加密分析攻击的难度。 资源提供程序或应用程序实例需要 DEK 访问权限才能加密和解密特定的块。 将 DEK 替换为新密钥时，只需使用新密钥对其关联的块中的数据重新加密。

**密钥加密密钥 (KEK)** ：用于加密 DEK 的加密密钥。 KEK 始终在 Key Vault 中，这使得 DEK 本身能得到加密和控制。 具有 KEK 访问权限的实体可能不是需要 DEK 的实体。 由于解密 DEK 需要 KEK，因此 KEK 实际上构成了一个单点机制：删除 KEK 即可删除 DEK。

DEK 使用 KEK 加密且单独存储。 只有有权访问 KEK 的实体才能解密这些 DEK。 有关详细信息，请参阅[静态加密中的安全性](../security/fundamentals/encryption-atrest.md)。

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>使用客户管理的密钥进行数据加密的工作原理

![显示“创建自己的密钥”概述的关系图](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

若要使 MySQL 服务器使用存储在 Key Vault 中的客户托管密钥对 DEK 进行加密，Key Vault 管理员将授予服务器以下访问权限：

* **get**：用于检索密钥保管库中密钥的公共部分和属性。
* **wrapKey**：可加密 DEK。 加密的 DEK 存储在 Azure Database for MySQL 中。
* **unwrapKey**：能够解密 DEK。 Azure Database for MySQL 需要解密的 DEK 对数据进行加密/解密

Key Vault 管理员还可[启用 Key Vault 审核事件的日志记录](../azure-monitor/insights/key-vault-insights-overview.md)，便于稍后对其进行审核。

当服务器配置为使用存储在 Key Vault 中的客户管理的密钥时，该服务器会将 DEK 发送到 Key Vault 进行加密。 Key Vault 返回存储在用户数据库中已加密的 DEK。 同样在必要时，服务器会将受保护的 DEK 发送到 Key Vault 进行解密。 如果启用了日志记录，则审核员可以使用 Azure Monitor 查看 Key Vault 审核事件日志。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 配置数据加密的要求

下面是配置 Key Vault 的要求：

* Key Vault 和 Azure Database for MySQL 必须属于同一个 Azure Active Directory (Azure AD) 租户。 不支持跨租户的 Key Vault 和服务器交互。 之后移动 Key Vault 资源需要重新配置数据加密。
* 启用 Key Vault 上的软删除功能，防止在意外删除密钥（或 Key Vault）时丢失数据。 被软删除的资源将保留 90 天，除非用户在此期间恢复或清除它们。 “恢复”和“清除”操作均自带与 Key Vault 访问策略关联的权限。 默认情况下，软删除功能处于关闭状态，但你可以通过 PowerShell 或 Azure CLI 启用它（请注意，无法通过 Azure 门户启用此功能）。
* 通过使用其唯一的托管标识授予具有 get、wrapKey 和 unwrapKey 权限的密钥保管库的 Azure Database for MySQL 访问权限。 在 Azure 门户中，当在 MySQL 上启用数据加密时，将自动创建唯一的 "服务" 标识。 有关使用 Azure 门户时的详细分步说明，请参阅[为 MySQL 配置数据加密](howto-data-encryption-portal.md)。

下面是配置客户托管密钥的要求：

* 用于加密 DEK 的客户管理的密钥只能是非对称的 RSA 2048。
* 密钥激活日期（如果已设置）必须是过去的日期和时间。 到期日期（若已设置）必须是将来的日期和时间。
* 密钥必须处于“已启用”状态。
* 如果要将[现有密钥导入](https://docs.microsoft.com/rest/api/keyvault/ImportKey/ImportKey)到密钥保管库中，请确保以支持的文件格式提供该密钥 `.pfx` (`.byok` 、 `.backup`) 。

## <a name="recommendations"></a>建议

通过客户管理的密钥使用数据加密时，请查看下列 Key Vault 配置建议：

* 在 Key Vault 中设置资源锁可控制谁能删除该关键资源，并防止意外或未经授权的删除。
* 对所有加密密钥启用审核和报告功能。 Key Vault 提供可轻松注入到其他安全信息和事件管理工具的日志。 Azure Monitor Log Analytics 是已集成的服务的一个示例。
* 确保 Key Vault 和 Azure Database for MySQL 位于同一区域，以确保对 DEK 包装和解包操作的更快访问。
* 将 Azure KeyVault 锁定为仅“专用终结点和所选网络”，并仅允许“受信任的 Microsoft”服务保护资源。

    ![trusted-service-with-AKV](media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png)

下面是客户管理的密钥的配置建议：

* 将客户管理的密钥副本保存在安全的位置，或将其托管到托管服务。

* 如果 Key Vault 生成密钥，请在首次使用该密钥之前创建密钥备份。 只能将备份还原到 Key Vault。 要详细了解备份命令，请参阅 [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)。

## <a name="inaccessible-customer-managed-key-condition"></a>无法访问客户管理的密钥的情形

在 Key Vault 中使用客户管理的密钥配置数据加密时，服务器必须保持联机状态才能持续访问该密钥。 如果服务器无法再访问 Key Vault 中客户管理的密钥，它将在 10 分钟内开始拒绝所有连接。 服务器会发出相应的错误消息，并将服务器状态更改为“无法访问”。 服务器达到此状态的一些原因包括：

* 如果我们为 Azure Database for MySQL 创建已启用数据加密的时间点还原服务器，则新创建的服务器将处于“无法访问”状态。 可以通过 [Azure 门户](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers)或 [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) 修复此问题。
* 如果我们为 Azure Database for MySQL 创建已启用数据加密的只读副本，则副本服务器将处于“无法访问”状态。 可以通过 [Azure 门户](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers)或 [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) 修复此问题。
* 如果删除 KeyVault，Azure Database for MySQL 将无法访问密钥，并将转到“无法访问”状态。 恢复 [Key Vault](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects) 并重新验证数据加密，以使服务器“可用”。
* 如果我们从 KeyVault 中删除密钥，Azure Database for MySQL 将无法访问密钥，并将转到“无法访问”状态。 恢复[密钥](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects)并重新验证数据加密，以使服务器“可用”。
* 如果存储在 Azure KeyVault 中的密钥过期，则该密钥将变为无效，并且 Azure Database for MySQL 将转换为“无法访问”状态。 使用 [CLI](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-set-attributes) 延长密钥到期日期，然后重新验证数据加密以使服务器“可用”。

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

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>在 Key Vault 中使用客户托管密钥进行还原和复制

在使用客户存储在 Key Vault 中的托管密钥对 Azure Database for MySQL 进行加密后，还将对服务器的任何新创建的副本进行加密。 可以通过本地或异地还原操作，或通过只读副本创建此新副本。 可更改该副本，使其反映出用于加密的客户管理的新密钥。 当客户管理的密钥更改时，服务器的旧备份将开始使用最新的密钥。

若要避免在还原或只读副本创建期间设置客户托管的数据加密时出现问题，在主服务器和还原/副本服务器上执行以下步骤非常重要：

* 从主 Azure Database for MySQL 启动还原或只读副本创建过程。
* 使新创建的服务器（还原/副本）保持无法访问状态，因为其唯一标识尚未授予 Key Vault 的权限。
* 在还原/副本服务器上，重新验证数据加密设置中客户托管密钥，以确保为新创建的服务器提供对存储在 Key Vault 中的密钥的包装和解包权限。

## <a name="limitations"></a>限制

对于 Azure Database for MySQL，使用客户托管密钥 (CMK) 对静态数据进行加密支持有少数限制-

* 对此功能的支持仅限于**常规用途**和**内存优化**定价层。
* 此功能仅在支持高达 16 TB 的存储的区域和服务器上受支持。 有关支持存储最多16TB 的 Azure 区域列表，请参阅[此处](concepts-pricing-tiers.md#storage)文档中的 "存储" 部分

    > [!NOTE]
    > - 在上面列出的区域中创建的所有新 MySQL 服务器都**提供**对使用客户管理器密钥的加密支持。  (PITR) 服务器或读取副本的还原时间点在理论上是 "新的"。
    > - 若要验证预配的服务器是否支持最大16TB，可以在门户中访问 "定价层" 边栏选项卡，并查看预配服务器支持的最大存储大小。 如果可以将滑块向上移动到4TB，则服务器可能不支持通过客户托管的密钥进行加密。 但是，始终使用服务托管密钥对数据进行加密。 AskAzureDBforMySQL@service.microsoft.com如果你有任何疑问，请联系。

* 只有 RSA 2048 加密密钥支持加密。

## <a name="next-steps"></a>后续步骤

了解如何[通过 Azure 门户使用客户托管密钥为 Azure database for MySQL 设置数据加密](howto-data-encryption-portal.md)。
