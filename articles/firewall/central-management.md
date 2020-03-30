---
title: Azure 防火墙集中管理
description: 了解 Azure 防火墙管理器集中管理
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444545"
---
# <a name="azure-firewall-central-management"></a>Azure 防火墙集中管理

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

如果您管理多个防火墙，您知道不断更改的防火墙规则会使它们保持同步变得困难。中央 IT 团队需要一种方法来定义基本防火墙策略，并在多个业务部门中强制实施这些策略。 同时，DevOps 团队希望创建自己的本地派生防火墙策略，以取得更好的敏捷性。

Azure 防火墙管理器预览可帮助解决这些问题。


## <a name="azure-firewall-manager-preview"></a>Azure 防火墙管理器预览

Azure 防火墙管理器预览版是一种网络安全管理服务，为基于云的安全外围提供中央安全策略和路由管理。 它使企业 IT 团队能够轻松集中定义网络和应用程序级别规则，以便跨多个 Azure 防火墙实例进行流量筛选。 您可以在集线器和分支体系结构中跨越不同的 Azure 区域和订阅，以便进行流量治理和保护。 它还通过跨组织实现的派生本地防火墙安全策略为 DevOps 提供更好的敏捷性。

### <a name="firewall-policy"></a>防火墙策略

防火墙策略是包含 NAT、网络和应用程序规则集合以及威胁智能设置的 Azure 资源。 它是一个全局资源，可用于*安全虚拟中心*和*集线器虚拟网络中*的多个 Azure 防火墙实例。 可以从头开始创建新策略，或者从现有策略继承策略。 DevOps 可以通过继承在组织规定的基本策略之上创建本地防火墙策略。 策略跨区域和订阅工作。
 
您可以创建防火墙策略和与 Azure 防火墙管理器的关联。 但是，您还可以使用 REST API、模板、Azure PowerShell 和 CLI 创建和管理策略。 创建策略后，可以将其与虚拟 WAN 集线器中的防火墙关联，使其成为*安全虚拟集线器*和/或虚拟网络中的防火墙，使其成为*集线器虚拟网络*。

### <a name="pricing"></a>定价

策略根据防火墙关联计费。 存在零个或一个防火墙关联的策略是免费的。 存在多个防火墙关联的策略按固定费率计费。 有关详细信息，请参阅 [Azure 防火墙管理器定价](https://azure.microsoft.com/pricing/details/firewall-manager/)。

## <a name="azure-firewall-management-partners"></a>Azure 防火墙管理合作伙伴

以下领先的第三方解决方案支持使用标准 Azure REST API 进行 Azure 防火墙集中管理。 每个解决方案都有其独特的特点和特点：

- [阿尔戈塞云流](https://www.algosec.com/azure/) 
- [梭子鱼云安全卫士](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [图芬·奥卡](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>后续步骤

有关 Azure 防火墙管理器预览的详细信息，请参阅[什么是 Azure 防火墙管理器预览？](../firewall-manager/overview.md)