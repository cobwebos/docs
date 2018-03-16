---
title: "使用 PowerShell 通过区域冗余的公共 IP 地址前端创建公共负载均衡器标准 | Microsoft Docs"
description: "了解如何使用 PowerShell 通过区域冗余的公共 IP 地址前端创建公共负载均衡器标准"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: abe970eb72be5ea6944acc253c2c3d9008b20c56
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-powershell"></a>使用 PowerShell 通过区域冗余的公共 IP 地址前端创建公共负载均衡器标准

本文指导逐步通过区域冗余前端使用公共 IP 标准地址创建公共[负载均衡器标准](https://aka.ms/azureloadbalancerstandard)。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>注册可用性区域、负载均衡器标准和公共 IP 标准预览

本文需要已安装 4.4.0 版或更高版本的 AzureRM 模块。 若要查找版本，请运行 `Get-Module -ListAvailable AzureRM`。 如果需要进行安装或升级，请从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureRM)安装最新版本的 AzureRM 模块。

>[!NOTE]
[负载均衡器标准 SKU](https://aka.ms/azureloadbalancerstandard) 目前处于预览状态。 在预览期，该功能的可用性和可靠性级别可能与正式版不同。 有关详细信息，请参阅 [Microsoft Azure 预览版 Microsoft Azure 补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 请将正式版[负载均衡器基本 SKU](load-balancer-overview.md) 用于生产服务。 

> [!NOTE]
> 可用性区域处于预览状态，已准备好在开发和测试方案中使用。 可为选择 Azure 资源、区域和 VM 大小系列提供支持。 有关如何开始使用以及可以尝试将可用性区域用于哪些 Azure 资源、区域和 VM 大小系列的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。 若需支持，可以在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 上寻求帮助或者 [open an Azure support ticket](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（创建 Azure 支持票证）。  

为负载均衡器的前端公共 IP 地址选择区域或区域冗余选项之前，必须先完成[注册可用性区域预览版](https://docs.microsoft.com/azure/availability-zones/az-overview)中的步骤。

## <a name="log-in-to-azure"></a>登录 Azure

使用 `Login-AzureRmAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>创建资源组

使用以下命令创建资源组：

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>创建公共 IP 地址 
使用以下命令创建公共 IP 标准：

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>为网站创建前端 IP 配置

使用以下命令创建前端 IP 配置：

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>创建后端地址池

使用以下命令创建后端地址池：

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>在端口 80 上创建负载均衡器探测

使用以下命令在端口 80 上为负载均衡器创建运行状况探测：

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则
 使用以下命令创建负载均衡器规则：

```powershell
   $rule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>创建负载均衡器
使用以下命令创建负载均衡器标准：

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>后续步骤
- 了解如何[在可用性区域中创建公共 IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)



