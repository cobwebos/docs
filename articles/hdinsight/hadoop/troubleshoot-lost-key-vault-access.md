---
title: 具有磁盘加密的 Azure HDInsight 群集失去 Key Vault 访问权限
description: 排查在与 Azure HDInsight 群集交互时遇到的问题的步骤和可能的解决方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: 2ae389be25cd8633a53a49cf000796c1510733a1
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965159"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>方案：具有磁盘加密的 Azure HDInsight 群集失去 Key Vault 访问权限

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

对于群集节点无法访问客户 Key Vault （KV）的创建自己的密钥（BYOK）群集，将显示资源运行状况中心（RHC）警报 `The HDInsight cluster is unable to access the key for BYOK encryption at rest`。 也可以在 Apache Ambari UI 上查看类似的警报。

## <a name="cause"></a>原因

此警报确保 KV 可从群集节点访问，从而确保为用户分配的托管标识提供网络连接、KV 运行状况和访问策略。 此警报只是在后续节点重新启动时即将关闭代理的警告，群集将继续运行，直到节点重新启动。

导航到 Apache Ambari UI，以查找有关从**磁盘加密 Key Vault 状态**的警报的详细信息。 此警报会详细介绍验证失败的原因。

## <a name="resolution"></a>分辨率

### <a name="kvaad-outage"></a>KV/AAD 中断

有关更多详细信息，请查看[Azure Key Vault 可用性和冗余](../../key-vault/key-vault-disaster-recovery-guidance.md)和 Azure 状态页 https://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV 意外删除

* 将 KV 上的已删除密钥还原为自动恢复。 有关详细信息，请参阅[恢复已删除的密钥](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey)。
* 联系 KV 团队以从意外删除中恢复。

### <a name="kv-access-policy-changed"></a>KV 访问策略已更改

为用户分配的、分配到 HDI 群集的托管标识还原访问策略，以访问 KV。

### <a name="key-permitted-operations"></a>允许的关键操作

对于 KV 中的每个键，您可以选择一组允许的操作。 确保为 BYOK 键启用了包装和解包操作

### <a name="expired-key"></a>过期的密钥

如果过期时间已过，并且密钥不旋转，请从备份 HSM 还原密钥，或联系 KV 团队来清除到期日期。

### <a name="kv-firewall-blocking-access"></a>KV 防火墙阻止访问

修复 KV 防火墙设置，以允许 BYOK 群集节点访问 KV。

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>有关虚拟网络阻止访问的 NSG 规则

检查与附加到群集的虚拟网络相关联的 NSG 规则。

## <a name="mitigation-and-prevention-steps"></a>缓解和预防步骤

### <a name="kv-accidental-deletion"></a>KV 意外删除

* 配置具有[资源锁集](../../azure-resource-manager/management/lock-resources.md)的 Key Vault。
* 将密钥备份到其硬件安全模块。

### <a name="key-deletion"></a>删除密钥

删除密钥之前应删除群集。

### <a name="kv-access-policy-changed"></a>KV 访问策略已更改

定期审核和测试访问策略。

### <a name="expired-key"></a>过期的密钥

* 将密钥备份到 HSM。
* 使用没有任何过期集的密钥。
* 如果需要设置到期时间，请在到期日期之前轮换密钥。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
