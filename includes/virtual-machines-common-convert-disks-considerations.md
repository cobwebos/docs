---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416060"
---
* 该转换需要重启 VM，因此请在预先存在的维护时段内计划 VM 迁移。 

* 转换是不可逆的。 

* 请注意，任何具有[虚拟机参与者](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色的用户将不能更改 VM 大小（因为它们可以预转换）。 这是因为包含托管磁盘的 VM 要求用户对 OS 磁盘具有 Microsoft.Compute/disks/write 权限。

* 请务必测试转换。 在生产环境中执行迁移之前迁移测试性虚拟机。

* 在转换期间，将解除分配 VM。 转换完成后，VM 在启动时会接收新的 IP 地址。 如果需要，可向 VM [分配静态 IP 地址](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)。

* 查看 Azure VM 代理支持转换过程所需的最低版本。 有关如何检查和更新代理版本的信息，请参阅 [Azure 中 VM 代理的最低版本支持](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)