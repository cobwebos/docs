---
title: Windows 虚拟桌面主机池负载平衡 - Azure
description: 适用于 Windows 虚拟桌面环境的主机池负载平衡方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 193821ed0df09b87f19e45a82ca42026405a0dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127870"
---
# <a name="host-pool-load-balancing-methods"></a>主机池负载均衡方法

Windows 虚拟桌面支持两种负载平衡方法。 每种方法确定在用户连接到主机池中的资源时，哪个会话主机将承载用户的会话。

以下负载平衡方法在 Windows 虚拟桌面中可用：

- 通过"广度优先"负载平衡，您可以在主机池中的会话主机之间均匀分配用户会话。
- 深度优先负载平衡允许您在主机池中使会话主机与用户会话饱和。 第一个会话达到其会话限制阈值后，负载均衡器将引导任何新用户连接到主机池中的下一个会话主机，直到其达到其限制，等等。

每个主机池只能配置特定于它的一种类型的负载平衡。 但是，无论它们位于哪个主机池中，两种负载平衡方法都具有以下行为：

- 如果用户已在主机池中具有会话并重新连接到该会话，负载均衡器将成功地将其现有会话重定向到会话主机。 即使会话主机的"允许新连接"属性设置为 False，此行为也会适用。
- 如果用户在主机池中尚未具有会话，则负载均衡器不会考虑其 AllowNewConnections 属性在负载平衡期间设置为 False 的会话主机。

## <a name="breadth-first-load-balancing-method"></a>广度优先负载平衡方法

广度优先负载平衡方法允许您分发用户连接以针对此方案进行优化。 此方法非常适合希望为连接到其池虚拟桌面环境的用户提供最佳体验的组织。

广度优先方法首先查询允许新连接的会话主机。 然后，该方法选择会话数最少的会话主机。 如果存在连接，该方法将选择查询中的第一个会话主机。

## <a name="depth-first-load-balancing-method"></a>深度优先负载平衡方法

深度优先负载平衡方法允许您一次使一个会话主机饱和，以便针对此方案进行优化。 此方法非常适合注重成本的组织，这些组织希望更精细地控制他们为主机池分配的虚拟机数。

深度优先方法首先查询允许新连接且未超过其最大会话限制的会话主机。 然后，该方法选择会话数最多的会话主机。 如果有领带，该方法将选择查询中的第一个会话主机。