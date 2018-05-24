---
title: 使用 Azure 门户管理 Azure DDoS 保护标准 | Microsoft Docs
description: 了解如何在 Azure Monitor 中使用 Azure DDoS 保护标准遥测来缓解攻击。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: dd094f2b9cdb9b5eb164dda2925d094cafa7cd89
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895606"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>使用 Azure 门户管理 Azure DDoS 保护标准

了解如何启用和禁用分布式拒绝服务 (DDoS) 保护，并使用标准 Azure DDoS 保护通过遥测来缓解 DDoS 攻击。 标准 DDoS 保护可保护分配有 Azure [公共 IP 地址](virtual-network-public-ip-address.md)的 Azure 资源，如虚拟机、负载均衡器和应用程序网关等。 若要了解有关标准 DDoS 保护及其功能的详细信息，请参阅[标准 DDoS 保护概述](ddos-protection-overview.md)。

在完成本教程中的任何步骤之前，使用分配到[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有“[权限](#permissions)”中所列适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的帐户登录到 Azure 门户 https://portal.azure.com。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-ddos-protection-plan"></a>创建 DDoS 防护计划

DDoS 防护计划在订阅中定义一组已启用 DDoS 防护标准的虚拟网络。 可以为组织配置一个 DDoS 防护计划，然后从多个订阅将虚拟网络链接到相同计划。 DDoS 防护计划本身也与在计划创建期间选择的订阅相关联。 如果受保护的公共 IP 地址超过 100，计划关联的订阅将产生针对计划以及超额费用的每月定期帐单。 有关 DDoS 定价的详细信息，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/ddos-protection/)。

大多数组织都不需要创建多个计划。 无法直接在订阅之间移动计划。 如果要更改计划所位于的订阅，需要[删除现有计划](#work-with-ddos-protection-plans)并创建新的计划。

1. 在 Azure 门户的左上角，选择“创建资源”。
2. 搜索 DDoS。 当“DDoS 防护计划”出现在搜索结果中时，将其选中。
3. 选择**创建**。
4. 输入或选择自己的值或以下示例值，然后选择“创建”：

    |设置        |值                                              |
    |---------      |---------                                          |
    |名称           | myDdosProtectionPlan                              |
    |订阅   | 选择订阅。                         |
    |资源组 | 选择“新建”，并输入 myResourceGroup |
    |Location       | 美国东部                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>为新的虚拟网络启用 DDoS

1. 在 Azure 门户的左上角，选择“创建资源”。
2. 选择“网络”，然后选择“虚拟网络”。
3. 输入或选择自己的值或以下示例值，接受其余默认值，然后选择“创建”：

    | 设置         | 值                                                        |
    | ---------       | ---------                                                    |
    | 名称            | myVirtualNetwork                                             |
    | 订阅    | 选择订阅。                                    |
    | 资源组  | 选择“使用现有资源组”，然后选择“myResourceGroup” |
    | Location        | 美国东部                                                      |
    | DDoS 防护 | 选择“标准”，然后在“DDoS 防护”下，选择“myDdosProtectionPlan”。 所选计划可位于与虚拟网络相同或不同的订阅中，但这两个订阅必须与同一 Azure Active Directory 租户相关联。|

如果已为虚拟网络启用 DDoS 防护，无法将虚拟网络移到其他资源组或订阅。 如果需要移动已启用 DDoS 标准的虚拟网络，请先禁用该标准并移动虚拟网络，然后启用 DDoS 标准。 移动后，适用于虚拟网络所有受保护的公共 IP 地址的自动优化策略阈值都将重置。

## <a name="enable-ddos-for-an-existing-virtual-network"></a>为现有虚拟网络启用 DDoS

1. 如果没有现有的 DDoS 防护计划，通过完成[创建 DDoS 防护计划](#create-a-ddos-protection-plan)中的步骤创建一个 DDoS 防护计划。
2. 在 Azure 门户的左上角，选择“创建资源”。
3. 输入想要在门户顶部的“搜索资源、服务和文档”框中为其启用 DDoS 防护标准的虚拟网络的名称。 当虚拟网络名称出现在搜索结果中时，将其选中。
4. 选择“设置”下的“DDoS 防护”。
5. 选择“标准”。 选择“DDoS 防护计划”下的现有 DDoS 防护计划，或在步骤 1 中创建的计划，然后选择“保存”。 所选计划可位于与虚拟网络相同或不同的订阅中，但这两个订阅必须与同一 Azure Active Directory 租户相关联。

## <a name="disable-ddos-for-a-virtual-network"></a>为虚拟网络禁用 DDoS

1. 输入想要在门户顶部的“搜索资源、服务和文档”框中为其禁用 DDoS 防护标准的虚拟网络的名称。 当虚拟网络名称出现在搜索结果中时，将其选中。
2. 选择“设置”下的“DDoS 防护”。
3. 选择“DDoS 防护计划”下的“基本”，然后选择“保存”。

## <a name="work-with-ddos-protection-plans"></a>使用 DDoS 防护计划

1. 在门户左上角选择“所有服务”。
2. 在“筛选器”框中输入 DDoS。 当“DDoS 防护计划”出现在结果中时，将其选中。
3. 从列表中选择要查看的保护计划。
4. 将列出与计划相关联的所有虚拟网络。
5. 如果要删除计划，必须首先与所有虚拟网络取消关联。 若要从虚拟网络中取消关联计划，请参阅[为虚拟网络禁用 DDoS](#disable-ddos-for-a-virtual-network)。

## <a name="configure-alerts-for-ddos-protection-metrics"></a>配置针对 DDoS 防护指标的警报

利用 Azure Monitor 警报配置，可以选择任何可用的 DDoS 防护指标，以在攻击期间发生活跃的风险缓解时发出警报。 当满足条件时，指定的地址会收到警报电子邮件：

1. 在门户左上角选择“所有服务”。
2. 在“筛选器”框中输入 Monitor。 当“Monitor”出现在结果中时，将其选中。
3. 选择“共享服务”下的“指标”。
4. 输入或选择自己的值或输入以下示例值，接受其余默认值，然后选择“确定”：

    |设置                  |值                                                                                               |
    |---------                |---------                                                                                           |
    |名称                     | myDdosAlert                                                                                        |
    |订阅             | 选择包含希望用于接收警报的公共 IP 地址的订阅。        |
    |资源组           | 选择包含希望用于接收警报的公共 IP 地址的资源组。      |
    |资源                 | 选择包含希望用于接收警报的公共 IP 地址的公共 IP 地址。 DDoS 监视分配给虚拟网络中的资源的公共 IP 地址。 如果虚拟网络中没有任何具有公共 IP 地址的资源，必须首先创建具有公共 IP 地址的资源。 可监视通过[适用于 Azure 服务的虚拟网络](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)中列出的资源管理器部署的所有资源的公共 IP 地址，Azure App Service 环境和 Azure VPN 网关除外。 可快速创建 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机继续本教程的内容。                   |
    |指标                   | 是否遭到 DDoS 攻击                                                                            |
    |阈值                | 1 - 1 表示遭到攻击。 0 表示未遭到攻击。                         |
    |周期                   | 选择所选的任何值。                                                                   |
    |通过电子邮件通知         | 选中复选框                                                                                  |
    |其他管理员 | 如果不是电子邮件所有者、参与者或订阅的阅读者，请输入你的电子邮件地址。 |

    检测到攻击几分钟后，将从 Azure Monitor 指标收到一封电子邮件，如下图所示：

    ![攻击警报](./media/manage-ddos-protection/ddos-alert.png)


若要模拟 DDoS 攻击来验证警报，请参阅[验证 DDoS 检测](#validate-ddos-detection)。

还可以了解有关为创建警报而[配置 webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和[逻辑应用](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)的详细信息。

## <a name="configure-logging-for-ddos-protection-metrics"></a>配置针对 DDoS 防护指标的日志记录

1. 在门户左上角选择“所有服务”。
2. 在“筛选器”框中输入 Monitor。 当“Monitor”出现在结果中时，将其选中。
3. 选择“设置”下的“诊断设置”。
4. 选择包含要记录的公共 IP 地址的订阅和资源组。
5. 为“资源类型”选择“公共 IP 地址”，然后选择要为其记录指标的特定公共 IP 地址。
6. 选择“启用诊断以收集以下数据”，然后根据需要选择任意多个以下选项：

    - **存档到存储帐户**：将数据写入 Azure 存储帐户。 若要了解有关此选项的详细信息，请参阅[存档诊断日志](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
    - **流式传输到事件中心**：允许日志接收器使用 Azure 事件中心选取日志。 事件中心将启用与 Splunk 或其他 SIEM 系统的集成。 若要了解有关此选项的详细信息，请参阅[将诊断日志流式传输到事件中心](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
    - **发送到 Log Analytics**：将日志写入到 Azure OMS Log Analytics 服务。 若要了解有关此选项的详细信息，请参阅[收集用于 Log Analytics 的诊断日志](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

若要模拟 DDoS 攻击来验证日志记录，请参阅[验证 DDoS 检测](#validate-ddos-detection)。

## <a name="use-ddos-protection-telemetry"></a>使用 DDoS 保护遥测

对攻击的遥测是通过 Azure Monitor 实时提供的。 遥测仅在公用 IP 地址处于风险缓解期间可用。 在攻击缓解之前或之后，都不会看到遥测。

1. 在门户左上角选择“所有服务”。
2. 在“筛选器”框中输入 Monitor。 当“Monitor”出现在结果中时，将其选中。
3. 选择“共享服务”下的“指标”。
4. 选择包含要为其遥测的公共 IP 地址的订阅和资源组。
5. 为“资源类型”选择“公共 IP 地址”，然后选择要为其遥测的特定公共 IP 地址。
6. 屏幕的左侧将显示一系列可用指标。 选中这些指标时，会在概述屏幕上的 **Azure Monitor 指标图表**中绘制这些指标。

指标名称提供不同的数据包类型和字节数与数据包数，每个指标都有如下所示的标记名称基本构造：

- **丢弃的标记名称（例如，丢弃的入站数据包 DDoS）**：由 DDoS 防护系统丢弃/清理的数据包数。
- **转发的标记名称（例如，转发的入站数据包 DDoS）**：由 DDoS 系统转发到目标 VIP 的数据包数 - 未筛选的流量。
- **无标记名称（例如，入站数据包 DDoS）**：进入到清理系统的总数据包数 – 表示丢弃的和转发的数据包的和。

若要模拟 DDoS 攻击来验证遥测，请参阅[验证 DDoS 检测](#validate-ddos-detection)。

## <a name="view-ddos-mitigation-policies"></a>查看 DDoS 缓解策略

标准 DDoS 防护针对已启用 DDoS 的虚拟网络中受保护资源的每个公共 IP 地址，应用三个自动优化的缓解策略（TCP SYN、TCP 和 UDP）。 通过选择“触发 DDoS 缓解的入站 TCP 数据包”和“触发 DDoS 缓解的入站 UDP 数据包”指标，可查看策略阈值，如下图中所示：

![查看缓解策略](./media/manage-ddos-protection/view-mitigation-policies.png)

策略阈值是通过基于 Azure 机器学习的网络流量探查自动配置的。 仅当超过策略阈值时，才会对受攻击的 IP 地址进行 DDoS 缓解。

## <a name="validate-ddos-detection"></a>验证 DDoS 检测

Mircosoft 已与 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) 合作构建接口，用户可在其中针对已启用 DDoS 保护的公共 IP 地址生成用于模拟的流量。 借助 BreakPoint Cloud 模拟，你可以：

- 验证 Microsoft Azure DDoS 防护如何防范 Azure 资源受到 DDoS 攻击
- 受到 DDoS 攻击时优化事件响应过程
- 阐述 DDoS 符合性
- 培训网络安全团队

## <a name="permissions"></a>权限

若要使用 DDoS 保护计划，必须将你的帐户分配到[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有下表所列适当操作的[自定义](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

| 操作                                            | 名称                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | 读取 DDoS 保护计划              |
| Microsoft.Network/ddosProtectionPlans/write       | 创建或更新 DDoS 保护计划  |
| Microsoft.Network/ddosProtectionPlans/delete      | 删除 DDoS 保护计划            |
| Microsoft.Network/ddosProtectionPlans/join/action | 加入 DDoS 保护计划              |

若要为虚拟网络启用 DDoS 保护，还必须为你的帐户分配[适用于虚拟网络的适当操作](manage-virtual-network.md#permissions)。

## <a name="next-steps"></a>后续步骤

- 为虚拟网络创建并应用 [Azure 策略](policy-samples.md)