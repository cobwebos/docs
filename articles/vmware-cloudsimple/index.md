---
title: Azure VMware 解决方案 (AVS)
description: Azure VMware 解决方案 (AVS) 的文档门户。
author: sharaths-cs
ms.author: b-mashar
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d81ea6778f3ba31d72c34334b1439994b076647c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025208"
---
# <a name="azure-vmware-solution-by-avs"></a>Azure VMware Solution by AVS

欢迎使用一站式门户来获得有关 Azure VMware Solution by AVS 的帮助。
在该文档站点中，可以了解以下主题：

## <a name="overview"></a>概述

详细了解 Azure VMware 解决方案 (AVS)

* 在[什么是 Azure VMware Solution by AVS](cloudsimple-vmware-solutions-overview.md) 中了解功能、优点和使用场景
* 查看[有关管理的关键概念](key-concepts.md)

## <a name="quickstart"></a>快速入门

了解如何开始使用解决方案

* 了解如何[初始化服务和购买容量](quickstart-create-cloudsimple-service.md)
* 在[配置 AVS 私有云环境](quickstart-create-private-cloud.md)中了解如何创建新的 VMware 环境
* 查看文章[在 Azure 上使用 VMware VM](quickstart-create-vmware-virtual-machine.md)，了解如何在 VMware 和 Azure 上统一管理。

## <a name="concepts"></a>概念

了解以下概念

* [AVS 服务](cloudsimple-service.md)（也称为“Azure VMware 解决方案 (AVS) - 服务”）。 此资源必须每个区域创建一次。
* 通过创建一个或多个 [AVS 节点](cloudsimple-node.md)资源，为你的环境购买容量。 这些资源也称为“Azure VMware Solution by AVS - 节点”。
* 使用 [AVS 私有云](cloudsimple-private-cloud.md)初始化并配置 VMware 环境。
* 使用 [AVS 虚拟机](cloudsimple-virtual-machines.md)（也称为“Azure VMware 解决方案 (AVS) - 虚拟机”）统一管理。
* 使用 [VLAN/子网](cloudsimple-vlans-subnets.md)设计基础网络。
* 使用[防火墙表](cloudsimple-firewall-tables.md)资源将基础网络分段并进行保护。
* 使用 [VPN 网关](cloudsimple-vpn-gateways.md)通过 WAN 安全访问 VMware 环境。
* 使用[公共 IP](cloudsimple-public-ip-address.md) 为工作负荷启用公共访问。
* 使用 [Azure 网络连接](cloudsimple-azure-network-connection.md)建立与 Azure 虚拟网络和本地网络的连接。
* 使用[帐户管理](cloudsimple-account.md)配置警报电子邮件目标。
* 使用[活动管理](cloudsimple-activity.md)屏幕查看用户和系统活动的日志。
* 了解各种 [VMware 组件](vmware-components.md)。

## <a name="tutorials"></a>教程

了解如何执行常见任务，例如：

* [创建 AVS 服务](create-cloudsimple-service.md)，在每个要部署 VMware 环境的区域中创建一次。
* 在 [AVS 门户](access-cloudsimple-portal.md)中管理核心服务功能。
* 通过[购买 AVS 节点](create-nodes.md)，为基础结构启用容量并优化计费。
* 使用 AVS 私有云管理 VMware 环境配置。 可以[创建](create-private-cloud.md)、[管理](manage-private-cloud.md)、[扩展](expand-private-cloud.md)或[收缩](shrink-private-cloud.md) AVS 私有云。
* 通过[映射 Azure 订阅](azure-subscription-mapping.md)启用统一管理。
* 使用[活动页](monitor-activity.md)监视用户和系统活动。
* 通过[创建和管理子网](create-vlan-subnet.md)为环境配置网络。
* 使用[防火墙表和规则](firewall.md)将环境分段并进行保护。
* 通过[分配公共 IP](public-ips.md) 为工作负荷启用入站 Internet 访问。
* 通过[设置 VPN](vpn-gateway.md) 启用来自内部网络或客户端工作站的连接。
* 启用来自[本地环境](on-premises-connection.md)以及去往 [Azure 虚拟网络](virtual-network-connection.md)的通信。
* 在[帐户汇总](account.md)中配置警报目标并查看购买的总容量
* 查看已访问 AVS 门户的[用户](users.md)。
* 从 Azure 门户中管理 VMware 虚拟机：
    * 在 Azure 门户中[创建虚拟机](azure-create-vm.md)。
    * [管理已创建的虚拟机](azure-manage-vm.md)。

## <a name="how-to-guides"></a>操作方法指南

这些指南介绍了一些目标解决方案，例如：

* [保护环境](private-cloud-secure.md)
* 安装第三方工具，使用[权限提升](escalate-privileges.md)在 vSphere 中启用其他用户和外部身份验证源。
* 通过[配置本地 DNS](on-premises-dns-setup.md) 来配置对各种 VMware 服务的访问权限。
* 通过[配置工作负荷 DNS 和 DHCP](dns-dhcp-setup.md)，为工作负荷启用名称和地址分配。
* 了解服务如何通过服务[更新和升级](vmware-components.md#updates-and-upgrades)来确保平台的安全性和功能。
* 通过使用[第三方备份软件（如 Veeam）](backup-workloads-veeam.md)创建示例备份体系结构，来节省备份的 TCO。
* 通过使用[第三方 KMS 加密软件](vsan-encryption.md)启用静态加密来创建安全环境。
* 通过配置 [Azure AD 标识源](azure-ad.md)，将 Azure Active Directory (Azure AD) 管理扩展到 VMware 中。
