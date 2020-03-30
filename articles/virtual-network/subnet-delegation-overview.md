---
title: Azure 虚拟网络中的子网委托是什么？
description: 了解 Azure 虚拟网络中的子网委托
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2019
ms.author: kumud
ms.openlocfilehash: b33ff808b802b6848e2d5debaf515a73bf21a1bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74281332"
---
# <a name="what-is-subnet-delegation"></a>什么是子网委托？

使用子网委托可为需要注入到虚拟网络的所选 Azure PaaS 服务指定特定的子网。 子网委托为客户提供完全控制权，使他们能够管理 Azure 服务与虚拟网络的集成。

将某个子网委托给 Azure 服务即表示你允许该服务为该子网建立一些基本的网络配置规则，这有助于该 Azure 服务稳定地操作其实例。 因此，Azure 服务可以建立一些部署前或部署后的条件，例如：
- 在共享子网或专用子网中部署服务。
- 在部署后将一组网络意向策略添加到服务，使服务能够正常运行。

##  <a name="advantages-of-subnet-delegation"></a>子网委托的优势

将子网委托给特定的服务可提供以下优势：

- 有助于为一个或多个 Azure 服务指定子网，并根据要求管理子网中的实例。 例如，虚拟网络所有者可为委托的子网定义以下设置，以便更好地管理资源和访问权限，如下所述：
    - 对网络安全组定义网络筛选流量策略。
    - 对用户定义的路由定义路由策略。
    - 对服务终结点配置定义服务集成。
- 以网络意向策略的形式定义已注入服务的部署前提条件，帮助这些服务更好地与虚拟网络集成。 这会确保在执行 PUT 时，可以阻止可能会影响已注入服务正常运行的任何操作。


## <a name="who-can-delegate"></a>谁可以委托子网？
子网委托是虚拟网络所有者针对特定 Azure 服务指定某个子网而需要执行的操作。 而 Azure 服务又会将实例部署到此子网，供客户工作负荷使用。

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>子网委托对子网的影响
每个 Azure 服务将定义自身的部署模型，在此模型中，这些服务可以定义出于注入目的，它们支持或不支持委托子网中的哪些属性，如下所述：
- 支持同一子网中与其他 Azure 服务或 VM/虚拟机规模集共享的子网，或仅支持专用子网（仅包含此服务自身的实例）。
- 支持与委托子网关联的 NSG。
- 支持还可以与任何其他子网关联的委托子网的关联 NSG。
- 允许路由表与委托子网的关联。
- 允许与任何其他子网关联的委托子网的关联路由表。
- 指定委托子网中的最小 IP 地址数。
- 指定委托子网中来自专用 IP 地址空间（10.0.0.0/8、192.168.0.0/16、172.16.0.0/12）的 IP 地址空间。
- 指定自定义 DNS 配置包含 Azure DNS 条目。

注入的服务也可以添加自身的策略，如下所述：
- **安全策略**：收集给定服务正常工作所需的安全规则。
- **路由策略**：收集给定服务正常工作所需的路由。

## <a name="what-subnet-delegation-does-not-do"></a>子网委托无法实现的目的

要注入到委托子网中的 Azure 服务仍有一组基本属性可用于非委托子网，例如：
-  Azure 服务可将实例注入到客户子网，但不能影响现有的工作负荷。
-  这些服务应用的策略或路由非常灵活，可由客户替代。

## <a name="next-steps"></a>后续步骤

- [委托子网](manage-subnet-delegation.md)
