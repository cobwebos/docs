---
title: Azure 澳大利亚 Azure Key Vault
description: 有关配置和 Azure Key Vault 使用澳大利亚地区中的密钥管理的指南, 以满足澳大利亚政府政策、法规和法规的特定要求。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 263765c777f994134befc52f0c63c7f18e590b39
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602128"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure 澳大利亚 Azure Key Vault

加密密钥的安全存储和加密密钥生命周期的管理是加密系统中的关键元素。  在 Azure 中提供此功能的服务是 Azure Key Vault。 Key Vault 已被 IRAP 安全访问, ACSC 经过保护。  本文概述了使用 Key Vault 符合澳大利亚国防局的 (.ASD)[信息安全手册控制](https://acsc.gov.au/infosec/ism/)(ISM) 时的主要注意事项。

Azure Key Vault 是一项云服务, 用于保护加密密钥和机密。 由于此数据是敏感的并且是业务关键的, Key Vault 启用对密钥保管库的安全访问, 仅允许已授权的用户和应用程序。 Key Vault 管理和控制三个主要项目:

- 密钥
- 机密
- 证书

本文重点介绍如何使用 Key Vault 管理密钥。

![Azure Key Vault](media/azure-key-vault-overview.png)

*图1– Azure Key Vault*

## <a name="key-design-considerations"></a>关键设计注意事项

### <a name="deployment-options"></a>部署选项

有两个选项可用于创建 Azure 密钥保管库。 这两个选项都使用 Thales nShield 系列的硬件安全模块 (HSM), 已验证了联邦信息处理标准 (FIPS), 并批准了在受保护的环境中存储密钥。 选项包括：

- **受软件保护的保管库:** 已验证 FIPS 140-2 级别1。 存储在 HSM 中的密钥。 在 Azure 上的计算资源中执行加密和解密操作。
- **HSM 保护的保管库:** 已验证 FIPS 140-2 级别2。 存储在 HSM 中的密钥。 加密和解密操作在 HSM 上执行。

Key Vault 支持 Rivest-Rivest-shamir-adleman-Rivest-shamir-adleman (RSA) 和椭圆曲线加密 (ECC) 密钥。 默认值为 RSA 2048 位密钥, 但有一个适用于 RSA 3072 位、RSA 4096 位和 ECC 密钥的高级选项。  所有键都符合 ISM 控件, 但首选椭圆曲线密钥。

### <a name="resource-operations"></a>资源操作

Azure Key Vault 涉及几个角色:

- **Key Vault 管理员:** 管理保管库的生命周期
- **关键管理员:** 管理保管库中密钥的生命周期
- **开发人员/操作员:** 将密钥从保管库集成到应用程序和服务
- **审核员**监视密钥用法和访问
- **应用程序：** 使用密钥来保护信息

Azure Key Vault 是通过两个单独的接口来保护的:

- **管理平面:** 此平面处理管理保管库, 由 RBAC 保护。
- **数据平面:** 此平面处理管理和访问保管库中的项目。  使用 Key Vault 访问策略保护。

根据 ISM 的要求, 在调用方 (用户或应用程序) 之前需要适当的身份验证和授权, 然后才能通过任一平面访问 key vault。

Azure RBAC 为 Key Vault、 [Key Vault 参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-contributor)提供了一个内置角色, 用于控制对密钥保管库的管理。 建议创建自定义角色, 该角色与更细化的角色一起用于管理密钥保管库。

>[!WARNING]
>通过 Key Vault 访问策略启用密钥访问权限后, 用户或应用程序就可以访问密钥保管库中的所有密钥 (例如, 如果用户具有 "删除" 访问权限, 则可以删除所有密钥)。  因此, 应该部署多个密钥保管库, 使其与安全域/边界一致。

### <a name="networking"></a>网络

你可以配置 Key Vault 防火墙和虚拟网络, 以控制对数据平面的访问。  你可以允许访问指定网络上的用户或应用程序, 同时拒绝对其他所有网络上的用户或应用程序的访问。 如果启用 "允许受信任的服务", 则[受信任的服务](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services)是此控制的例外情况。  虚拟网络控制不适用于管理平面。

应将对密钥保管库的访问权限显式限制为拥有用户或应用程序需要访问密钥的最小网络集。

### <a name="bring-your-own-key-byok"></a>创建自己的密钥 (BYOK)

Key Vault 支持 BYOK。  BYOK 使用户能够从其现有密钥基础结构导入密钥。  Thales 提供了一个[澳大利亚工具集](https://www.microsoft.com/download/details.aspx?id=45345), 用于支持从外部 HSM (例如, 使用脱机工作站生成的密钥) 将密钥安全传输和导入到 Key Vault。

### <a name="key-vault-auditing-and-logging"></a>Key Vault 审核和日志记录

ACSC 需要英联邦实体才能使用适当的 Azure 服务来对其 Azure 工作负荷进行实时监视和报告。

通过对键值启用 **_"AuditEvent"_** 诊断设置来启用日志记录。  审核事件将记录到指定的存储帐户中。  应根据数据保留策略设置 **_"RetentionInDays"_** 期间。  管理平面和数据平面上的[操作](https://docs.microsoft.com/azure/key-vault/key-vault-logging#interpret)均经过审核并记录。 [Azure Monitor 中的 Azure Key Vault 解决方案](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)可用于查看 Key Vault AuditEvent 日志。  可以使用许多其他 Azure 服务来处理和分发 Key Vault AuditEvents。

### <a name="key-rotation"></a>密钥轮换

将密钥存储在 Key Vault 提供了一个点来维护应用程序外部的密钥, 而不会影响应用程序的行为。 将密钥存储在 Azure Key Vault 会启用各种策略来支持密钥旋转:

- 手动
- 通过 Api 以编程方式
- 自动化脚本 (例如, 使用 PowerShell 和 Azure 自动化)

这些选项允许定期轮换密钥, 以满足符合性要求, 如果存在可能已泄露密钥的问题, 则应使用临时密钥。

#### <a name="key-rotation-strategies"></a>密钥轮换策略

为存储在 KeyVault 中的密钥制定适当的密钥轮换策略非常重要。  使用错误的密钥会导致信息被错误解密, 丢失密钥可能会导致信息完全丢失。  不同方案的密钥轮换策略示例包括:

- **即时数据:** 可变信息在两方之间传输。  旋转键后, 双方都必须有一种机制来同步从密钥保管库检索更新的密钥。
- **数据作为 rest:** 参与方将加密数据存储在以后使用时对其进行解密。  当某个密钥要旋转时, 必须使用旧密钥对数据进行解密, 然后使用新的轮替密钥对其进行加密。  可以使用密钥加密密钥来最大程度地降低解密/加密过程的影响 (请参阅示例)。  Microsoft 管理了与 Azure 存储的密钥轮换相关的大部分过程 (请参阅 ...)
- **访问密钥:** 许多 Azure 服务具有可存储在 Key Vault 中的访问密钥 (例如 CosmosDB)。  Azure 服务具有主要访问密钥和辅助访问密钥。  这一点很重要, 这两个密钥不能同时旋转。  因此, 应在一段时间后旋转一个键, 并验证密钥操作, 然后才能旋转第二个键。

### <a name="high-availability"></a>高可用性

ISM 具有几个与业务连续性相关的控件。
Azure Key Vault 具有多个冗余层, 其中的内容复制到了区域和辅助的[配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中。

如果 key vault 处于故障转移状态, 它处于只读模式, 并且将返回到读写模式, 主服务将还原。

ISM 具有几个与备份相关的控件。  为保管库和密钥开发和执行适当的备份/还原计划是非常重要的。

## <a name="key-lifecycle"></a>关键生命周期

### <a name="key-operations"></a>密钥操作

Key Vault 支持对密钥执行以下操作:

- **创建**允许客户端在 Key Vault 中创建密钥。 密钥的值由 Key Vault 生成，存储但不发布到客户端。 可在 Key Vault 中创建非对称密钥。
- **导入**允许客户端将现有密钥导入到 Key Vault。 非对称密钥可以使用 JWK 构造中的多种不同的打包方法导入到 Key Vault。
- **时更新**允许具有足够权限的客户端修改与以前存储在 Key Vault 中的密钥相关联的元数据（密钥属性）。
- **delete**允许具有足够权限的客户端删除 Key Vault 中的密钥。
- **成员列表**允许客户端列出给定 Key Vault 中的所有项。
- **列出版本:** 允许客户端列出给定 Key Vault 中的给定密钥的所有版本。
- **获取：** 允许客户端检索 Key Vault 中的给定密钥的公共部分。
- **backup**导出受保护窗体中的密钥。
- **restore**导入以前备份的密钥。

对于用户、服务主体或应用程序, 可以向用户、服务主体或应用程序授予相应的权限集, 使用 Key Vault 访问控制项来实现关键操作。

Key Vault 包含软删除功能, 可以恢复已删除的保管库和密钥。 默认情况下, **_"软删除"_** 未启用, 但一旦启用, 对象将保留90天 (保持期), 并显示为已删除。  如果禁用了 **_"清除保护"_** 选项, 则额外的权限 **_"清除"_** 允许永久删除密钥。

创建或导入现有密钥会创建密钥的新版本。

### <a name="cryptographic-operations"></a>加密操作

Key Vault 还支持使用密钥进行加密操作:

- **签名并验证:** 此操作为 "签名哈希" 或 "验证哈希"。 Key Vault 不支持将内容作为签名创建的一部分进行哈希处理。
- **密钥加密/包装:** 此操作用于保护另一个密钥。
- **加密和解密:** 存储的密钥用于加密或解密单个数据块

对于用户、服务主体或应用程序, 可以向用户、服务主体或应用 Key Vault 程序授予一组相应的权限, 使其能够执行加密操作。

有三个关键特性可设置为控制是否启用密钥并使用加密操作:

- **enabled**
- **nbf:** 在指定日期之前未启用
- **exp:** 到期日期

## <a name="storage-and-keys"></a>存储和密钥

客户管理的密钥更具灵活性, 可对要控制的密钥进行评估并对其进行管理。 它们还允许审核用于保护数据的加密密钥。
Key Vault 中存储和密钥有三个方面:

- Key Vault 托管存储帐户密钥
- 适用于静态数据的 Azure 存储服务加密 (SSE)
- 托管磁盘和 Azure 磁盘加密

Key Vault 的 Azure 存储帐户密钥管理是对 Key Vault 的密钥服务的扩展, 支持存储帐户密钥的同步和重新生成 (旋转)。  [Azure 存储与 Azure Active Directory 的集成](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)建议在发布时使用 (预览), 因为它可以提供更高的安全性和易用性。
SSE 使用两个密钥来管理静态数据的加密:

- 密钥加密密钥 (KEK)
- 数据加密密钥 (DEK)

虽然 Microsoft 管理 Dek, 但 SSE 有一个选项可用来使用可存储在 Key Vault 中的客户托管的 Kek。 这样, 就可以根据相应的符合性策略, Azure Key Vault 中的键轮换。 轮换密钥时, Azure 存储会重新加密该存储帐户的帐户加密密钥。 这不会导致对所有数据进行重新加密, 并且不需要其他操作。

SSE 用于托管磁盘, 但不支持客户管理的密钥。  可以在 Key Vault 中通过客户托管的 KEK 密钥使用 Azure 磁盘加密来加密托管磁盘。

## <a name="next-steps"></a>后续步骤

查看有关[联合身份验证](identity-federation.md)的文章

查看[引用库](reference-library.md)中的其他 Azure Key Vault 文档和教程
