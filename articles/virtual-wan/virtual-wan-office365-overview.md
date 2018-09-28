---
title: Azure 虚拟 WAN 中的 Office 365 控制平面
description: 了解虚拟 WAN 中的 Office 365 控制平面。
author: cherylmc
ms.service: virtual-wan
services: virtual-wan
ms.topic: article
ms.date: 9/24/2018
ms.author: cherylmc
ms.openlocfilehash: 815b91295540e93f8f0ffbc002fcf02ce1c13365
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992665"
---
# <a name="office-365-control-plane-in-virtual-wan"></a>虚拟 WAN 中的 Office 365 控制平面

使用特定 SDWAN 设备的虚拟 WAN 客户可以在 Azure 门户中为受信任的流量配置 O365 Internet 突围策略。 这样可以：
- 让 O365 流量进入靠近用户的 Microsoft 网络，提供优化的用户体验。
- 避免流量逆转和迂回，因此节省 WAN 成本。
- 按 O365 连接原则传递。

## <a name="faqs"></a>常见问题
### <a name="what-is-the-customer-benefit"></a>客户权益有哪些？
在虚拟 WAN 中使用此功能时，客户现在可以指定自己信任的可以直接进行 Internet 突围的 Office 365 流量类别。 这种受信任的 O365 流量将绕过代理，直接从用户位置路由到最近的 Microsoft POP。 这样就避免了流量逆转和迂回，因此可以提供最佳用户体验并节省 WAN 成本。 

### <a name="what-are-the-office-365-traffic-categories"></a>Office 365 流量类别有哪些？
Office 365 终结点代表网络地址和子网。 终结点可以是 URL、IP 地址或 IP 范围。 URL 可以是 FQDN，例如 *account.office.net*，也可以是通配符 URL，例如 **.office365.com*。 终结点根据其重要程度分为三类 - **优化**、**允许**、**默认**。 [此处](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles#BKMK_Categories)提供有关终结点类别的更多详细信息。

### <a name="which-office-365-traffic-category-is-recommended-by-microsoft-for-direct-internet-breakout"></a>Microsoft 建议使用哪种 Office 365 流量类别进行直接的 Internet 突围？
“优化”类别是最关键的网络终结点，要求绕过 SSL 中断和检查以及其他网络安全设备。 它应该有靠近用户的 Internet 直接出口。 这些终结点代表的 Office 365 方案对网络性能、延迟和可用性最敏感。 此类别包括一小组（数量级大约为 10）关键 URL 以及一组定义的 IP 子网，专用于核心 Office 365 工作负荷，例如 Exchange Online、SharePoint Online、Skype for Business Online 和 Microsoft Teams。 

对于 Internet 直接出口，也建议使用“允许”类别。 不过，允许网络流量的同事，可能需要忍受一定程度的网络延迟。 “优化”和“允许”类别的终结点都托管在 Microsoft 数据中心，作为 Office 365 的一部分管理。 “默认”类别可以定向到默认的 Internet 出口位置，不需要 Internet 直接出口，也不需绕过 SSL 中断和检查设备。

### <a name="how-do-i-set-my-o365-policies-via-virtual-wan"></a>如何通过虚拟 WAN 设置 O365 策略？
可以通过“虚拟 WAN” -> “设置” -> “配置”选项卡来启用策略。可以在这里指定首选的 O365 流量类别，以便进行直接的 Internet 突围。

![配置虚拟 WAN 中的 Office 365 控制平面](media/virtual-wan-office365-overview/configure-office365-control-plane.png)

### <a name="how-does-this-work"></a>它的工作原理是什么？

1.  O365 流量进入靠近用户的 Microsoft 网络，提供优化的体验。
2.  路由策略由 SDWAN 使用。 然后，它会为受信任的类别绕过安全代理，并为这些类别执行本地直接突围。
3.  避免流量逆转和迂回，节省 WAN 成本。

### <a name="which-partner-devices-support-this-via-virtual-wan"></a>哪些合作伙伴设备通过虚拟 WAN 提供此方面的支持？
目前，Citrix 通过虚拟 WAN 支持这些策略。

### <a name="what-happens-to-the-remaining-categories-of-untrusted-o365-traffic"></a>其余类别（不受信任）的 O365 流量的情况如何？
其余 O365 流量会沿着客户的默认 Internet 流量路径行进。

### <a name="what-if-i-have-already-specified-my-o365-policies-via-my-sdwan-provider"></a>如果我已通过 SDWAN 提供商指定了 O365 策略，会出现什么情况？
如果同时通过 SDWAN UX 和 Azure 虚拟 WAN 指定策略，则以在虚拟 WAN 中设置的策略为准。

## <a name="next-steps"></a>后续步骤
- 详细了解[虚拟 WAN](virtual-wan-about.md)。