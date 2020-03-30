---
title: 按云简单私有云管理 Azure VMware 解决方案
description: 描述可用于管理云简单私有云资源和活动的功能
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 620c0226d3aca907352658ebbe1b94c7673d91cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014821"
---
# <a name="manage-private-cloud-resources-and-activity"></a>管理私有云资源和活动

私有云由云简单门户管理。  从 CloudSimple 门户检查状态、可用资源、私有云上的活动和其他设置。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 中的[https://portal.azure.com](https://portal.azure.com)Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

访问[云简单门户](access-cloudsimple-portal.md)。

## <a name="view-the-list-of-private-clouds"></a>查看私有云列表

**"资源**"页上的 **"私有云**"选项卡列出了订阅中的所有私有云。 信息包括名称、vSphere 群集的数量、位置、私有云的当前状态以及资源信息。

![私有云页面](media/manage-private-cloud.png)

选择私有云以获取其他信息和操作。

## <a name="private-cloud-summary"></a>私有云摘要

查看所选私有云的全面摘要。  摘要页面包括部署在私有云上的 DNS 服务器。  您可以将 DNS 转发从本地 DNS 服务器连接到私有云 DNS 服务器。  有关 DNS 转发的详细信息，请参阅[配置 DNS 以了解本地私有云 vCenter 的名称解析](https://docs.azure.cloudsimple.com/on-premises-dns-setup/)。

![私有云摘要](media/private-cloud-summary.png)

### <a name="available-actions"></a>可用操作

* [启动 vSphere 客户端](https://docs.azure.cloudsimple.com/vsphere-access/)。 访问此私有云的 vCenter。
* [购买节点](create-nodes.md)。 向此私有云添加节点。
* [展开](expand-private-cloud.md)。 向此私有云添加节点。
* **刷新**。 更新此页面上的信息。
* **删除**。 您可以随时删除私有云。 **在删除之前，请确保已备份所有系统和数据。** 删除私有云将删除所有 VM、vCenter 配置和数据。 单击所选私有云的摘要部分中的 **"删除**"。 删除后，所有私有云数据在安全、高度合规的擦除过程中被擦除。
* [更改 vSphere 权限](escalate-private-cloud-privileges.md)。  上报您在此私有云上的权限。

## <a name="private-cloud-vlanssubnets"></a>私有云 VLANS/子网

查看所选私有云的已定义 VLAN/子网的列表。  该列表包括创建私有云时创建的管理 VLAN/子网。

![私有云 - VLAN/子网](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>可用操作

* [添加 VLANS/子网](https://docs.azure.cloudsimple.com/create-vlan-subnet/)。 向此私有云添加 VLAN/子集。

选择 VLAN/子网以进行以下操作
* [附加防火墙表](https://docs.azure.cloudsimple.com/firewall/)。 将防火墙表附加到此私有云。
* **编辑**
* **删除**（仅限用户定义的 VLAN/子网络）

## <a name="private-cloud-activity"></a>私有云活动

查看所选私有云的以下信息。  活动信息是所选私有云所有活动的筛选列表。  此页面显示多达 25 个最近的活动。

* 最近的警报
* 最近的事件
* 最近的任务
* 最近的审计

![私有云 - 活动](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>云机架

云机架是私有云的构建基块。 每个机架提供一个容量单元。 CloudSimple 在创建或扩展私有云时，根据您的选择自动配置云机架。  查看云机架的完整列表，包括每个云分配给的私有云。

![私有云 - 云机架](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere 管理网络

当前在私有云上配置的 VMware 管理资源和虚拟机的列表。 信息包括软件版本、完全限定的域名 （FQDN） 和资源的 IP 地址。

![私有云 - vSphere 管理网络](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 了解有关[私有云](cloudsimple-private-cloud.md)的更多