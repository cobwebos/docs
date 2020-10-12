---
title: 使用 Azure PowerShell 的 Azure 流量管理器子网覆盖 |Microsoft Docs
description: 本文帮助你了解如何使用流量管理器子网替代来替代流量管理器配置文件的路由方法，以便使用 Azure PowerShell 通过预定义的 IP 范围到终结点的映射，基于最终用户 IP 地址将流量定向到某个终结点。
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 01bd3b1e945ee7c9ac16af7048536c0a9e2d731a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401582"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>使用 Azure PowerShell 的流量管理器子网替代

使用流量管理器子网替代可以更改配置文件的路由方法。  添加替代后，会使用预定义的 IP 范围到终结点的映射，基于最终用户的 IP 地址来定向流量。 

## <a name="how-subnet-override-works"></a>子网替代的工作原理

将子网替代添加到流量管理器配置文件时，流量管理器将首先检查是否存在用于最终用户的 IP 地址的子网替代。 如果找到一个，则将用户的 DNS 查询定向到相应的终结点。  如果找不到映射，流量管理器将回退到配置文件的原始路由方法。 

可将 IP 地址范围指定为 CIDR 范围（例如 1.2.3.0/24）或地址范围（例如 1.2.3.4-5.6.7.8）。 与每个终结点关联的 IP 范围对于该终结点必须是唯一的。 不同终结点之间的 IP 范围出现任何重叠会导致流量管理器拒绝配置文件。

有两种类型的路由配置文件支持子网替代：

* **地理** - 如果流量管理器找到了 DNS 查询的 IP 地址的子网替代，它会将该查询路由到终结点，而不管该终结点的运行状况如何。
* **性能** - 如果流量管理器找到了 DNS 查询的 IP 地址的子网替代，它只会将流量路由到正常的终结点。  如果子网替代终结点不正常，流量管理器将回退到性能路由试探法。

## <a name="create-a-traffic-manager-subnet-override"></a>创建流量管理器子网替代

若要创建流量管理器子网替代，可以使用 Azure PowerShell 将替代子网添加到流量管理器终结点。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以在 [Azure Cloud Shell](https://shell.azure.com/powershell) 中运行以下命令，或者在计算机上运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 如果在计算机上运行 PowerShell，则需要 Azure PowerShell 模块 1.0.0 或更高版本。 可以运行 `Get-Module -ListAvailable Az` 来查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 以登录到 Azure。


1. **检索流量管理器终结点：**

    若要启用子网替代，请使用 [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0) 检索要将替代添加到的终结点，并将其存储在变量中。

    请将 Name、ProfileName 和 ResourceGroupName 替换为要更改的终结点的值。

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **将 IP 地址范围添加到终结点：**
    
    若要将 IP 地址范围添加到终结点，请使用 [Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) 添加范围。

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    添加范围后，使用 [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) 更新终结点。

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
可以使用 [Remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0) 删除 IP 地址范围。

1.  **检索流量管理器终结点：**

    若要启用子网替代，请使用 [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0) 检索要将替代添加到的终结点，并将其存储在变量中。

    请将 Name、ProfileName 和 ResourceGroupName 替换为要更改的终结点的值。

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **从终结点中删除 IP 地址范围：**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     删除范围后，使用 [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) 更新终结点。

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>后续步骤
详细了解流量管理器[流量路由方法](traffic-manager-routing-methods.md)。

了解[子网流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
