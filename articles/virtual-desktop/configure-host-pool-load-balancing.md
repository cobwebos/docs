---
title: 配置 Windows 虚拟桌面负载平衡方法-Azure
description: 如何为 Windows 虚拟桌面环境配置负载平衡方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 3a940dbf592087878cb9dd19f856f1a3d94291c5
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676781"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>配置 Windows 虚拟桌面负载均衡方法

为主机池配置负载平衡方法，可以调整 Windows 虚拟桌面环境，从而更好地满足你的需求。

>[!NOTE]
> 这不适用于永久性桌面主机池，因为用户始终将1:1 映射到主机池中的某个会话主机。

## <a name="configure-breadth-first-load-balancing"></a>配置广度优先负载均衡

广度优先负载平衡是新的非持久性主机池的默认配置。 广度优先负载平衡在主机池中的所有可用会话主机之间分配新的用户会话。 配置广度优先负载平衡时，可以为主机池中的每个会话主机设置最大会话限制。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)（如果尚未这样做），以便在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

若要将主机池配置为在不调整最大会话限制的情况下执行广度优先负载平衡，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

若要将主机池配置为执行广度优先负载平衡，并使用新的最大会话限制，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>配置深度优先负载平衡

深度优先负载平衡将新用户会话分发到具有最大连接数但未达到其最大会话限制阈值的可用会话主机。 配置深度优先负载平衡时，**必须**为主机池中的每个会话主机设置最大会话限制。

若要将主机池配置为执行深度优先负载平衡，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
