---
title: Windows 虚拟桌面主机池负载平衡-Azure
description: 了解 Windows 虚拟桌面环境的主机池负载平衡方法。
author: Heidilohr
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd8f9e4a3ef63cd97f96af3d4f96a2bb65c3cd09
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951853"
---
# <a name="host-pool-load-balancing-methods"></a>主机池负载均衡方法

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md)。

Windows 虚拟桌面支持两种负载平衡方法。 每个方法都确定在连接到主机池中的资源时，哪些会话主机将承载用户会话。

Windows 虚拟桌面提供以下负载平衡方法：

- 广度优先负载平衡允许您在主机池中的会话主机之间均匀分布用户会话。
- 深度优先负载平衡使你能够在主机池中使用用户会话对会话主机进行饱和。 第一个会话达到其会话限制阈值后，负载均衡器会将与主机池中的下一个会话主机的任何新用户连接定向到该主机，直到它达到其限制等。

每个主机池只能配置特定于其的一种负载平衡。 但是，无论它们位于哪个主机池，这两种负载平衡方法均共享以下行为：

- 如果用户已在主机池中有会话，并重新连接到该会话，则负载均衡器会将其成功地重定向到其现有会话的会话主机。 即使会话主机的 AllowNewConnections 属性设置为 False，此行为也适用。
- 如果用户还没有在主机池中建立会话，则负载均衡器不会将其 AllowNewConnections 属性设置为 False 的会话主机视为负载平衡。

## <a name="breadth-first-load-balancing-method"></a>广度优先负载平衡方法

通过广度优先的负载平衡方法，您可以分发用户连接以便针对此方案进行优化。 此方法非常适用于想要为连接到其共用虚拟桌面环境的用户提供最佳体验的组织。

广度优先方法首先查询允许新连接的会话主机。 然后，此方法会随机从包含最少会话数的一组会话主机中随机选择一个会话主机。 例如，如果有九台计算机具有11、12、13、14、15、16、17、18和19个会话，则您创建的新会话不会自动切换到第一台计算机。 相反，它可以转到最小数量的会话 (11、12、13、14、15) 的前五台计算机。

## <a name="depth-first-load-balancing-method"></a>深度优先负载平衡方法

深度优先的负载平衡方法可让你一次为一个会话主机提供饱和，以便针对此方案进行优化。 此方法非常适合需要更精细地控制其为主机池分配的虚拟机数量的注重成本的组织。

深度优先方法首先查询允许新连接的会话主机，而不会超出其最大会话限制。 然后，方法选择具有最多会话的会话主机。 如果存在关联，则该方法将选择查询中的第一个会话主机。

>[!IMPORTANT]
>深度优先负载平衡算法根据最大会话主机限制将会话分发到会话主机。 使用深度优先负载平衡算法时，此参数是必需的。 为了获得最佳的用户体验，请确保将 "最大会话主机限制" 参数更改为最适合你的环境的数字。