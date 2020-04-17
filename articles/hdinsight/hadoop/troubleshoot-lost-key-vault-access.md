---
title: 具有磁盘加密的 Azure HDInsight 群集失去密钥保管库访问权限
description: 在与 Azure HDInsight 群集交互时，对问题的步骤和可能的解决方案进行故障排除。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: b1d941fbf86d453a56a5157ed988a32173c614fc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461525"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>方案：具有磁盘加密的 Azure HDInsight 群集失去密钥保管库访问权限

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

资源运行状况中心 （RHC） 警报`The HDInsight cluster is unable to access the key for BYOK encryption at rest`显示，用于自带密钥 （BYOK） 群集，其中群集节点已失去对客户密钥保管库 （KV） 的访问。 类似的警报也可以看到阿帕奇安巴里 UI。

## <a name="cause"></a>原因

警报可确保 KV 可从群集节点访问，从而确保用户分配的托管标识的网络连接、KV 运行状况和访问策略。 此警报只是后续节点重新启动时即将关闭代理的警告，群集将继续运行，直到节点重新启动。

导航到 Apache Ambari UI，从**磁盘加密密钥保管库状态**中查找有关警报的详细信息。 此警报将提供有关验证失败原因的详细信息。

## <a name="resolution"></a>解决方法

### <a name="kvaad-outage"></a>KV/AAD 中断

有关详细信息，请查看[Azure 密钥保管库可用性和冗余以及](../../key-vault/general/disaster-recovery-guidance.md)Azure 状态页https://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV 意外删除

* 将 KV 上已删除的密钥还原为自动恢复。 有关详细信息，请参阅[恢复已删除的密钥](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey)。
* 联系 KV 团队，从意外删除中恢复。

### <a name="kv-access-policy-changed"></a>KV 访问策略已更改

还原分配给 HDI 群集以访问 KV 的用户分配的托管标识的访问策略。

### <a name="key-permitted-operations"></a>密钥允许的操作

对于 KV 中的每个键，您可以选择允许的操作集。 确保为 BYOK 密钥启用了换行和解包操作

### <a name="expired-key"></a>过期密钥

如果过期已过且密钥未旋转，请从备份 HSM 还原密钥或联系 KV 团队以清除到期日期。

### <a name="kv-firewall-blocking-access"></a>KV 防火墙阻止访问

修复 KV 防火墙设置，允许 BYOK 群集节点访问 KV。

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>关于虚拟网络阻止访问的 NSG 规则

检查与连接到群集的虚拟网络关联的 NSG 规则。

## <a name="mitigation-and-prevention-steps"></a>缓解和预防步骤

### <a name="kv-accidental-deletion"></a>KV 意外删除

* 使用[资源锁定集](../../azure-resource-manager/management/lock-resources.md)配置密钥保管库。
* 将密钥备份到其硬件安全模块。

### <a name="key-deletion"></a>密钥删除

在删除密钥之前，应删除群集。

### <a name="kv-access-policy-changed"></a>KV 访问策略已更改

定期审核和测试访问策略。

### <a name="expired-key"></a>过期密钥

* 将密钥备份到 HSM。
* 使用不设置任何过期集的密钥。
* 如果需要设置过期，请在到期日期之前旋转键。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport)- 用于改善客户体验的官方 Microsoft Azure 帐户连接。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
