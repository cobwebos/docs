---
title: 通过客户托管的密钥 Azure Database for PostgreSQL 单服务器数据加密
description: 通过客户托管的密钥 Azure Database for PostgreSQL 单服务器数据加密使你能够创建自己的密钥（BYOK）以进行静态数据保护，并使组织能够在密钥和数据的管理中实现职责分离。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: f9e60b2f1685e03a9daa7a4801f43799a21eb411
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940545"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-customer-managed-key"></a>通过客户托管的密钥 Azure Database for PostgreSQL 单服务器数据加密

> [!NOTE]
> 此时，你必须请求访问权限才能使用此功能。 为此，请联系 AskAzureDBforPostgreSQL@service.microsoft.com。

通过客户托管的密钥 Azure Database for PostgreSQL 单服务器数据加密使你能够创建自己的密钥（BYOK）以进行静态数据保护，并使组织能够在密钥和数据的管理中实现职责分离。 通过客户托管的加密，你负责和完全控制密钥的生命周期（创建密钥、上传、旋转、删除）、密钥使用权限，以及对密钥的操作进行审核。

对于 Azure Database for PostgreSQL 单一服务器，数据加密在服务器级别设置。 对于这种形式的数据加密，密钥用于加密数据库加密密钥（DEK），这是一个基于云的外部密钥管理系统，它是一个客户托管的非对称密钥，存储在客户拥有的和客户托管的[Azure Key Vault （AKV）](../key-vault/key-Vault-secure-your-key-Vault.md)中。 AKV 高度可用，并为 RSA 加密密钥提供可缩放的安全存储，可根据 FIPS 140-2 第2级验证的硬件安全模块（Hsm）提供支持。 它不允许直接访问存储的密钥，但使用授权实体的密钥提供加密/解密服务。 可以通过本地 HSM 设备 Key Vault、导入或[传输到 Key Vault 来](../key-vault/key-Vault-hsm-protected-keys.md)生成密钥。

> [!NOTE]
> 此功能在所有 Azure Database for PostgreSQL 单一服务器支持常规用途和内存优化定价层的 Azure 区域中均可用。

## <a name="benefits"></a>优势

Azure Database for PostgreSQL 单一服务器的数据加密具有以下优势：

* 增加了对加密密钥的透明度和精细控制和管理 
* 通过在 Azure Key Vault 中托管密钥来集中管理和组织。 
* 能够实现组织中密钥和数据的管理中的职责分离
* 将密钥管理与组织内的数据管理分开，因此 Key Vault 管理员可以撤消密钥访问权限，以使加密数据库不可访问 
* 与最终客户更好地信任，因为 Azure Key Vault 的设计使 Microsoft 无法看到或提取加密密钥

## <a name="terminology-and-description"></a>术语和说明

数据加密密钥 (DEK) – 对称 AES256 密钥，用于加密数据分区或块。 使用不同的密钥加密每个数据块可以增加加密分析攻击的难度。 资源提供程序或应用程序实例需要 DEK 访问权限才能加密和解密特定的块。 将 DEK 替换为新密钥时，必须使用新密钥重新加密其关联块中的数据。

**密钥加密密钥（KEK）** -用于对数据加密密钥进行加密的加密密钥。 使用绝不会离开 Key Vault 的密钥加密密钥，允许加密和控制数据加密密钥。 具有 KEK 访问权限的实体可能不同于需要 DEK 的实体。 由于解密 DEK 需要 KEK，因此 KEK 实际上构成了一个单点机制：删除 KEK 即可删除 DEK。

使用密钥加密密钥加密的数据加密密钥单独进行存储，只有有权访问密钥加密密钥的实体才能解密这些数据加密密钥。 有关详细信息，请参阅[静态加密中的安全性](../security/fundamentals/encryption-atrest.md)。

## <a name="how-data-encryption-with-customer-managed-key-works"></a>使用客户管理的密钥进行数据加密的方式

