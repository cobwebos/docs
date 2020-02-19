---
title: Azure 防火墙中央管理
description: 了解 Azure 防火墙管理器中央管理
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444545"
---
# <a name="azure-firewall-central-management"></a>Azure 防火墙中央管理

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

如果你管理多个防火墙，你会知道不断变化的防火墙规则使它们难以保持同步。中心 IT 团队需要一种方法来定义基本防火墙策略并跨多个业务单位实施这些策略。 同时，DevOps 团队想要创建自己的本地派生防火墙策略，以获得更好的灵活性。

Azure 防火墙管理器预览版可帮助解决这些问题。


## <a name="azure-firewall-manager-preview"></a>Azure 防火墙管理器预览

Azure 防火墙管理器预览版是一项网络安全管理服务，为基于云的安全外围网络提供中心安全策略和路由管理。 企业 IT 团队可通过它来集中定义跨多个 Azure 防火墙实例进行的流量筛选的网络和应用程序级别规则。 你可以跨不同的 Azure 区域以及中心和辐射型体系结构中的订阅来实现流量管理和保护。 它还通过在组织中实现的派生本地防火墙安全策略，为 DevOps 提供更好的灵活性。

### <a name="firewall-policy"></a>防火墙策略

防火墙策略是一种 Azure 资源，其中包含 NAT、网络和应用程序规则集合以及威胁情报设置。 这是一项全局资源，可跨*安全虚拟中心*和*中心虚拟网络*中的多个 Azure 防火墙实例使用。 可以从头开始创建新的策略，也可以从现有策略继承。 继承允许 DevOps 在组织规定的基本策略之上创建本地防火墙策略。 策略跨区域和订阅工作。
 
可以通过 Azure 防火墙管理器创建防火墙策略和关联。 不过，还可以使用 REST API、模板、Azure PowerShell 和 CLI 来创建和管理策略。 创建策略后，可以将其与虚拟 WAN 中心中的防火墙关联，使其成为虚拟网络中*受保护的虚拟中心*和/或防火墙，使其成为*中心虚拟网络*。

### <a name="pricing"></a>定价

策略根据防火墙关联进行计费。 具有零个或一个防火墙关联的策略是免费的。 具有多个防火墙关联的策略按固定费率计费。 有关详细信息，请参阅[Azure 防火墙管理器定价](https://azure.microsoft.com/pricing/details/firewall-manager/)。

## <a name="azure-firewall-management-partners"></a>Azure 防火墙管理合作伙伴

以下第三方解决方案支持使用标准 Azure REST Api 进行 Azure 防火墙集中管理。 其中每个解决方案都有其自己的独特特性和功能：

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security 监护人](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>后续步骤

有关 Azure 防火墙管理器预览版的详细信息，请参阅[什么是 Azure 防火墙管理器预览版？](../firewall-manager/overview.md)