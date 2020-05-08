---
title: 配置 Windows 虚拟桌面负载平衡-Azure
description: 如何为 Windows 虚拟桌面环境配置负载平衡方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 447de339d3ceef7aeb1c232605b0e30bbbb1e7d8
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612429"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>配置 Windows 虚拟桌面负载均衡方法

为主机池配置负载平衡方法，可以调整 Windows 虚拟桌面环境，从而更好地满足你的需求。

>[!NOTE]
> 这不适用于永久性桌面主机池，因为用户始终将1:1 映射到主机池中的某个会话主机。

## <a name="prerequisites"></a>先决条件

本文假设已按照[设置 Windows 虚拟桌面 powershell 模块](powershell-module.md)中的说明下载并安装 PowerShell 模块并登录到 Azure 帐户。

## <a name="configure-breadth-first-load-balancing"></a>配置广度优先负载均衡

广度优先负载平衡是新的非持久性主机池的默认配置。 广度优先负载平衡在主机池中的所有可用会话主机之间分配新的用户会话。 配置广度优先负载平衡时，可以为主机池中的每个会话主机设置最大会话限制。

若要将主机池配置为在不调整最大会话限制的情况下执行广度优先负载平衡，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' 
```

然后，为了确保已设置了广度优先的负载均衡方法，请运行以下 cmdlet： 

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType 

Name             : hostpoolname 
LoadBalancerType : BreadthFirst
```

若要将主机池配置为执行广度优先负载平衡，并使用新的最大会话限制，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>配置深度优先负载平衡

深度优先负载平衡将新用户会话分发到具有最大连接数但未达到其最大会话限制阈值的可用会话主机。 配置深度优先负载平衡时，必须为主机池中的每个会话主机设置最大会话限制。

若要将主机池配置为执行深度优先负载平衡，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ### 
```

若要确保已更新设置，请运行以下 cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit 

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>配置 Azure 门户的负载平衡

你还可以配置 Azure 门户的负载平衡。

配置负载平衡：

1. 登录 Azure 门户：https://portal.azure.com。 
2. 搜索并选择 "服务" 下的**Windows 虚拟桌面**。 
3. 在 Windows 虚拟桌面页面中，选择 "**主机池**"。
4. 选择要编辑的主机池的名称。
5. 选择“属性”  。
6. 在该字段中输入**最大会话限制**，并在下拉菜单中选择要用于此主机池的**负载平衡算法**。
7. 选择“保存”  。 这会应用新的负载平衡设置。