![自带密钥概述](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

要使 PostgreSQL 服务器能够使用存储在 AKV 中的客户托管密钥来加密 DEK，Key Vault 管理员需要使用其唯一标识向服务器授予以下访问权限：

* **获取**-用于检索 Key Vault 中的密钥的公共部分和属性
* **wrapKey** -能够保护（加密） DEK
* **unwrapKey** -能够取消对 DEK 的保护（解密）

Key Vault 管理员还可以对[Key Vault 审核事件启用日志记录](../azure-monitor/insights/azure-key-vault.md)，以便以后可以对其进行审核。

如果将服务器配置为使用存储在 Key Vault 中的客户托管密钥，则服务器会将 DEK 发送到加密的 Key Vault。 Key Vault 返回存储在用户数据库中的加密 DEK。 同样，如果需要，服务器会将受保护的 DEK 发送到 Key Vault 进行解密。 如果启用了日志记录，审核员可以使用 Azure Monitor 查看 Key Vault AuditEvent 日志。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>为 Azure Database for PostgreSQL 单个服务器配置数据加密的要求

### <a name="requirements-for-configuring-akv"></a>配置 AKV 的要求

* Key Vault 和 Azure Database for PostgreSQL 单一服务器必须属于同一 Azure Active Directory （AAD）租户。 不支持跨租户 Key Vault 和服务器交互。 以后移动资源要求您重新配置数据加密。 了解有关移动资源的详细信息。
* 必须在 Key Vault 上启用软删除功能，以免发生意外的意外键（或 Key Vault）删除。 软删除的资源将保留90天，除非客户在此期间恢复或清除。 "恢复" 和 "清除" 操作在 Key Vault 访问策略中具有相关联的权限。 软删除功能在默认情况下处于关闭状态，可通过 Powershell 或 CLI 启用。 不能通过 Azure 门户启用此功能。
* 使用**get、wrapKey、unwrapKey**权限，通过其唯一的托管标识授予对 Key Vault 的 Azure Database for PostgreSQL 单一服务器访问权限。 使用 Azure 门户时，在 PostgreSQL 单服务器上启用数据加密时，将自动创建唯一标识。 有关使用 Azure 门户的详细分步说明，请参阅[配置 PostgreSQL 单服务器的数据加密](howto-data-encryption-portal.md)。

* 使用带有 AKV 的防火墙时，必须启用 "*允许受信任的 Microsoft 服务" 选项来绕过防火墙*。

### <a name="requirements-for-configuring-customer-managed-key"></a>配置客户托管密钥的要求

* 用于加密 DEK 的客户托管密钥只能是非对称的，RSA 2028。
* 密钥激活日期（如果已设置）必须是过去的日期和时间。 到期日期（如果已设置）必须是将来的日期和时间。
* 密钥必须处于 "*已启用*" 状态。
* 如果要将现有密钥导入到 Key Vault 中，请确保以支持的文件格式（`.pfx`、`.byok``.backup`）提供该密钥。

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>使用客户托管密钥的数据加密的建议

### <a name="recommendation-for-configuring-akv"></a>配置 AKV 的建议

* 设置 Key Vault 的资源锁，以控制谁可以删除此重要资源并防止意外或未经授权的删除。 详细了解资源锁。
* 对所有加密密钥启用审核和报告： Key Vault 提供可轻松注入到其他安全信息和事件管理工具的日志。 Azure Monitor Log Analytics 是已集成的服务的一个示例。

* 确保 Key Vault 和 Azure Database for PostgreSQL 单一服务器位于同一区域，以确保更快地访问 DEK wrap/解包操作。

### <a name="recommendation-for-configuring-customer-managed-key"></a>配置客户托管密钥的建议

* 将客户托管的密钥（KEK）的副本保存在安全的位置，或将其托管到托管服务。

* 如果在 Key Vault 中生成密钥，请在首次使用 AKV 中的密钥之前创建密钥备份。 只能将备份还原到 Azure Key Vault。 了解有关[AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)命令的详细信息。

## <a name="inaccessible-customer-managed-key-condition"></a>无法访问客户托管的密钥条件

如果在 Azure Key Vault 中使用客户托管的密钥配置数据加密（AKV），则需要对此密钥进行持续访问，使服务器保持联机。 如果服务器在10分钟内失去了对 AKV 中客户托管密钥的访问权限，则服务器将开始拒绝所有连接与相应的错误消息，并将服务器状态更改为 "**不可访问**"。 不能访问的数据库中允许的唯一操作是将其删除。

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>Azure Key Vault （AKV）的意外密钥访问吊销

可能会出现以下情况：具有对 Key Vault 的足够访问权限的某人意外禁用了对密钥的服务器访问权限：

* 从服务器撤消 Key Vault 的 get、wrapKey、unwrapKey 权限
* 删除密钥
* 删除 Key Vault
* 更改 Key Vault 的防火墙规则

* 正在删除 Azure Active Directory 中服务器的托管标识

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>监视 Key Vault 中客户托管的密钥

若要监视数据库状态并为丢失 TDE 保护程序访问启用警报，请配置以下 Azure 功能：

* [Azure 资源运行状况](../service-health/resource-health-overview.md)-在第一次连接到数据库后，无法访问的数据库将显示为 "无法访问"。
* [活动日志](../service-health/alerts-activity-log-service-notifications.md)-当访问客户管理的 Key Vault 中的客户密钥失败时，会向活动日志添加条目。 通过为这些事件创建警报，你可以尽快恢复访问权限。

* [操作组](../azure-monitor/platform/action-groups.md)可以定义为根据你的偏好发送通知和警报，例如电子邮件/短信/推送/语音、逻辑应用、WEBHOOK、ITSM 或自动化 Runbook。

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>Key Vault 中的客户托管密钥的还原和副本

使用存储在 Key Vault 中的客户托管密钥对 Azure Database for PostgreSQL 单一服务器进行加密后，任何新创建的服务器副本（尽管是本地或异地还原操作或通过读取副本）都将使用相同的客户的托管密钥。 但是，可以对其进行更改，以反映新客户的加密管理密钥。 当客户管理的密钥更改时，服务器的旧备份将开始使用最新的密钥。

若要避免在创建还原或读取副本过程中设置客户管理的数据加密时出现问题，请务必在 master 和 restore/replica 服务器上执行以下步骤：

* 从主 Azure Database for PostgreSQL 单一服务器启动还原或读取副本创建过程。
* 新创建的服务器（还原/复制）将保持为不可访问状态，因为尚未向其授予对 Azure Key Vault 的权限（AKV）
* 在还原/副本服务器上，重新验证数据加密设置中客户托管的密钥，以确保为新创建的服务器提供了对存储在 AKV 中的密钥的换行/解包权限。

* 必须执行上述两个步骤，以确保主服务器和还原/副本服务器上的数据加密得以保留。

## <a name="next-steps"></a>后续步骤

了解如何使用 Azure 门户为适用于[Azure database For PostgreSQL 单一服务器的客户托管密钥设置数据加密](howto-data-encryption-portal.md)。
