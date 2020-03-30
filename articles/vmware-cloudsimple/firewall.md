---
title: Azure VMware 解决方案（按云简单 ） - 设置防火墙表和规则
description: 介绍如何设置私有云防火墙表和规则以限制子网和 VLAN 上的流量。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 986f4b0da7254ebac3725a704f32af785c72fbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244662"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>为私有云设置防火墙表和规则

防火墙表和相关规则允许您指定适用于特定子网和 VLAN 的流量限制。

* 子网可以与一个防火墙表关联。
* 防火墙表可以与多个子网关联。

## <a name="add-a-new-firewall-table"></a>添加新的防火墙表

1. [访问云简单门户](access-cloudsimple-portal.md)，并在侧菜单上选择 **"网络**"。
2. 选择**防火墙表**。
3. 选择 **"创建防火墙表**"。

    ![VLAN/子网页面](media/firewall-tables-page.png)

4. 输入表的名称。
5. 列出了表的默认规则。 单击 **"创建新规则**"以创建其他规则。 有关详细信息，请参阅以下过程。
6. 单击"**完成"** 以保存防火墙表。

> [!IMPORTANT]
> 每个私有云最多可以创建两个防火墙表。

## <a name="firewall-rules"></a>防火墙规则

防火墙规则确定防火墙如何处理特定类型的流量。 所选防火墙表**的"规则**"选项卡列出了所有关联的规则。

![防火墙规则表](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>创建防火墙规则

1. 以以下任一方式显示创建防火墙规则的设置：
    * 创建防火墙表时单击"**添加规则**"。
    * 在 **"网络>防火墙表**"页上选择特定的防火墙表，然后单击"**创建新的防火墙规则**"。
2. 设置规则如下：
    * **名称**。 为规则指定名称。
    * **优先级**。 为规则分配优先级。 首先执行编号较低的规则。
    * **流量类型**。 选择规则是专用云、Internet 还是 VPN 流量（无状态）还是公共 IP 地址（有状态）。
    * **协议**。 选择规则（TCP、UDP 或任何协议）所涵盖的协议。
    * **方向**. 选择规则是针对入站流量还是出站流量。 您必须为入站和出站流量定义单独的规则。
    * **行动**。 选择规则匹配（允许或拒绝）时要执行的操作。
    * **源**. 指定规则涵盖的源（CIDR 块、内部源或任何源）。
    * **源端口范围**. 指定受规则约束的端口范围。
    * **方向**. 选择入站或出站。
    * **目的地**. 指定规则覆盖的目标（CIDR 块、内部或任何源）。
    * **源端口范围**. 指定受规则约束的端口范围。

    ![防火墙表添加规则](media/firewall-rule-create.png)

3. 单击 **"完成"** 以保存规则并将其添加到防火墙表的规则列表中。

> [!IMPORTANT]
> 每个防火墙表最多可以有 10 个入站规则和 20 个出站规则。 这些限制可以通过[联系支持部门](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来增加。

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>附加 VLAN/子网

定义防火墙表后，可以指定受表中规则约束的子网。

1. 在 **"网络** > **防火墙表"** 页上，选择防火墙表。
2. 打开**附加的 VLAN/子网**选项卡。
3. 单击 **"附加到 VLAN/子网**"。
4. 选择私有云和 VLAN。 将显示关联的子网名称和 CIDR 块。
5. 单击 **“提交”**。
