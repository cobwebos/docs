---
title: 使用客户管理密钥进行数据加密 - 用于 PostgreSQL 的 Azure 数据库 - 单个服务器
description: 使用客户管理的密钥进行 PostgreSQL 单一服务器数据加密的 Azure 数据库使您能够自带密钥 （BYOK） 以进行静态数据保护。 它还允许组织在密钥和数据管理方面实现职责分离。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 20e01e681c382e3c9c69f76c95a90f709f409d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297016"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>使用客户管理的密钥进行 PostgreSQL 单一服务器数据加密的 Azure 数据库

> [!NOTE]
> 目前，必须请求访问权限才能使用此功能。 为此，请联系AskAzureDBforPostgreSQL@service.microsoft.com。

使用用于 PostgreSQL 单个服务器的 Azure 数据库的客户管理密钥进行数据加密，使您能够自带密钥 （BYOK） 以进行静态数据保护。 它还允许组织在密钥和数据管理方面实现职责分离。 使用客户管理的加密，您可以负责并完全控制密钥的生命周期、密钥使用权限和对密钥的操作的审核。

在服务器级别设置具有用于 PostgreSQL 单一服务器的 Azure 数据库的客户管理密钥的数据加密。 对于给定服务器，客户管理的密钥（称为密钥加密密钥 （KEK））用于加密服务使用的数据加密密钥 （DEK）。 KEK 是存储在客户拥有和客户管理的[Azure 密钥保管库](../key-vault/key-Vault-secure-your-key-Vault.md)实例中的非对称密钥。 密钥加密密钥 （KEK） 和数据加密密钥 （DEK） 在本文后面进行了更详细的介绍。

密钥保管库是一个基于云的外部密钥管理系统。 它高度可用，为 RSA 加密密钥提供可扩展、安全的存储，由 FIPS 140-2 级验证的硬件安全模块 （HSM） 提供可选的支持。 它不允许直接访问存储的密钥，但确实向授权实体提供加密和解密服务。 密钥保管库可以生成密钥、导入密钥或[将其从本地 HSM 设备传输](../key-vault/key-Vault-hsm-protected-keys.md)。

> [!NOTE]
> 此功能在所有 Azure 区域都可用，其中用于 PostgreSQL 单个服务器的 Azure 数据库都支持"通用"和"内存优化"定价层。

## <a name="benefits"></a>优点

后格雷SQL单一服务器 Azure 数据库的数据加密提供了以下优点：

* 数据访问完全由您通过删除密钥和使数据库无法访问的能力控制 
*    完全控制关键生命周期，包括旋转密钥以符合公司策略
*    Azure 密钥保管库中密钥的集中管理和组织
*    能够在安全官员、DBA 和系统管理员之间实现职责分离

## <a name="terminology-and-description"></a>术语和说明

**数据加密密钥 （DEK）**：用于加密数据分区或数据块的对称 AES256 密钥。 使用不同的密钥加密每个数据块可以增加加密分析攻击的难度。 资源提供程序或应用程序实例需要 DEK 访问权限才能加密和解密特定的块。 使用新密钥替换 DEK 时，只能使用新密钥重新加密其关联块中的数据。

**密钥加密密钥 （KEK）：** 用于加密 SDK 的加密密钥。 从不离开密钥保管库的 KEK 允许对 SDK 本身进行加密和控制。 有权访问 KEK 的实体可能与需要 DEK 的实体不同。 由于解密 DEK 需要 KEK，因此 KEK 实际上构成了一个单点机制：删除 KEK 即可删除 DEK。

与 KEK 加密的 SDK 单独存储。 只有有权访问 KEK 的实体才能解密这些 SDK。 有关详细信息，请参阅[静态加密的安全性](../security/fundamentals/encryption-atrest.md)。

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>使用客户管理密钥进行数据加密的工作原理

