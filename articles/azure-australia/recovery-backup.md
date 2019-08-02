---
title: Azure 澳大利亚的备份和灾难恢复
description: 适用于澳大利亚政府机构的 Microsoft Azure 中的备份和灾难恢复, 因为它与 ".ASD
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 078918cbfeffb1ff5f3626b5add96bc6622a90a7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571518"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Azure 澳大利亚的备份和灾难恢复

具有支持的基础结构的备份和灾难恢复计划对所有组织都非常重要。 将备份解决方案包含在[澳大利亚网络安全中心的基本 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm)中, 重点介绍了该解决方案的重要性。

Microsoft Azure 提供了两种实现复原能力的服务:Azure 备份和 Azure Site Recovery。 利用这些服务, 你可以在本地和云中保护数据, 从而实现各种设计方案。 Azure 备份和 Azure Site Recovery 都使用常见存储和管理资源: Azure 恢复服务保管库。 此保管库用于管理、监视和隔离 Azure 备份和 Azure Site Recovery 数据。

本文详细介绍了用于实现 Azure 备份的关键设计元素 Azure Site Recovery, 并详细介绍了[澳大利亚国防局 (.asd) 信息安全手册 (ISM) 控件](https://acsc.gov.au/infosec/ism/index.htm)。

## <a name="azure-backup"></a>Azure 备份

![Azure 备份](media/backup-overview.png)

Azure 备份类似于传统的本地备份解决方案, 可用于备份本地数据和 Azure 托管数据。 可以使用 azure 备份将以下数据类型备份到 Azure:

* 文件和文件夹
* 受支持的 Windows 和 Linux 操作系统:
  * Hyper-v 和 VMWare 虚拟机监控程序
  * 物理硬件
* 支持的 Microsoft 应用程序

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

Azure Site Recovery 复制包含单个虚拟机或多层应用程序的工作负荷。 支持从本地复制到 Azure、在 Azure 区域之间或 Azure Site Recovery 安排的本地位置之间进行复制。 本地虚拟机可复制到 Azure 或受支持的本地虚拟机监控程序。 配置后, Azure Site Recovery 会对复制、故障转移和故障回复进行协调。

## <a name="key-design-considerations"></a>关键设计注意事项

实现备份或灾难恢复解决方案时, 建议的解决方案需要考虑:

* 要捕获的数据的范围和数量
* 数据将保留多长时间
* 如何安全地存储和管理此数据
* 存储数据的地理位置
* 例程系统测试过程

ISM 提供有关设计解决方案时应进行的安全注意事项的指导。 Microsoft Azure 提供了解决这些安全注意事项的方法。

### <a name="data-sovereignty"></a>数据主权

组织需要确保在 utilising 基于云的存储位置时保留数据主权。 Azure 策略提供限制可创建 Azure 资源的允许位置的方法。 内置 Azure 策略 "允许的位置" 用于确保在指定的 Azure 策略范围内创建的任何 Azure 资源只能在指定的地理位置创建。

Azure 资源的地理限制的 Azure 策略项包括:

* allowedLocations
* allowedSingleLocation

这些策略允许 Azure 管理员将创建限制为已命名的位置列表, 甚至可限制为单个地理位置。

### <a name="redundant-and-geographically-dispersed-storage"></a>冗余和地理位置分散的存储

存储在 Azure 恢复服务保管库中的数据始终存储在冗余存储中。 默认情况下, 恢复服务保管库使用 Azure 地域冗余存储 (GRS)。 使用 GRS 存储的数据将复制到恢复服务保管库的[辅助配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中的其他 Azure 数据中心。 此复制的数据将存储为只读, 并且仅在存在 Azure 故障转移事件时才可写。 在 Azure 数据中心内, 数据在单独的容错域和升级域之间进行复制, 以最大限度地提高硬件或基于维护的中断的可能性。 每年 GRS 提供至少 99.99999999999999% 的可用性。

Azure 恢复服务保管库可配置为利用本地冗余存储 (LRS)。 LRS 是一种成本较低的存储选项, 降低了可用性。 此冗余模型在不同的容错域和升级域之间采用相同的复制, 但不会在地理区域之间复制。 LRS 存储中的数据, 而不像 GRS 一样弹性, 每年仍提供至少 99.999999999% 的可用性。

与传统的非现场存储技术 (如磁带介质) 不同, 会自动创建数据的其他副本, 无需任何额外的管理开销。

### <a name="restricted-access-and-activity-monitoring"></a>受限访问和活动监视

必须防止备份数据损坏、修改和未审批删除。 Azure 备份和 Azure Site Recovery 都利用了常用的 Azure 管理结构。 此构造向位于 Azure 中的资源提供详细的审核、日志记录和基于角色的访问控制 (RBAC)。 可以限制对备份数据的访问以选择管理人员, 并且可以记录和审核涉及备份数据的所有操作。

Azure 备份和 Azure Site Recovery 都具有内置日志记录和报告功能。 在备份或复制期间出现的任何问题都将报告给使用 Azure 管理结构的管理员。

Azure 恢复服务保管库还提供了以下额外的数据安全措施:

* 执行删除操作后, 备份数据将保留14天
* 关键操作的警报和通知, 如 "停止包含删除数据的备份"
* 关键操作的安全 PIN 要求
* 最小保持期检查已准备就绪

以下最小保持期检查包括:

* 对于每日保留期, 最少保留7天
* 对于每周保留期, 最少保留四周的保留期
* 对于每月保留期, 最少三个月的保留期
* 对于每年保留, 最少一年的保留期

Azure 中存储的所有备份数据都使用 Azure 存储服务加密 (SSE) 进行静态加密。 默认情况下, 将为所有新的和现有的存储帐户启用此功能, 并且无法禁用。 加密数据会在检索过程中自动解密。 默认情况下, 使用 SSE 加密的数据使用由提供并由 Microsoft 托管的密钥进行加密。 组织可以选择提供和管理自己的加密密钥, 以便与 SSE 一起使用。 这为加密的数据提供了一个可选的附加安全层。 此密钥可由客户在本地或安全地存储在 Azure 密钥保管库中。

### <a name="secure-data-transport"></a>安全数据传输

使用 AES 256 在传输过程中加密的 Azure 备份数据。 此数据通过使用管理人员在首次配置备份时创建的密码来保护。 Microsoft 无权访问此通行短语, 这意味着客户必须确保安全地存储此密码。 然后, 通过安全的 HTTPS 连接在本地环境和 Azure 恢复服务保管库之间进行数据传输。  然后, 使用 Azure SSE 对恢复服务保管库中的数据进行静态加密。

还会始终在传输中加密 Azure Site Recovery 数据。 所有复制的数据都将使用 HTTPS 和 TLS 安全地传输到 Azure。 当 Azure 客户使用 ExpressRoute 连接连接到 Azure 时, Azure Site Recovery 通过此专用连接发送数据。  当 Azure 客户使用 VPN 连接连接到 Azure 时, 将通过 internet 安全地在本地和恢复服务保管库之间复制数据。

这一安全网络数据传输消除了管理传统的场外备份存储解决方案 (例如磁带介质) 的安全风险和缓解要求。

### <a name="data-retention-periods"></a>数据保持期

建议至少使用三个月的备份保留期, 但通常需要较长的保留期。 Azure 备份最多可提供9999个备份副本。 如果每天创建一个受保护实例的单个 Azure 备份, 这将允许保留27年的每日备份。 受保护实例每月的备份允许833年的保留期。 由于备份数据已过时, 并且在一段时间后会保留不太细化的备份, 因此备份数据的总保留期会增长。  Azure 不会限制数据在 Azure 恢复服务保管库中保留的时间长度, 而只会限制每个实例的总备份数。 从旧备份或新备份还原之间也没有性能差异, 每次还原所需的时间也相同。

Azure 恢复服务保管库已有大量的默认备份和保留策略。  管理人员还可以创建自定义的备份和保留策略。

![Azure 备份策略](media/create-policy.png)

需要在配置 Azure 备份和保留策略时找到备份频率和长期保留要求之间的平衡。

### <a name="backup-and-restore-testing"></a>备份和还原测试

ISM 建议测试备份数据, 以确保受保护的数据在需要还原或故障转移时有效。 Azure 备份和 Azure Site Recovery 还提供了一种方法, 用于在备份或复制受保护的数据后对其进行测试。 可以将 Azure 备份管理的数据还原到一个命名的位置, 然后可以验证数据的一致性。

Azure Site Recovery 提供了执行故障转移测试的内置功能。 复制到恢复服务保管库的工作负荷可以还原到一个命名的 Azure 环境。 目标还原环境可以完全独立于任何生产环境, 以确保在执行测试时不会对生产系统产生任何影响。 测试完成后, 可以立即删除测试环境和所有资源以降低运营成本。

可使用内置于 Azure 平台中的 Azure 自动化服务自动执行故障转移测试和验证。 这允许将故障转移测试计划为自动执行, 以确保将数据成功复制到 Azure。

## <a name="next-steps"></a>后续步骤

请查看有关[确保 Azure 策略的安全性](azure-policy.md)一文。
