---
title: Azure 安全中心中的自适应网络强化 |Microsoft Docs
description: " 了解如何启用自适应网络强化 Azure 安全中心中。 "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2019
ms.author: monhaber
ms.openlocfilehash: f35f410ddc039ee264fa1de317e152cb03f391b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241527"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure 安全中心中的自适应网络强化
了解如何在 Azure 安全中心中配置自适应网络强化。

## <a name="what-is-adaptive-network-hardening"></a>什么是自适应网络强化？
将应用[网络安全组 (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview)来筛选流量传入和传出资源，可以提高网络安全状况。 但是，仍然可以有某些情况下，在其中实际流量流过 NSG 是定义的 NSG 规则的子集。 在这些情况下，进一步提高安全状况可以通过强化 NSG 规则，根据实际流量模式。

自适应网络强化提供了建议进一步强化的 NSG 规则。 它使用机器学习算法，在实际流量，已知受信任的配置、 威胁智能和其他指标遭到破坏的因素，然后提供建议，以允许仅来自特定 IP/端口元组的流量。

例如，假设现有的 NSG 规则以允许来自 140.20.30.10/24 端口 22 上的流量。 自适应网络强化的建议，根据分析，会以缩小范围，并允许流量从 140.23.30.10/29 – 这是一个更窄的 IP 范围，并拒绝所有其他流量到该端口。

![网络强化视图](./media/security-center-adaptive-network-hardening/traffic-hardening.png)

> [!NOTE]
> 自适应网络强化建议支持对以下端口：22, 3389, 21, 23, 445, 4333, 3306, 1433, 1434, 53, 20, 5985, 5986, 5432, 139, 66, 1128

## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>查看自适应网络强化警报和规则

1. 在安全中心，选择**联网** -> **自适应网络强化**。 下面三个单独的选项卡会列出网络 Vm:
   * **不正常的资源**:当前具有的建议和运行自适应网络强化算法来触发的警报的虚拟机。 
   * **正常运行的资源**:没有警报和建议的 Vm。
   * **扫描资源**:自适应网络强化算法不能在由于以下原因之一运行的 Vm:
      * **Vm 是经典 Vm**:支持仅 Azure 资源管理器 Vm。
      * **没有足够的数据是可用**:为了生成准确强化建议的流量，安全中心需要至少 30 天的流量数据。
      * **VM 不受 ASC 标准版**:设置为安全中心的标准定价层的 Vm 有资格获得此功能。

     ![不正常的资源](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. 从**不正常的资源**选项卡上，选择要查看的警报和建议的强化规则要应用的 VM。

    ![强化警报](./media/security-center-adaptive-network-hardening/hardening-alerts.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>查看并应用自适应网络强化建议规则

1. 从**不正常的资源**选项卡上，选择一个 VM。 列出的警报和建议的强化规则。

     ![强化规则](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > **规则**选项卡列出了自适应网络强化建议您添加的规则。 **警报**选项卡列出了由于流量流到不在推荐的规则中允许的 IP 范围内的资源生成的警报。

2. 如果你想要更改某些规则的参数，则可以修改它，如中所述[修改规则](#modify-rule)。
   > [!NOTE]
   > 此外可以[删除](#delete-rule)或[添加](#add-rule)规则。

3. 选择你想要在 NSG 上应用并单击的规则**强制实施**。

      > [!NOTE]
      > 已强制实施的规则添加到保护 VM NSG(s)。 （虚拟机无法通过 NSG 关联到的 NIC、 虚拟机驻留在其中的子网和 / 或受保护）

    ![强制实施规则](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### 修改规则  <a name ="modify-rule"> </a>

您可能想要修改已建议的规则的参数。 例如，你可能想要更改建议的 IP 范围。

有关修改的自适应网络强化规则的一些重要准则：

* 可以修改"允许"规则的参数。 
* 不能更改"允许"规则以成为"拒绝"规则。 

  > [!NOTE]
  > 创建和修改"拒绝"规则操作的直接在 NSG 的更多详细信息，请参阅[创建、 更改或删除网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

* 一个**拒绝所有流量**规则是将在此处，列出的"都拒绝"规则的唯一类型，不能修改它。 但是，可以删除它 (请参阅[删除规则](#delete-rule))。
  > [!NOTE]
  > 一个**拒绝所有流量**规则建议时，因此运行该算法后，安全中心不会确定应允许，请根据现有的 NSG 配置的流量。 因此，建议的规则是拒绝所有流量流向指定的端口。 此类型的规则的名称显示为"*由系统生成*"。 强制实施此规则之后, 该 NSG 中的其实际名称将是协议、 流量方向、"拒绝"和一个随机数字组成的字符串。

*若要修改的自适应网络强化规则：*

1. 若要修改的某些规则的参数在**规则**选项卡上，单击该规则的行末尾的三个点 （...），单击**编辑**。

   ![编辑规则](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. 在中**编辑规则**窗口中，更新的详细信息，你想要更改，然后单击**保存**。

   > [!NOTE]
   > 单击后**保存**，已成功更改该规则。 *但是，你已不应用它到 NSG。* 若要将其应用，必须在列表中，选择的规则，并单击**强制实施**（如下所述，在下一步）。

   ![编辑规则](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. 若要应用已更新的规则，从列表中，选择已更新的规则，然后单击**强制实施**。

    ![强制实施规则](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### 添加新规则 <a name ="add-rule"> </a>

您可以添加一个"允许"规则，不建议的安全中心。

> [!NOTE]
> 可以在此处添加仅"允许"规则。 如果你想要添加"拒绝"规则，您可以执行操作，因此直接在 NSG 上。 有关更多详细信息，请参阅[创建、 更改或删除网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)。

*若要添加自适应网络强化规则：*

1. 单击**添加规则**（位于左上角）。

   ![添加规则](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. 在中**新的规则**窗口中，输入详细信息，然后单击**添加**。

   > [!NOTE]
   > 单击后**添加**，你已成功添加规则，并列出的其他建议的规则。 但是，你已不应用它在 NSG 上。 若要激活它，您必须在列表中，选择的规则，并单击**强制实施**（如下所述，在下一步）。

3. 若要应用新规则，请从列表中，选择新的规则，然后单击**强制实施**。

    ![强制实施规则](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### 删除规则 <a name ="delete-rule"> </a>

如有必要，可以删除建议的规则。 例如，可以确定应用建议的规则无法阻止合法的流量。

*若要删除的自适应网络强化规则：*

1. 在中**规则**选项卡上，单击该规则的行末尾的三个点 （...），单击**删除**。  

    ![强化规则](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)







 

