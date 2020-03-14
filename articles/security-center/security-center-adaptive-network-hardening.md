---
title: Azure 安全中心的自适应网络强化 |Microsoft Docs
description: 了解如何使用实际的流量模式来强化网络安全组（NSG）规则，并进一步改善安全状况。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: bc610fa1d7a5fa1a10db3298164404b92d5d9f85
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139583"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure 安全中心的自适应网络强化
了解如何在 Azure 安全中心配置自适应网络强化。

## <a name="what-is-adaptive-network-hardening"></a>什么是自适应网络强化？
应用[网络安全组（NSG）](https://docs.microsoft.com/azure/virtual-network/security-overview)来筛选进出资源的流量，改善网络安全状况。 但是，在某些情况下，通过 NSG 流动的实际流量是所定义的 NSG 规则的子集。 在这些情况下，可以根据实际流量模式强化 NSG 规则，从而进一步改善安全状况。

自适应网络强化为进一步强化 NSG 规则提供了建议。 它使用机器学习算法，这种算法是实际流量、已知受信任的配置、威胁智能和其他损害迹象的因素，然后提供仅允许来自特定 IP/端口元组的流量的建议。

例如，假设现有的 NSG 规则是允许端口22上的 140.20.30.10/24 流量。 根据分析，自适应网络强化建议会缩小范围并允许来自 140.23.30.10/29 的流量（这是较窄的 IP 范围），并拒绝到该端口的所有其他流量。

>[!TIP]
> 自适应网络强化建议仅在特定端口上受支持。 有关完整列表，请参阅下面的[支持哪些端口？](#which-ports-are-supported) 


![网络强化视图](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>查看自适应网络强化警报和规则

1. 在安全中心中，选择 "**网络**" -> **自适应网络强化**。 网络 Vm 在三个不同的选项卡下列出：
   * 不**正常的资源**：当前具有通过运行自适应网络强化算法触发的建议和警报的 vm。 
   * **正常资源**：没有警报和建议的 vm。
   * 未**扫描的资源**：自适应网络强化算法无法在其上运行的 vm，原因如下：
      * **Vm 为经典 vm**：仅支持 Azure 资源管理器 vm。
      * **数据不足**：若要生成准确的流量强化建议，安全中心需要至少30天的流量数据。
      * **VM 未按 ASC 标准进行保护**：仅设置为安全中心标准定价层的 vm 有资格使用此功能。

     ![不正常资源](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. 从 "不**正常资源**" 选项卡中，选择要查看其警报的 VM，并选择要应用的建议强化规则。

    ![强化警报](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>查看并应用自适应网络强化建议规则

1. 从 "不**正常资源**" 选项卡中，选择一个 VM。 列出了警报和建议的强化规则。

     ![强化规则](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > "**规则**" 选项卡列出自适应网络强化建议你添加的规则。 "**警报**" 选项卡列出了由于流量而生成的警报，这些警报流向了资源，而不是推荐的规则中所允许的 IP 范围内。

2. 如果要更改规则的某些参数，可对其进行修改，如[修改规则](#modify-rule)中所述。
   > [!NOTE]
   > 你还可以[删除](#delete-rule)或[添加](#add-rule)规则。

3. 选择要应用于 NSG 的规则，然后单击 "**强制**"。

      > [!NOTE]
      > 强制执行的规则将添加到保护 VM 的 NSG。 （VM 可通过关联到其 NIC 的 NSG 或 VM 所在的子网，或两者）进行保护

    ![强制执行规则](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### 修改规则<a name ="modify-rule"> </a>

你可能需要修改建议的规则参数。 例如，你可能想要更改建议的 IP 范围。

修改自适应网络强化规则的一些重要准则：

* 只能修改 "允许" 规则的参数。 
* 不能将 "允许" 规则更改为 "拒绝" 规则。 

  > [!NOTE]
  > 创建和修改 "拒绝" 规则是直接在 NSG 上完成的。 有关详细信息，请参阅[创建、更改或删除网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

* "**拒绝所有流量**" 规则是此处列出的唯一 "拒绝" 规则类型，不能修改。 不过，您可以删除它（请参阅[删除规则](#delete-rule)）。
  > [!NOTE]
  > 如果运行算法，安全中心不会根据现有的 NSG 配置来识别应允许的流量，则建议使用 "**拒绝所有流量**" 规则。 因此，建议将所有流量拒绝到指定端口。 此类规则的名称显示为 "*系统已生成*"。 强制执行此规则后，其在 NSG 中的实际名称将为包含协议、流量方向、"拒绝" 和随机数字的字符串。

*修改自适应网络强化规则：*

1. 若要修改规则的某些参数，请在 "**规则**" 选项卡中，单击规则行末尾的三个点（...），然后单击 "**编辑**"。

   ![编辑规则](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. 在 "**编辑规则**" 窗口中，更新要更改的详细信息，然后单击 "**保存**"。

   > [!NOTE]
   > 单击 "**保存**" 后，已成功更改规则。 *但尚未将其应用到 NSG。* 若要应用该规则，必须在列表中选择规则，然后单击 "**强制**" （如下一步中所述）。

   ![编辑规则](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. 若要应用更新的规则，请从列表中选择更新的规则，然后单击 "**强制**"。

    ![强制规则](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### 添加新规则<a name ="add-rule"> </a>

可以添加安全中心不推荐的 "允许" 规则。

> [!NOTE]
> 此处只能添加 "允许" 规则。 如果要添加 "拒绝" 规则，可以直接在 NSG 上执行此操作。 有关详细信息，请参阅[创建、更改或删除网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

*添加自适应网络强化规则：*

1. 单击 "**添加规则**" （位于左上角）。

   ![添加规则](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. 在 "**新建规则**" 窗口中，输入详细信息，然后单击 "**添加**"。

   > [!NOTE]
   > 单击 "**添加**" 后，您已成功添加了规则，并列出了其他建议的规则。 但是，你未将其应用于 NSG。 若要激活它，你必须在列表中选择规则，然后单击 "**强制**" （如下一步中所述）。

3. 若要应用新规则，请从列表中选择新规则，然后单击 "**强制**"。

    ![强制规则](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### 删除规则<a name ="delete-rule"> </a>

必要时，可以删除当前会话的建议规则。 例如，你可能会确定应用建议的规则可能会阻止合法的流量。

*删除当前会话的自适应网络强化规则：*

1. 在 "**规则**" 选项卡中，单击规则行末尾的三个点（...），然后单击 "**删除**"。  

    ![强化规则](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)

 

## <a name="which-ports-are-supported"></a>支持哪些端口？

自适应网络强化建议仅在特定端口上受支持。 此表提供完整列表：

|端口|协议|关联服务|
|:---:|:----:|:----|
|13|UDP|日间服务|
|17|UDP|QOTD 协议|
|19|UDP|CHARGEN 协议|
|22|TCP|SSH|
|23|TCP|Telnet|
|53|UDP|DNS|
|69|UDP|TFTP|
|81|TCP|潜在恶意（TOR 出口节点）|
|111|TCP/UDP|RPC|
|119|TCP|NNTP|
|123|UDP|NTP|
|135|TCP/UDP|终结点映射器;RPCDCE|
|137|TCP/UDP|NetBIOS 名称服务|
|138|TCP/UDP|NetBIOS 数据报服务|
|139|TCP|NetBIOS 会话服务|
|161|TCP/UDP|SNMP|
|162|TCP/UDP|SNMP|
|389|TCP|LDAP|
|445|TCP|SMB|
|512|TCP|Rexec|
|514|TCP|远程 shell|
|593|TCP/UDP|HTTP RPC|
|636|TCP|LDAP|
|873|TCP|Rsync|
|1433|TCP|MS SQL|
|1434|UDP|MS SQL|
|1900|UDP|SSDP|
|1900|UDP|SSDP|
|2049|TCP/UDP|NFS|
|2301|TCP|Compaq 管理服务|
|2323|TCP|三维-nfsd|
|2381|TCP|Compaq 管理服务|
|3268|TCP|LDAP|
|3306|TCP|MySQL|
|3389|TCP|RDP|
|4333|TCP|mSQL|
|5353|UDP|Mdn|
|5432|TCP|PostgreSQL|
|5555|TCP|个人代理;HP OmniBack|
|5800|TCP|VNC|
|5900|TCP|远程帧缓冲区;VNC|
|5900|TCP|VNC|
|5985|TCP|Windows PowerShell|
|5986|TCP|Windows PowerShell|
|6379|TCP|Redis|
|6379|TCP|Redis|
|7000|TCP|Cassandra|
|7001|TCP|Cassandra|
|7199|TCP|Cassandra|
|8081|TCP|CosmosDBSun 代理管理员|
|8089|TCP|Splunk|
|8545|TCP|潜在恶意（Cryptominer）|
|9042|TCP|Cassandra|
|9160|TCP|Cassandra|
|9300|TCP|Elasticsearch|
|11211|UDP|Memcached|
|16379|TCP|Redis|
|26379|TCP|Redis|
|27017|TCP|MongoDB|
|37215|TCP|潜在恶意|
||||