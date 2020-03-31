---
title: Azure 安全中心的自适应网络强化 |微软文档
description: 了解如何使用实际流量模式强化网络安全组 （NSG） 规则并进一步改善安全状况。
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
ms.openlocfilehash: a75be23e2e8215d86aebcfd7f4317f2f597d3c5b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385072"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure 安全中心的自适应网络强化
了解如何在 Azure 安全中心配置自适应网络强化。

## <a name="what-is-adaptive-network-hardening"></a>什么是自适应网络强化？
应用[网络安全组 （NSG）](https://docs.microsoft.com/azure/virtual-network/security-overview)来过滤资源中和来自资源的流量，从而改善网络安全状况。 但是，在某些情况经 NSG 的实际流量仍然是定义的 NSG 规则的子集。 在这些情况下，可以通过根据实际流量模式强化 NSG 规则来进一步改善安全状态。

自适应网络强化提供了进一步强化 NSG 规则的建议。 它使用机器学习算法，该算法将实际流量、已知可信配置、威胁情报和其他危害指标的因素考虑在内，然后提供建议，仅允许来自特定 IP/端口组合的流量。

例如，假设现有的 NSG 规则允许在端口 22 上从 140.20.30.10/24 的流量。 自适应网络强化的建议，基于分析，将缩小范围，并允许流量从 140.23.30.10/29 - 这是一个较窄的 IP 范围，并拒绝所有其他流量到该端口。

>[!TIP]
> 自适应网络强化建议仅在以下特定端口（对于 UDP 和 TCP）上支持：13、 17、 19， 22， 23， 53， 69， 81， 111， 119， 123， 135， 135， 137， 138， 139， 161， 162， 389， 445， 512， 514， 593， 636， 873， 1433， 1434， 1900， 2049， 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![网络强化视图](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>查看自适应网络强化警报和规则

1. 在安全中心中，选择**网络** -> **自适应网络强化**。 网络 VM 列在三个单独的选项卡下：
   * **不正常资源**：当前具有通过运行自适应网络强化算法触发的建议和警报的 VM。 
   * **健康资源**：没有警报和建议的 VM。
   * **未扫描资源**：自适应网络强化算法由于以下原因之一无法运行的 VM：
      * **VM 是经典 VM：** 仅支持 Azure 资源管理器 VM。
      * **数据不足**：为了生成准确的流量强化建议，安全中心至少需要 30 天的流量数据。
      * **VM 不受 ASC 标准的保护**：只有设置为安全中心标准定价层的 VM 才有资格享受此功能。

     ![不健康的资源](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. 从 **"不正常资源**"选项卡中，选择一个 VM 以查看其警报和要应用的建议强化规则。

    ![强化警报](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>查看并应用自适应网络强化建议规则

1. 从 **"不正常的资源**"选项卡中选择 VM。 列出了警报和建议的强化规则。

     ![硬化规则](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > "**规则"** 选项卡列出了自适应网络强化建议您添加的规则。 "**警报"** 选项卡列出了由于流量而生成的警报，这些警报流到资源，而资源不在建议规则中允许的 IP 范围内。

2. 如果要更改规则的某些参数，可以对其进行修改，如[修改规则 中](#modify-rule)所述。
   > [!NOTE]
   > 您还可以[删除](#delete-rule)或[添加](#add-rule)规则。

3. 选择要在 NSG 上应用的规则，然后单击 **"强制**"。

      > [!NOTE]
      > 强制规则将添加到保护 VM 的 NSG 中。 （VM 可以受与其 NIC 关联的 NSG 或 VM 所在的子网或两者同时受到保护）

    ![强制执行规则](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>修改规则<a name ="modify-rule"> </a>

您可能需要修改已建议的规则的参数。 例如，您可能希望更改建议的 IP 范围。

修改自适应网络强化规则的一些重要准则：

* 只能修改"允许"规则的参数。 
* 您不能将"允许"规则更改为"拒绝"规则。 

  > [!NOTE]
  > 创建和修改"拒绝"规则直接在 NSG 上完成。 有关详细信息，请参阅[创建、更改或删除网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

* **拒绝所有流量**规则是此处列出的"拒绝"规则的唯一类型，无法修改。 但是，您可以删除它（请参阅[删除规则](#delete-rule)）。
  > [!NOTE]
  > 当由于运行算法的结果，安全中心不根据现有的 NSG 配置标识应允许的流量时，建议使用 **"拒绝所有流量**规则"。 因此，建议的规则是拒绝到指定端口的所有流量。 这种类型的规则的名称显示为"*系统生成*"。 执行此规则后，其在 NSG 中的实际名称将是一个由协议、流量方向、"DENY"和随机数组成的字符串。

*要修改自适应网络强化规则，*

1. 要修改规则的某些参数，**请在"规则"** 选项卡中单击规则行末尾的三个点 （...），然后单击"**编辑**"。

   ![编辑规则](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. 在 **"编辑规则"** 窗口中，更新要更改的详细信息，然后单击"**保存**"。

   > [!NOTE]
   > 单击 **"保存"** 后，已成功更改规则。 *但是，您尚未将其应用于 NSG。* 要应用它，您必须选择列表中的规则，然后单击 **"强制"（** 如下一步所述）。

   ![编辑规则](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. 要应用更新的规则，请从列表中选择更新的规则，然后单击 **"强制**"。

    ![强制执行规则](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>添加新规则<a name ="add-rule"> </a>

您可以添加安全中心不建议的"允许"规则。

> [!NOTE]
> 此处只能添加"允许"规则。 如果要添加"拒绝"规则，可以直接在 NSG 上添加。 有关详细信息，请参阅[创建、更改或删除网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

*要添加自适应网络强化规则，*

1. 单击 **"添加规则**"（位于左上角）。

   ![添加规则](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. 在 **"新建规则"** 窗口中，输入详细信息，然后单击"**添加**"。

   > [!NOTE]
   > 单击 **"添加**"后，您已成功添加规则，并且该规则将与其他建议的规则一起列出。 但是，您尚未将其应用于 NSG。 要激活它，您必须选择列表中的规则，然后单击 **"强制"（** 如下一步所述）。

3. 要应用新规则，请从列表中选择新规则，然后单击 **"强制**"。

    ![强制执行规则](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>删除规则<a name ="delete-rule"> </a>

如有必要，可以删除当前会话的建议规则。 例如，您可以确定应用建议的规则可能会阻止合法流量。

*要删除当前会话的自适应网络强化规则，请进行：*

1. 在 **"规则"** 选项卡中，单击规则行末尾的三个点 （...），然后单击 **"删除**"。  

    ![硬化规则](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)