![显示自带密钥概述的图表](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

对于 PostgreSQL 服务器使用存储在密钥保管库中的客户托管密钥进行 DEK 加密，密钥保管库管理员向服务器授予以下访问权限：

* **获取**： 用于检索密钥保管库中密钥的公共部分和属性。
* **包装键**：能够加密DEK。
* **解包键**：能够解密DEK。

密钥保管库管理员还可以[启用密钥保管库审核事件的日志记录](../azure-monitor/insights/azure-key-vault.md)，以便以后可以审核这些事件。

当服务器配置为使用存储在密钥保管库中的客户托管密钥时，服务器会将 DEK 发送到密钥保管库进行加密。 密钥保管库返回存储在用户数据库中的加密 DEK。 同样，在需要时，服务器将受保护的 DEK 发送到密钥保管库进行解密。 如果启用了日志记录，审核员可以使用 Azure 监视器查看密钥保管库审核事件日志。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>为 PostgreSQL 单服务器配置 Azure 数据库的数据加密要求

以下是配置密钥保管库的要求：

* PostgreSQL 单个服务器的密钥保管库和 Azure 数据库必须属于同一 Azure 活动目录 （Azure AD） 租户。 不支持跨租户密钥保管库和服务器交互。 之后移动资源需要重新配置数据加密。
* 您必须在密钥保管库上启用软删除功能，以便在意外删除密钥（或密钥保管库）时防止数据丢失。 软删除的资源将保留 90 天，除非用户在此期间恢复或清除它们。 恢复和清除操作具有它们自己的权限，在密钥保管库访问策略中关联。 软删除功能默认处于关闭状态，但您可以通过 PowerShell 或 Azure CLI 将其启用（请注意，无法通过 Azure 门户启用它）。
* 使用获取、包装密钥和取消包装密钥权限，授予 PostgreSQL 单个服务器对密钥保管库的 Azure 数据库访问权限，并使用其唯一的托管标识。 在 Azure 门户中，在 PostgreSQL 单台服务器上启用数据加密时，将自动创建唯一标识。 请参阅使用 Azure 门户时使用 Azure 门户获得详细、分步说明[的 PostgreSQL 单一服务器的 Azure 数据库数据加密](howto-data-encryption-portal.md)。

* 当您使用带有密钥保管库的防火墙时，必须启用 **"允许受信任的 Microsoft 服务绕过防火墙**"选项。

以下是配置客户管理密钥的要求：

* 用于加密 DEK 的客户管理密钥只能是不对称的 RSA 2028。
* 密钥激活日期（如果已设置）必须是过去的日期和时间。 到期日期（如果设置）必须是将来的日期和时间。
* 密钥必须处于“已启用”状态。**
* 如果要将现有密钥导入密钥保管库，请确保以受支持的文件格式 （、`.pfx` `.byok` `.backup`. ） 提供它。

## <a name="recommendations"></a>建议

使用客户管理的密钥使用数据加密时，以下是配置密钥保管库的建议：

* 在密钥保管库上设置资源锁，以控制谁可以删除此关键资源并防止意外或未经授权的删除。
* 启用对所有加密密钥的审核和报告。 密钥保管库提供易于注入其他安全信息和事件管理工具的日志。 Azure 监视器日志分析是已集成的服务的一个示例。

* 确保 PostgreSQL 单个服务器的密钥保管库和 Azure 数据库驻留在同一区域中，以确保 DEK 换行和解包操作的访问速度更快。

以下是配置客户管理密钥的建议：

* 将客户管理的密钥副本保存在安全的地方，或将其托管到托管服务。

* 如果密钥保管库生成密钥，则在首次使用密钥之前创建密钥备份。 您只能将备份还原到密钥保管库。 有关备份命令的详细信息，请参阅[备份 -AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)。

## <a name="inaccessible-customer-managed-key-condition"></a>无法访问的客户管理的关键条件

使用密钥保管库中的客户管理密钥配置数据加密时，服务器需要持续访问此密钥才能保持联机状态。 如果服务器无法访问密钥保管库中的客户管理密钥，服务器将在 10 分钟内开始拒绝所有连接。 服务器发出相应的错误消息，并将服务器状态更改为*不可访问*。 在此状态下数据库上允许的唯一操作是删除它。

### <a name="accidental-key-access-revocation-from-key-vault"></a>密钥保管库的意外密钥访问吊销

对密钥保管库具有足够访问权限的人员可能会通过以下情况意外禁用服务器对密钥的访问：

* 从服务器撤消密钥保管库的获取、包装密钥和取消包密钥权限。
* 删除密钥。
* 删除密钥保管库。
* 更改密钥保管库的防火墙规则。

* 删除 Azure AD 中服务器的托管标识。

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>监视密钥保管库中的客户管理密钥

要监视数据库状态，并启用对丢失透明数据加密保护程序访问的警报，请配置以下 Azure 功能：

* [Azure 资源运行状况](../service-health/resource-health-overview.md)：在拒绝第一个与数据库的连接后，无法访问的数据库已无法访问客户密钥，显示为"无法访问"。
* [活动日志](../service-health/alerts-activity-log-service-notifications.md)：当客户托管的密钥保管库中的客户密钥访问失败时，条目将添加到活动日志中。 如果为这些事件创建警报，则可以尽快恢复访问。

* [操作组](../azure-monitor/platform/action-groups.md)：定义这些组，以便根据您的首选项向您发送通知和警报。

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>使用密钥保管库中的客户托管密钥还原和复制

在使用存储在密钥保管库中的客户托管密钥对 PostgreSQL 单个服务器的 Azure 数据库进行加密后，对新创建的服务器副本也进行加密。 您可以通过本地或异地还原操作或通过读取副本制作此新副本。 但是，可以更改副本以反映新客户的托管密钥进行加密。 更改客户管理的密钥时，服务器的旧备份将开始使用最新密钥。

为了避免在还原或读取副本创建期间设置客户管理的数据加密时出现问题，请务必在主服务器和还原/副本服务器上执行以下步骤：

* 从 PostgreSQL 单个服务器的主 Azure 数据库启动还原或读取副本创建过程。
* 将新创建的服务器（还原/复制副本）保持在不可访问状态，因为它的唯一标识尚未被授予密钥保管库的权限。
* 在还原/复制服务器上，在数据加密设置中重新验证客户管理的密钥。 这可确保为新创建的服务器授予密钥保管库中存储的密钥的换行和解包权限。

## <a name="next-steps"></a>后续步骤

了解如何使用[Azure 门户为 PostgreSQL 单一服务器的 Azure 数据库设置数据加密](howto-data-encryption-portal.md)。

