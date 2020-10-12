---
title: '注册对等互连服务连接 - Azure PowerShell '
description: 本教程介绍如何使用 PowerShell 注册对等互连服务连接。
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service .
ms.openlocfilehash: a12bfa042c8741814cedc70f1dcb67dedbfd331e
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400428"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>教程：使用 Azure PowerShell 注册对等互连服务连接

在本教程中，你将了解如何使用 Azure PowerShell 注册对等互连服务。

Azure 对等互连服务是一种网络服务，可增强客户与 Microsoft 云服务（例如 Microsoft 365、Dynamics 365、软件即服务 (SaaS) 服务、Azure 或可通过公共 Internet 访问的任何 Microsoft 服务）建立的连接。 在本文中，你将了解如何使用 Azure PowerShell 注册对等互连服务连接。

如果还没有 Azure 订阅，请现在就创建一个[帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果决定在本地安装并使用 PowerShell，则本快速入门需要使用 Azure PowerShell 模块 1.0.0 版本或更高版本。 要查找已安装的版本，请运行 `Get-Module -ListAvailable Az`。 有关安装和升级的信息，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

最后，如果在本地运行 PowerShell，则还将需运行 `Connect-AzAccount`。 该命令创建与 Azure 的连接。

使用 Azure PowerShell 模块来注册和管理对等互连服务。 可以通过 PowerShell 命令行或在脚本中注册或管理对等互连服务。


## <a name="prerequisites"></a>先决条件  
必须满足以下条件：

### <a name="azure-account"></a>Azure 帐户

必须具有使用中的有效 Microsoft Azure 帐户。 设置对等互连服务连接时需要使用此帐户。 对等互连服务是 Azure 订阅中的一种资源。

### <a name="connectivity-provider"></a>连接服务提供商

可以与 Internet 服务提供商或 Internet 交换合作伙伴合作，以获取对等互连服务，从而将你的网络连接到 Microsoft 网络。

请确保连接服务提供商与 Microsoft 是合作伙伴。

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>将订阅注册到资源提供程序和功能标志

在继续执行注册对等互连服务的步骤之前，请使用 Azure PowerShell 将订阅注册到资源提供程序和功能标志。 此处指定了 Azure PowerShell 命令：

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>提取位置和服务提供商 

在 Azure PowerShell 中运行以下命令，以获取应启用对等互连服务的位置和服务提供商。 

获取对等互连服务位置：

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

获取对等互连服务提供商：
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>注册对等互连服务连接

使用以下命令集通过 Azure PowerShell 注册对等互连服务连接。 此示例注册名为 myPeeringService 的对等互连服务。

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>注册对等互连服务前缀

执行以下命令，通过 Azure PowerShell 注册连接服务提供商提供的前缀。 此示例注册名为 myPrefix 的前缀。

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>列出所有对等互连服务连接

若要查看所有对等互连服务的列表，请运行以下命令：

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>列出所有对等互连服务前缀

若要查看所有对等互连服务前缀的列表，请运行以下命令：

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>删除对等互连服务前缀

若要删除对等互连服务前缀，请运行以下命令：

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>后续步骤

- 若要了解对等互连服务连接，请参阅[对等互连服务连接](connection.md)。
- 若要了解对等互连服务连接遥测数据，请参阅[对等互连服务连接遥测数据](connection-telemetry.md)。
- 若要使用 Azure 门户注册对等互连服务连接，请参阅[注册对等互连服务连接 - Azure 门户](azure-portal.md)。
- 若要使用 Azure CLI 注册对等互连服务连接，请参阅[注册对等互连服务连接 - Azure CLI](cli.md)。
