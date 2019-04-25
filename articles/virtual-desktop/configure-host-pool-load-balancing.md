---
title: 配置 Windows 虚拟桌面预览负载平衡方法-Azure
description: 如何配置 Windows 虚拟桌面环境的负载平衡方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0c4702dada17e759d89c33be99b3155f4b15ad9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60328878"
---
# <a name="configure-the-windows-virtual-desktop-preview-load-balancing-method"></a>配置 Windows 虚拟桌面预览负载平衡方法

配置主机池的负载平衡方法，可调整 Windows 虚拟桌面预览环境更好地适合你的需求。

>[!NOTE]
> 这不适用于持久桌面主机池因为用户始终在主机池内具有 1 对 1 映射到会话主机。

## <a name="configure-breadth-first-load-balancing"></a>配置广度优先的负载平衡

广度优先的负载均衡是新的非永久性主机池的默认配置。 广度优先的负载均衡分布在主机池中的所有可用的会话主机的新用户会话。 在配置广度优先的负载平衡，您可能主机池中设置每个会话主机的最大会话限制。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)（如果尚未这样做），以便在 PowerShell 会话中使用。

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
