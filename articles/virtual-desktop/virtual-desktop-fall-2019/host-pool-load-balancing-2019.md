---
title: Windows 虚拟桌面主机池负载平衡-Azure
description: Windows 虚拟桌面环境的主机池负载平衡方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e2bfbd580d0e114cf4a135879340745107183b6a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614806"
---
# <a name="host-pool-load-balancing-methods"></a>主机池负载均衡方法

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。 如果尝试管理春季2020更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../host-pool-load-balancing.md)。

Windows 虚拟桌面支持两种负载平衡方法。 每个方法都确定在连接到主机池中的资源时，哪些会话主机将承载用户会话。

Windows 虚拟桌面提供以下负载平衡方法：

- 广度优先负载平衡允许您在主机池中的会话主机之间均匀分布用户会话。
- 深度优先负载平衡使你能够在主机池中使用用户会话对会话主机进行饱和。 第一个会话达到其会话限制阈值后，负载均衡器会将与主机池中的下一个会话主机的任何新用户连接定向到该主机，直到它达到其限制等。

每个主机池只能配置特定于其的一种负载平衡。 但是，无论它们位于哪个主机池，这两种负载平衡方法均共享以下行为：

- 如果用户已在主机池中有会话，并重新连接到该会话，则负载均衡器会将其成功地重定向到其现有会话的会话主机。 即使会话主机的 AllowNewConnections 属性设置为 False，此行为也适用。
- 如果用户还没有在主机池中建立会话，则负载均衡器不会将其 AllowNewConnections 属性设置为 False 的会话主机视为负载平衡。

## <a name="breadth-first-load-balancing-method"></a>广度优先负载平衡方法

通过广度优先的负载平衡方法，您可以分发用户连接以便针对此方案进行优化。 此方法非常适用于想要为连接到其共用虚拟桌面环境的用户提供最佳体验的组织。

广度优先方法首先查询允许新连接的会话主机。 然后，方法选择会话数最少的会话主机。 如果存在关联，则该方法将选择查询中的第一个会话主机。

## <a name="depth-first-load-balancing-method"></a>深度优先负载平衡方法

深度优先的负载平衡方法可让你一次为一个会话主机提供饱和，以便针对此方案进行优化。 此方法非常适合需要更精细地控制其为主机池分配的虚拟机数量的注重成本的组织。

深度优先方法首先查询允许新连接的会话主机，而不会超出其最大会话限制。 然后，方法选择具有最多会话的会话主机。 如果存在关联，则该方法将选择查询中的第一个会话主机。