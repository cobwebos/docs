---
title: 管理 Azure VMware 解决方案（AVS）私有云
description: 介绍可用于管理 AVS 私有云资源和活动的功能
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47bf2251f71204b99245c1a9d55ef87157c41dd8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014821"
---
# <a name="manage-avs-private-cloud-resources-and-activities"></a>管理 AVS 私有云资源和活动

从 AVS 门户管理 AVS 私有云。 在 avs 门户上检查 "状态"、"可用资源"、"AVS 私有云上的活动" 和 "其他设置"。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-avs-portal"></a>访问 AVS 门户

访问[AVS 门户](access-cloudsimple-portal.md)。

## <a name="view-the-list-of-avs-private-clouds"></a>查看 AVS 私有云列表

"**资源**" 页上的 " **AVS 私有云**" 选项卡列出了订阅中的所有 AVS 私有云。 信息包括：名称、vSphere 群集的数量、位置、AVS 私有云的当前状态和资源信息。

![AVS 私有云页面](media/manage-private-cloud.png)

选择一个 AVS 私有云以获取其他信息和操作。

## <a name="avs-private-cloud-summary"></a>AVS 私有云摘要

查看所选的 AVS 私有云的完整摘要。 "摘要" 页包括在 AVS 私有云上部署的 DNS 服务器。 可以设置从本地 DNS 服务器到 AVS 私有云 DNS 服务器的 DNS 转发。 有关 DNS 转发的详细信息，请参阅为[本地的 AVS 私有云 VCenter 配置 DNS 进行名称解析](https://docs.azure.cloudsimple.com/on-premises-dns-setup/)。

![AVS 私有云摘要](media/private-cloud-summary.png)

### <a name="available-actions"></a>可用操作

* [启动 vSphere 客户端](https://docs.azure.cloudsimple.com/vsphere-access/)。 访问此 AVS 私有云的 vCenter。
* [购买节点](create-nodes.md)。 向此 AVS 私有云添加节点。
* [展开](expand-private-cloud.md)。 向此 AVS 私有云添加节点。
* **刷新**。 更新此页上的信息。
* **删除**。 你可以随时删除 AVS 私有云。 **删除之前，请确保已备份所有系统和数据。** 删除 AVS 私有云会删除所有 Vm、vCenter 配置和数据。 对于所选的 AVS 私有云，单击 "摘要" 部分中的 "**删除**"。 删除后，所有的 AVS 私有云数据都将在安全、高度兼容的擦除过程中清除。
* [更改 vSphere 权限](escalate-private-cloud-privileges.md)。 升级此 AVS 私有云上的权限。

## <a name="avs-private-cloud-vlanssubnets"></a>AVS 私有云 VLAN/子网

查看所选的 AVS 私有云的已定义 Vlan/子网列表。 此列表包括创建 AVS 私有云时创建的管理 Vlan/子网。

![AVS 私有云-Vlan/子网](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>可用操作

* [添加 vlan/子网](https://docs.azure.cloudsimple.com/create-vlan-subnet/)。 向此 AVS 私有云添加 VLAN/子集。

为以下操作选择 VLAN/子网
* [附加防火墙表](https://docs.azure.cloudsimple.com/firewall/)。 将防火墙表附加到此 AVS 私有云。
* **编辑**
* **删除**（仅用户定义的 Vlan/子网）

## <a name="avs-private-cloud-activity"></a>AVS 私有云活动

查看所选的 AVS 私有云的以下信息。 活动信息是所选的 AVS 私有云的所有活动的筛选列表。 此页显示最多25个最近的活动。

* 最近的警报
* 最近的事件
* 最近的任务
* 最近审核

![AVS 私有云-活动](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>云机架

云架是 AVS 私有云的构建基块。 每个机架都提供容量单元。 在创建或扩展 AVS 私有云时，AVS 会根据你的选择自动配置云架。 查看云机架的完整列表，包括每个分配给的 AVS 私有云。

![AVS 私有云-云机架](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere 管理网络

当前在 AVS 私有云上配置的 VMware 管理资源和虚拟机的列表。 信息包括软件版本、完全限定的域名（FQDN）和资源的 IP 地址。

![AVS 私有云-vSphere 管理网络](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>后续步骤

* [在 Azure 上使用 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 了解有关[AVS 私有云](cloudsimple-private-cloud.md)的详细信息