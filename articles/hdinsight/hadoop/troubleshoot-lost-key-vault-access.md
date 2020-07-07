---
title: 已启用磁盘加密的 Azure HDInsight 群集失去 Key Vault 访问权限
description: 与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: b1d941fbf86d453a56a5157ed988a32173c614fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81461525"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>方案：已启用磁盘加密的 Azure HDInsight 群集失去 Key Vault 访问权限

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

当“创建自己的密钥”(BYOK) 群集节点失去客户 Key Vault (KV) 的访问权限时，会针对该群集显示资源运行状况中心 (RHC) 警报 `The HDInsight cluster is unable to access the key for BYOK encryption at rest`。 Apache Ambari UI 中也会显示类似的警报。

## <a name="cause"></a>原因

此警报确保可从群集节点访问 KV，从而确保网络连接、KV 运行状况以及用户分配的托管标识的访问策略正常。 此警报只是警告接下来在重新启动节点时中介即将关闭，但在重新启动节点之前，群集可继续正常运行。

导航到 Apache Ambari UI，在“磁盘加密 Key Vault 状态”中找到有关警报的详细信息。  此警报会详细说明验证失败的原因。

## <a name="resolution"></a>解决方法

### <a name="kvaad-outage"></a>KV/AAD 中断

查看 [Azure Key Vault 可用性和冗余](../../key-vault/general/disaster-recovery-guidance.md)以及 Azure 状态页了解更多详细信息 https://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV 意外删除

* 还原 KV 上已删除的密钥，以自动进行恢复。 有关详细信息，请参阅[恢复已删除的密钥](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey)。
* 联系 KV 团队，以便在意外删除后进行恢复。

### <a name="kv-access-policy-changed"></a>KV 访问策略已更改

还原分配到 HDI 群集的、用户分配的托管标识的访问策略，以便能够访问 KV。

### <a name="key-permitted-operations"></a>密钥允许的操作

对于 KV 中的每个密钥，可以选择一组允许的操作。 确保为 BYOK 密钥启用包装和解包操作

### <a name="expired-key"></a>过期的密钥

如果密钥已过期但尚未轮换密钥，请从备份 HSM 还原密钥，或者联系 KV 团队来清除过期日期。

### <a name="kv-firewall-blocking-access"></a>KV 防火墙阻止访问

修复 KV 防火墙设置，以允许 BYOK 群集节点访问 KV。

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>虚拟网络中的 NSG 规则阻止访问

检查与附加到群集的虚拟网络相关联的 NSG 规则。

## <a name="mitigation-and-prevention-steps"></a>缓解和预防措施

### <a name="kv-accidental-deletion"></a>KV 意外删除

* 配置 Key Vault 并[设置资源锁](../../azure-resource-manager/management/lock-resources.md)。
* 将密钥备份到其硬件安全模块。

### <a name="key-deletion"></a>密钥删除

删除密钥之前应删除群集。

### <a name="kv-access-policy-changed"></a>KV 访问策略已更改

定期审核并测试访问策略。

### <a name="expired-key"></a>过期的密钥

* 将密钥备份到 HSM。
* 使用未设置任何过期时间的密钥。
* 如果需要设置过期时间，请在过期日期之前轮换密钥。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
