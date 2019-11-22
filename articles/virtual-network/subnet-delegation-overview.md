---
title: 什么是 Azure 虚拟网络中的子网委派？
description: 了解 Azure 虚拟网络中的子网委派
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281332"
---
# <a name="what-is-subnet-delegation"></a>什么是子网委派？

利用子网委派，你可以为所选的需要注入到虚拟网络的 Azure PaaS 服务指定特定子网。 子网委派为客户提供对管理 Azure 服务与虚拟网络集成的完全控制权。

将子网委托给 Azure 服务时，你允许该服务为该子网建立一些基本的网络配置规则，这有助于 Azure 服务以稳定的方式操作其实例。 因此，Azure 服务可能会建立一些预先部署或后期部署的情况，例如：
- 在共享的与专用的子网中部署服务。
- 向服务添加一组网络意向策略后，服务才能正常工作所需的部署。

##  <a name="advantages-of-subnet-delegation"></a>子网委派的优点

向特定服务委托子网具有以下优势：

- 有助于为一个或多个 Azure 服务指定子网，并根据要求管理子网中的实例。 例如，虚拟网络所有者可以为委托子网定义以下内容，以便更好地管理资源和访问权限，如下所示：
    - 网络筛选流量策略与网络安全组。
    - 具有用户定义的路由的路由策略。
    - 服务与服务终结点的配置集成。
- 通过以网络意向策略形式定义部署的预备条件，帮助注入的服务以更好地与虚拟网络集成。 这可确保可以影响注入的服务的正常运行的任何操作。


## <a name="who-can-delegate"></a>谁可以委派？
子网委派是虚拟网络所有者为了为特定 Azure 服务指定其中一个子网而需要执行的练习。 Azure 服务反过来会将实例部署到此子网中，供客户工作负荷使用。

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>子网在子网上的委派影响
每个 Azure 服务都定义自己的部署模型，在该模型中，用户可以在委派的子网中定义其所要执行或不支持的属性，例如：
- 与其他 Azure 服务或 VM/虚拟机规模集在同一子网中共享的子网，或它仅支持其中只有此服务的实例的专用子网。
- 支持与委托子网关联的 NSG。
- 支持与委托子网关联的 NSG 也可以与任何其他子网关联。
- 允许路由表与委托子网关联。
- 允许与委托子网关联的路由表与任何其他子网关联。
- 指示委托子网中的最小 IP 地址数。
- 指示委托子网中的 IP 地址空间来自专用 IP 地址空间（10.0.0.0/8、192.168.0.0/16、172.16.0.0/12）。
- 指示自定义 DNS 配置具有 Azure DNS 项。

注入的服务还可以添加自己的策略，如下所示：
- **安全策略**：收集给定服务工作所需的安全规则的集合。
- **路由策略**：给定服务工作所需的路由的集合。

## <a name="what-subnet-delegation-does-not-do"></a>子网委派不执行的操作

要注入到委托子网中的 Azure 服务仍有一组可用于非委托子网的基本属性，例如：
-  Azure 服务可将实例注入客户子网，但不会影响现有工作负荷。
-  这些服务应用的策略或路由是灵活的，客户可以对其进行重写。

## <a name="next-steps"></a>后续步骤

- [委托子网](manage-subnet-delegation.md)
