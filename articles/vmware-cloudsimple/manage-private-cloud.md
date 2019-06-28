---
title: 管理 Azure 的 VMware 解决方案通过 CloudSimple 私有云
description: 介绍可用于管理 CloudSimple 私有云资源和活动的功能
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 05a2fb451b3acce1011c1d5f4cf17f0a865d57d0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332882"
---
# <a name="manage-private-cloud-resources-and-activity"></a>管理私有云资源和活动

私有云通过 CloudSimple 门户进行管理。  检查状态、 可用资源、 私有云以及其他设置从 CloudSimple 门户上的活动。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

访问[CloudSimple 门户](access-cloudsimple-portal.md)。

## <a name="view-the-list-of-private-clouds"></a>查看私有云的列表

**私有云**选项卡上**资源**页将列出订阅中的所有私有云。 信息包括名称、 数 vSphere 群集，位置，当前状态的私有云和资源信息。

![私有云页面](media/manage-private-cloud.png)

选择的其他信息和操作私有云。

## <a name="private-cloud-summary"></a>私有云摘要

查看所选的私有云的综合性摘要。  摘要页包括私有云上部署的 DNS 服务器。  您可以将 DNS 设置的本地 DNS 服务器转发给你的私有云 DNS 服务器。  DNS 转发的详细信息，请参阅[配置的 DNS 进行名称解析从本地私有云 vcenter](https://docs.azure.cloudsimple.com/on-premises-dns-setup/)。

![私有云摘要](media/private-cloud-summary.png)

### <a name="available-actions"></a>可用操作

* [启动 vSphere 客户端](https://docs.azure.cloudsimple.com/vsphere-access/)。 为此私有云访问 vCenter。
* [购买节点](create-nodes.md)。 将节点添加到此私有云。
* [展开](expand-private-cloud.md)。 将节点添加到此私有云。
* **刷新**。 更新此页上的信息。
* **删除**。 可以在任何时候删除私有云。 **在删除之前，请确保已备份所有系统和数据。** 删除私有云会删除所有虚拟机、 vCenter 配置和数据。 单击**删除**所选的私有云的摘要部分中。 删除以下安全且高度符合擦除过程中清除所有私有云数据。
* [更改 vSphere 权限](escalate-private-cloud-privileges.md)。  在此私有云上的特权提升。

## <a name="private-cloud-vlanssubnets"></a>私有云 VLAN/子网

查看所选的私有云定义 Vlan/子网的列表。  此列表包括 Vlan/子网创建时已创建了私有云的管理。

![私有云-Vlan/子网](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>可用操作

* [添加子网VLAN/](https://docs.azure.cloudsimple.com/create-vlan-subnet/)。 将 VLAN/子集添加到此私有云。

选择以下操作 VLAN/子网
* [附加防火墙表](https://docs.azure.cloudsimple.com/firewall/)。 将防火墙表附加到此私有云。
* **编辑**
* **删除**（仅用户定义的 Vlan/子网）

## <a name="private-cloud-activity"></a>私有云活动

查看所选的私有云的以下信息。  活动信息是所选的私有云的所有活动的筛选的列表。  此页显示最多 25 个最新活动。

* 最近的警报
* 最新事件
* 最新任务
* 最新的审核

![私有云-活动](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>云机架

云机架是您的私有云的构建基块。 每个机架提供容量的单位。 CloudSimple 会自动配置基于所选内容创建或扩展私有云时的云机架。  查看云机架的完整列表，包括私有云的每个分配给。

![私有云-云机架](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere 管理网络

VMware 管理资源和私有云当前配置的虚拟机的列表。 信息包括软件版本、 完全限定的域名 (FQDN) 和资源的 IP 地址。

![私有云-vSphere 管理网络](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 详细了解[私有云](cloudsimple-private-cloud.md)