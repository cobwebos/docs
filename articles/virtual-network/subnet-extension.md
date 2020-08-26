---
title: Azure 中的子网扩展 |Microsoft Docs
description: 了解 Azure 中的子网扩展。
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: f718471c3f79e9a33b0e03b088f8c8d2ae0231d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "73587506"
---
# <a name="subnet-extension"></a>子网扩展
将工作负荷迁移到公有云需要经过认真的规划和协调。 其中一个重要考虑因素是能够保留 IP 地址。 如果应用程序具有 IP 地址依赖性，或者法规要求使用特定的 IP 地址，则这项能力就非常重要。 Azure 虚拟网络允许使用所选的 IP 地址范围创建 VNet 和子网，从而解决了此问题。

除上述要求以外，如果还要求在本地保留某些应用程序，则迁移可能有一定的难度。 在这种情况下，必须在 Azure 与本地之间拆分应用程序，但不能在任何一端为 IP 地址重新编号。 此外，必须允许这些应用程序像是在同一个网络中一样相互通信。

上述问题的解决方法之一是使用子网扩展。 扩展网络后，位于不同实际位置的应用程序可以通过同一个广播域进行通信，而无需重新架构网络拓扑。 

尽管在一般情况下扩展网络并非良好的做法，在对于以下用例，可能有必要这样做。

- **分阶段迁移**：最常见的场景是分阶段完成迁移。 需要先迁移几个应用程序，然后逐渐将剩余的应用程序迁移到 Azure。
- **延迟**：低延迟要求可能是在本地保留某些应用程序的另一个原因，这可以确保它们尽量靠近你的数据中心。
- **符合性**：另一种用例是，需要根据法规要求在本地保留某些应用程序。
 
> [!NOTE] 
> 除非必要，否则不应扩展子网。 如果确实需要扩展子网，应尽量将此措施作为一个中间步骤来实施。 如果有时间，应尝试为本地网络中的应用程序重新编号，然后将其迁移到 Azure。

下一部分会介绍如何将子网扩展到 Azure 中。


## <a name="extend-your-subnet-to-azure"></a>将子网扩展到 Azure
 可以使用基于第 3 层叠加网络的解决方案将本地子网扩展到 Azure。 大多数解决方案使用叠加技术（例如 VXLAN）通过第 3 层叠加网络来扩展第 2 层网络。 下图显示了一个通用的解决方案。 在此解决方案中，同一子网位于 Azure 和本地这两端。 

![子网扩展示例](./media/subnet-extension/subnet-extension.png)

该子网中的 IP 地址已分配到 Azure 中和本地的 VM。 Azure 和本地的网络中都插入了一个 NVA。 当 Azure 中的 VM 尝试与本地网络中的 VM 通信时，Azure NVA 将捕获数据包，封装该数据包，然后通过 VPN/Express Route 将其发送到本地网络。 本地 NVA 接收数据包，解封装数据包，然后将其转发到 NVA 所在网络中的目标接收端。 返回流量使用类似的路径和逻辑。

在以上示例中，Azure NVA 和本地 NVA 相互通信，并了解各自的 IP 地址。 更复杂的网络还可能包含映射服务，该服务维护 NVA 及其 IP 地址之间的映射。 当 NVA 收到数据包时，将查询映射服务，以找出具有目标 IP 地址的 NVA 的地址。

下一部分将提供我们已在 Azure 中测试的子网扩展解决方案的详细信息。

## <a name="next-steps"></a>后续步骤 
[使用供应商解决方案将子网扩展到 Azure](https://github.com/microsoft/Azure-LISP)。