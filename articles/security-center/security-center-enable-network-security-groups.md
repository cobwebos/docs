---
title: 在 Azure 安全中心启用网络安全组 | Microsoft 文档
description: 本文档演示如何实现 Azure 安全中心建议**启用网络安全组**。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: fe75781629e8d6416405cc9eec9ce14e61a00b14
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301421"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>在 Azure 安全中心启用网络安全组
Azure 安全中心建议启用网络安全组 (NSG)（如果尚未启用）。 NSG 包含一系列访问控制列表 (ACL) 规则，这些规则可以允许或拒绝虚拟网络中流向 VM 实例的网络流量。 NSG 可以与子网或该子网中的各个 VM 实例相关联。 当 NSG 与某个子网相关联时，ACL 规则将应用到该子网中的所有 VM 实例。 另外，可以通过将 NSG 直接关联到单个 VM，对流向该 VM 的流量进行进一步的限制。 有关详细信息，请参阅 [What is a Network Security Group (NSG)?](../virtual-network/security-overview.md)（什么是网络安全组 (NSG)？）

如果没有启用 NSG，安全中心会显示两点建议：在子网上启用网络安全组和在虚拟机上启用网络安全组。 选择哪个级别、子网或 VM 来应用 NSG。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  这并非一份循序渐进的指南。
>
>

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡，选择在子网或在虚拟机上“启用网络安全组”。
   ![启用网络安全组][1]
2. 这会打开子网或虚拟机的“配置缺少的网络安全组”边栏选项卡，具体取决于所选的建议。 选择一个用于配置 NSG 的子网或虚拟机。

   ![配置子网的 NSG][2]

   ![配置 VM 的 NSG][3]
3. 在“选择网络安全组”边栏选项卡中，选择一个现有的 NSG 或选择“新建”以创建一个 NSG。

   ![选择网络安全组][4]

如果创建 NSG，请按照[管理网络安全组](../virtual-network/manage-network-security-group.md)中的步骤创建 NSG 并设置安全规则。

## <a name="see-also"></a>另请参阅
本文档演示如何实现安全中心建议，为子网或虚拟机“启用网络安全组”。 若要了解有关启用 NSG 的详细信息，请参阅以下内容：

* [什么是网络安全组 (NSG)？](../virtual-network/security-overview.md)
* [管理网络安全组](../virtual-network/manage-network-security-group.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) -- 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md)查找有关使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/)获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
