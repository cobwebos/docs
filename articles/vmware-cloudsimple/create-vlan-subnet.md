---
title: 创建 Vlan/子网
description: Azure VMware 解决方案（AVS）-介绍如何创建和管理你的 AVS 私有云的 Vlan/子网，然后应用防火墙规则。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 249c48500dbcd75f62f856b3345b3a2c02502d1a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024766"
---
# <a name="create-and-manage-vlanssubnets-for-your-avs-private-clouds"></a>创建和管理你的 AVS 私有云的 Vlan/子网

打开 "网络" 页上的 "Vlan/子网" 选项卡，创建和管理你的 AVS 私有云的 Vlan/子网。 创建 VLAN/子网之后，可应用防火墙规则。

## <a name="create-a-vlansubnet"></a>创建 VLAN/子网

1. [访问 AVS 门户](access-cloudsimple-portal.md)，并在侧菜单中选择 "**网络**"。
2. 选择 " **vlan/子网**"。
3. 单击 "**创建 VLAN/子网**"。

    ![VLAN/子网页](media/vlan-subnet-page.png)

4. 为新的 VLAN/子网选择 AVS 私有云。
5. 输入 VLAN ID。
6. 输入子网名称。
7. 若要启用 VLAN （子网）上的路由，请指定子网 CIDR 范围。 请确保 CIDR 范围不与任何本地子网、Azure 子网或网关子网重叠。
8. 单击“提交”。

    ![创建 VLAN/子网](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> 每个私有云的配额为30个 Vlan。 可以通过[联系支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来增加这些限制。

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>使用 VLAN 信息在 vSphere 中设置分布式端口组

若要在 vSphere 中创建分布式端口组，请按照 VMware 主题 "添加分布式端口组" 中的说明进行操作，请参阅<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">VSphere 网络指南</a>。 设置分布式端口组时，请从 AVS 配置中提供 VLAN 信息。

![分布式端口组](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>选择防火墙表

防火墙表和关联规则在 "**网络 > 防火墙表**" 页上定义。 若要选择要应用于 AVS 私有云的 VLAN/子网的防火墙表，请在 " **vlan/子网**" 页上选择 "vlan/子网" 单击**防火墙表附件**。 有关设置防火墙表和定义规则的说明，请参阅[防火墙表](firewall.md)。

![防火墙表链接](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> 子网可与一个防火墙表相关联。 防火墙表可以与多个子网相关联。

## <a name="edit-a-vlansubnet"></a>编辑 VLAN/子网

若要编辑 VLAN/子网的设置，请在 " **vlan/子网**" 页上选择该设置，然后单击 "**编辑**" 图标。 进行更改，然后单击 " **Submet**"。

## <a name="delete-a-vlansubnet"></a>删除 VLAN/子网

若要删除 VLAN/子网，请在 " **vlan/子网**" 页上选择它，然后单击 "**删除**" 图标。 单击 "**删除**" 进行确认。
