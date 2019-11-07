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
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587506"
---
# <a name="subnet-extension"></a>子网扩展
工作负荷迁移到公有云需要认真规划和协调。 其中一项关键注意事项可以是保留 IP 地址的能力。 如果你的应用程序具有 IP 地址依赖关系，或者你具有使用特定 IP 地址的符合性要求，这可能非常重要。 Azure 虚拟网络通过允许你使用所选的 IP 地址范围创建 VNet 和子网，解决了此问题。

在上述要求与其他要求在本地保留某些应用程序的要求一起使用时，迁移可能会有一定的难度。 在这种情况下，你必须在 Azure 和本地之间拆分应用程序，而无需对任何一侧的 IP 地址进行重新编号。 此外，您还必须允许应用程序与同一网络中的应用程序进行通信。

上述问题的一种解决方案是子网扩展。 如果扩展网络，应用程序可以在同一广播网域位于不同的物理位置时进行讨论，无需重塑网络拓扑。 

在一般情况下，扩展网络并不是一种很好的做法，在以下情况下，可能需要使用它们。

- **分阶段迁移**：最常见的情况是你想要进行迁移。 你需要先和多个应用程序，将其余应用程序迁移到 Azure。
- **延迟**：低延迟要求可能是另一个原因，那就是让某些应用程序保留在本地，以确保它们尽可能接近你的数据中心。
- **符合性**：另一个用例是，你可能具有在本地保留某些应用程序的符合性要求。
 
> [!NOTE] 
> 除非有必要，否则不应扩展子网。 在扩展子网的情况下，应尝试使其成为中间步骤。 对于时间，应尝试在本地网络中重新编号应用程序并将其迁移到 Azure。

在下一部分中，我们将讨论如何将子网扩展到 Azure 中。


## <a name="extend-your-subnet-to-azure"></a>将子网扩展到 Azure
 可以使用基于第3层覆盖网络的解决方案将本地子网扩展到 Azure。 大多数解决方案使用 VXLAN 等覆盖技术来扩展第2层网络（使用第3层覆盖网络）。 下图显示了一个通用的解决方案。 在此解决方案中，同一子网存在于 Azure 和本地的双方。 

![子网扩展示例](./media/subnet-extension/subnet-extension.png)

子网中的 IP 地址将分配给 Azure 上和本地的虚拟机。 Azure 和本地都在其网络中插入了 NVA。 当 Azure 中的 VM 尝试与本地网络中的 VM 通信时，Azure NVA 将捕获数据包，将其封装，并通过 VPN/Express 路由将其发送到本地网络。 本地 NVA 接收数据包，将其 decapsulates，并将其转发到其网络中的预期接收方。 返回流量使用类似的路径和逻辑。

在上面的示例中，Azure NVA 和本地 NVA 相互通信，并了解彼此之间的 IP 地址。 更复杂的网络还可以有一个映射服务，该服务维护 Nva 与它们背后的 IP 地址之间的映射。 当 NVA 收到数据包时，它将查询映射服务以找出其后面有目标 IP 地址的 NVA 的地址。

在下一部分中，你将了解有关我们在 Azure 中测试的子网扩展解决方案的详细信息。

## <a name="next-steps"></a>后续步骤 
[使用供应商解决方案将子网扩展到 Azure。](https://github.com/microsoft/Azure-LISP)