---
title: 主机池负载平衡方法 （预览版）-Azure
description: 主机池负载平衡方法为 Windows 虚拟桌面环境的。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0a07406c8bad4ad0bef2949103d1f2c78e7dd8af
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318504"
---
# <a name="host-pool-load-balancing-methods"></a>主机池负载平衡方法

Windows 虚拟桌面 （预览版） 支持两种负载平衡方法。 每个方法确定哪些会话主机将承载当它们连接到资源池中主机的用户的会话。

Windows 虚拟桌面中提供了以下负载平衡方法：

- 广度优先的负载平衡，可在池中主机会话主机中均匀分配用户会话。
- 深度优先前负载平衡，可使会话主机与用户会话饱和主机池中。 一旦第一个会话到达其会话的限制阈值，负载均衡器将定向到下一步会话主机上的主机池的任何新用户连接，直到它达到其限制，等等。

每个主机池只能配置一种类型的负载平衡特定于它。 但是，无论其下面的行为托管池这两个负载平衡方法共享它们中：

- 如果用户已在主机池有会话并重新连接到该会话，负载均衡器将已成功重定向其到其现有会话的会话主机。 即使该会话主机的 AllowNewConnections 属性设置为 False，此行为也适用。
- 如果用户还没有一个会话中的主机池，则负载均衡器不会考虑会话主机负载平衡期间将其 AllowNewConnections 属性设置为 False。

## <a name="breadth-first-load-balancing-method"></a>广度优先的负载平衡方法

广度优先的负载平衡方法，可将针对此方案进行优化的用户连接分配。 此方法非常适用于想要为连接到其共用虚拟桌面环境的用户提供最佳体验的组织。

广度优先的方法首先查询允许新的连接的会话主机。 方法然后选择具有最小会话主机的会话数。 如果存在等同，该方法在查询中选择第一个会话主机。

## <a name="depth-first-load-balancing-method"></a>深度优先前负载平衡方法

深度优先前负载平衡方法，可使一个会话主机饱和每次针对此方案进行优化。 此方法非常适合于节约成本的统一组织希望更精细地控制他们已为一个主机池分配的虚拟机的数量。

深度优先方法首先查询中允许新的连接，并且还未得到超过其最大会话限制会话主机。 该方法则会选择具有会话的最大数目的会话主机。 如果存在等同，该方法在查询中选择第一个会话主机。