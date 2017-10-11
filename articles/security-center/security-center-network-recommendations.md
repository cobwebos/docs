---
title: "保护 Azure 安全中心的网络 | Microsoft Docs"
description: "本文档说明在 Azure 安全中心中的建议，帮助你保护你的 Azure 网络，并保持符合安全策略。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 00b715507a7c3a4d784b800e7bf0c700f6ea6ff1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="protecting-your-network-in-azure-security-center"></a>保护 Azure 安全中心中的网络
Azure 安全中心可分析 Azure 资源的安全状态。 在安全中心识别潜在的安全漏洞时，它会创建一些建议，这些建议会指导完成配置所需控件的过程。  适用于 Azure 资源类型的建议：虚拟机 (VM)、网络、SQL，以及应用程序。

本文介绍适用于网络的建议。  网络建议以下一代防火墙、网络安全组、配置入站流量规则和其他为中心。  使用下表作为参考，以便了解可用的网络建议，以及应用建议后，每个建议的做法。

## <a name="available-network-recommendations"></a>可用的网络建议
| 建议 | 说明 |
| --- | --- |
| [添加下一代防火墙](security-center-add-next-generation-firewall.md) |建议从 Microsoft 合作伙伴添加下一代防火墙 (NGFW)，以增强安全保护。 |
| [仅通过 NGFW 路由流量](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |建议配置通过 NGFW 强制将流量入站到 VM 的网络安全组 (NSG) 规则。 |
| [在子网或虚拟机上启用网络安全组](security-center-enable-network-security-groups.md) |建议在子网或 VM 上启用 NSG。 |
| [通过面向 Internet 的终结点限制访问](security-center-restrict-access-through-internet-facing-endpoints.md) |建议为 NSG 配置入站流量规则。 |

## <a name="see-also"></a>另请参阅
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：

* [保护 Azure 安全中心中的虚拟机](security-center-virtual-machine-recommendations.md)
* [保护 Azure 安全中心中的应用程序](security-center-application-recommendations.md)
* [保护 Azure 安全中心中的 Azure SQL 服务](security-center-sql-service-recommendations.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [Azure 安全中心常见问题](security-center-faq.md) - 查找有关使用服务的常见问题。
