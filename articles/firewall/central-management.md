---
title: Azure 防火墙集中管理
description: 了解 Azure 防火墙管理器集中管理
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: victorh
ms.openlocfilehash: 23a7682d8a64de57db4ff9ae785ada90d4a06944
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87084664"
---
# <a name="azure-firewall-central-management"></a>Azure 防火墙集中管理

如果管理多个防火墙，你会发现，不断变化的防火墙规则将使其难以保持同步。中央 IT 团队需要一种方法来定义基本防火墙策略，并跨多个业务部门实施这些策略。 同时，DevOps 团队希望创建自己的本地派生防火墙策略，以提高灵活性。

Azure 防火墙管理器可以帮助解决这些问题。


## <a name="azure-firewall-manager"></a>Azure 防火墙管理器

Azure 防火墙管理器是一种网络安全管理服务，可为基于云的安全外围提供安全策略和路由集中管理。 借助它，企业 IT 团队可以轻松地集中定义跨多个 Azure 防火墙实例进行流量筛选的网络和应用程序级规则。 你可以跨中心和分支体系结构中的不同 Azure 区域和订阅来调控和保护流量。 它还通过跨组织实施的派生本地防火墙安全策略，为 DevOps 提供更好的灵活性。

### <a name="firewall-policy"></a>防火墙策略

防火墙策略是包含 NAT、网络和应用程序规则集合以及威胁情报设置的 Azure 资源。 它是一种全局资源，可跨安全虚拟中心和中心虚拟网络中的多个 Azure 防火墙实例使用 。 可以从头开始创建新策略，或者从现有策略继承策略。 DevOps 可以通过继承在组织规定的基本策略之上创建本地防火墙策略。 策略跨区域和订阅工作。
 
你可以使用 Azure 防火墙管理器创建防火墙策略和关联项。 不过，你也可以使用 REST API、模板、Azure PowerShell 和 CLI 来创建和管理策略。 创建策略后，可以将其与虚拟 WAN 中心的防火墙关联，使其成为安全虚拟中心，以及/或者与虚拟网络中的防火墙关联，使其成为中心虚拟网络。

### <a name="pricing"></a>定价

策略根据防火墙关联计费。 存在零个或一个防火墙关联的策略是免费的。 存在多个防火墙关联的策略按固定费率计费。 有关详细信息，请参阅 [Azure 防火墙管理器定价](https://azure.microsoft.com/pricing/details/firewall-manager/)。

## <a name="azure-firewall-management-partners"></a>Azure 防火墙管理合作伙伴

以下行业领先的第三方解决方案支持使用标准 Azure REST API 进行 Azure 防火墙集中管理。 其中每个解决方案都有其独特的特性和功能：

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security Guardian](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>后续步骤

有关 Azure 防火墙管理器的详细信息，请参阅[什么是 Azure 防火墙管理器？](../firewall-manager/overview.md)