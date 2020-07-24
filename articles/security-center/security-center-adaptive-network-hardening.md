---
title: Azure 安全中心的自适应网络强化 |Microsoft Docs
description: 了解如何使用实际的流量模式来强化网络安全组 (NSG) 规则，并进一步改善安全状况。
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
ms.openlocfilehash: d12df01fe1506f7f5ade9cce60ae7af0412e3010
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080805"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure 安全中心内的自适应网络强化
了解如何在 Azure 安全中心内配置自适应网络强化。

## <a name="what-is-adaptive-network-hardening"></a>什么是自适应网络强化？
应用[网络安全组 (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) 来筛选发往/来自资源的流量，可以改善网络安全状况。 但是，仍然可能存在一些这样的情况：通过 NSG 流动的实际流量是所定义 NSG 规则的子集。 在这些情况下，可以根据实际流量模式强化 NSG 规则，从而进一步改善安全状况。

自适应网络强化为进一步强化 NSG 规则提供了建议。 它使用机器学习算法，这种算法会将实际流量、已知受信任的配置、威胁情报和其他泄露标志都考虑在内，然后提供仅允许来自特定 IP/端口元组的流量的建议。

例如，假设现有的 NSG 规则是在端口 22 上允许来自 140.20.30.10/24 流量。 基于分析提供的自适应网络强化建议会是，缩小范围并允许来自 140.23.30.10/29 的流量（这是更小的 IP 范围），拒绝发往该端口的所有其他流量。

>[!TIP]
> 自适应网络强化建议仅在以下特定端口上受支持（适用于 UDP 和 TCP）：13、17、19、22、23、53、69、81、111、119、123、135、137、138、139、161、162、389、445、512、514、593、636、873、1433、1434、1900、2049、2301、2323、2381、3268、3306、3389、4333、5353、5432、5555、5800、5900、5900、5985、5986、6379、6379、7000、7001、7199、8081、8089、8545、9042、9160、9300、11211、16379、26379、27017、37215


![“网络强化”视图](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>查看“自适应网络强化”警报和规则

1. 在安全中心内，选择“网络” -> “自适应网络强化”。 网络 VM 将在下面三个不同的选项卡下列出：
   * **不正常的资源**：当前具有通过运行自适应网络强化算法触发的建议和警报的 VM。 
   * **正常的资源**：没有警报和建议的 VM。
   * **未扫描的资源**：由于以下原因之一而无法运行自适应网络强化算法的 VM：
      * **VM 是经典 VM**：只有 Azure 资源管理器 VM 受支持。
      * **没有足够的数据可用**：为了生成准确的流量强化建议，安全中心至少需要 30 天的流量数据。
      * **VM 未按 ASC 标准进行保护**：仅设置为安全中心标准定价层的 vm 有资格使用此功能。

     ![不正常的资源](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. 从“不正常的资源”选项卡中，选择要查看其警报的 VM，并选择要应用的建议强化规则。

    ![强化警报](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>查看并应用自适应网络强化建议规则

1. 从“不正常的资源”选项卡上，选择一个 VM。 此时会列出警报和建议的强化规则。

     ![强化规则](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > “规则”选项卡列出了自适应网络强化建议添加的规则。 “警报”选项卡列出了由于流量（流向不在建议规则所允许的 IP 范围内的资源）而生成的警报。

2. 如果要更改规则的某些参数，可以对其进行修改，如[修改规则](#modify-rule)中所述。
   > [!NOTE]
   > 还可以[删除](#delete-rule)或[添加](#add-rule)规则。

3. 选择要应用于 NSG 的规则，然后单击“强制执行”。

      > [!NOTE]
      > 强制执行的规则将添加到保护 VM 的 NSG。 （VM 可由关联到其 NIC 的 NSG 和/或 VM 所在的子网保护）

    ![强制执行规则](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>修改规则  <a name ="modify-rule"> </a>

你可能想要修改已建议的规则的参数。 例如，你可能想要更改建议的 IP 范围。

有关修改自适应网络强化规则的一些重要准则：

* 只能修改“允许”规则的参数。 
* 不能将“允许”规则更改为“拒绝”规则。 

  > [!NOTE]
  > 创建和修改“拒绝”规则是直接在 NSG 上执行的。 有关详细信息，请参阅[创建、更改或删除网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

* **拒绝所有流量**规则是此处列出的唯一“拒绝”规则类型，并且该规则不能修改。 不过，你可以删除它（请参阅[删除规则](#delete-rule)）。
  > [!NOTE]
  > 在运行算法后，如果安全中心根据现有的 NSG 配置未识别出应允许的流量，则会建议执行**拒绝所有流量**规则。 因此，建议的规则是拒绝发往指定端口的所有流量。 此类型规则的名称显示为“系统生成”。 强制执行此规则后，此规则在 NSG 中的实际名称将是包含协议、流量方向、“DENY”和随机数字的字符串。

若要修改自适应网络强化规则，请执行以下操作：

1. 若要修改规则的某些参数，请在“规则”选项卡中单击规则行末尾的三个点 (...)，然后单击“编辑”。

   ![编辑规则](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. 在“编辑规则”窗口中，更新要更改的详细信息，然后单击“保存”。

   > [!NOTE]
   > 单击“保存”后，便已成功更改了规则。 但尚未将其应用到 NSG。 若要应用该规则，必须在列表中选择该规则，然后单击“强制执行”（如下一步所述）。

   ![编辑规则](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. 若要应用更新的规则，请从列表中选择更新的规则，然后单击“强制执行”。

    ![强制执行规则](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>添加新规则 <a name ="add-rule"> </a>

可以添加安全中心未建议的“允许”规则。

> [!NOTE]
> 在这里只能添加“允许”规则。 如果要添加“拒绝”规则，可以直接在 NSG 上执行此操作。 有关详细信息，请参阅[创建、更改或删除网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

若要添加自适应网络强化规则，请执行以下操作：

1. 单击“添加规则”（位于左上角）。

   ![添加规则](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. 在“新建规则”窗口中，输入详细信息，然后单击“添加”。

   > [!NOTE]
   > 单击“添加”后，便已成功添加了规则，该规则会随其他建议的规则一起列出。 但是，还尚未将其应用到 NSG。 若要激活该规则，必须在列表中选择该规则，然后单击“强制执行”（如下一步所述）。

3. 若要应用新规则，请从列表中选择新规则，然后单击“强制执行”。

    ![强制执行规则](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>删除规则 <a name ="delete-rule"> </a>

必要时，可以删除当前会话的建议规则。 例如，你可能会确定应用建议的规则会阻止合法的流量。

若要删除当前会话的自适应网络强化规则，请执行以下操作：

1. 在“规则”选项卡中单击规则行末尾的三个点 (...)，然后单击“删除”。  

    ![强化规则](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)