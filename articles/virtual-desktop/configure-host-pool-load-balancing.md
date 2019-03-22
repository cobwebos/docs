---
title: 配置 Windows 虚拟机负载平衡方法 （预览版）-Azure
description: 如何配置 Windows 虚拟桌面环境的负载平衡方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 10a1066b85b16749fe95e373e696d486b0e7bafa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318339"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>配置 Windows 虚拟机负载平衡方法

配置主机池的负载平衡方法，可调整的 Windows 虚拟桌面 （预览版） 环境，使其更适合你的需求。

>[!NOTE]
> 这不适用于持久桌面主机池因为用户始终在主机池内具有 1 对 1 映射到会话主机。

## <a name="configure-breadth-first-load-balancing"></a>配置广度优先的负载平衡

广度优先的负载均衡是新的非永久性主机池的默认配置。 广度优先的负载均衡分布在主机池中的所有可用的会话主机的新用户会话。 在配置广度优先的负载平衡，您可能主机池中设置每个会话主机的最大会话限制。

首先，[下载并导入的 Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)如果尚未在 PowerShell 会话中使用。

若要配置一个主机的池来执行广度优先的负载平衡而无需调整最大会话限制，请运行以下 PowerShell cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

若要配置主机池执行广度优先的负载平衡，并使用新的最大会话限制，请运行以下 PowerShell cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>配置深度优先前负载平衡

深度优先前负载平衡将分发到的最大连接数具有可用的会话主机的新用户会话，但是未达到其最大会话限制阈值。 当配置深度优先前负载平衡，你**必须**主机池中设置每个会话主机的最大会话限制。

若要配置一个主机的池来执行深度优先的负载平衡，请运行以下 PowerShell cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
