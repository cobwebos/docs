---
title: 使用 Azure PowerShell 的 Azure 流量管理器子网覆盖 |Microsoft Docs
description: 本文将帮助你了解如何使用流量管理器子网替代来替代流量管理器配置文件的路由方法，以便基于使用 Azure 的最终用户 IP 地址通过预定义 IP 范围将流量定向到终结点映射PowerShell.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: 93362c0e1b359583e30886172f8ccb155c5ffaf4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348887"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>使用 Azure Powershell 的流量管理器子网覆盖

流量管理器子网替代允许更改配置文件的路由方法。  添加替代会根据最终用户的 IP 地址，使用预定义的 IP 范围将流量定向到终结点映射。 

## <a name="how-subnet-override-works"></a>子网替代的工作原理

将子网替代添加到流量管理器配置文件时，流量管理器将首先检查是否存在用于最终用户的 IP 地址的子网替代。 如果找到一个，则将用户的 DNS 查询定向到相应的终结点。  如果找不到映射，流量管理器将回退到配置文件的原始路由方法。 

可以将 IP 地址范围指定为 CIDR 范围（例如，1.2.3.0/24）或作为地址范围（例如，1.2.3.4-5.6.7.8）。 与每个终结点关联的 IP 范围对该终结点必须是唯一的。 不同终结点之间 IP 范围的任何重叠将导致流量管理器拒绝该配置文件。

支持子网替代的路由配置文件分为两种类型：

* **地理位置**-如果流量管理器找到 DNS 查询的 IP 地址的子网替代，它会将查询路由到终结点的运行状况。
* **性能**-如果流量管理器找到 DNS 查询的 IP 地址的子网替代，它将仅将流量路由到终结点（如果该终结点正常运行）。  如果子网替代终结点不正常，流量管理器将回退到性能路由试探法。

## <a name="create-a-traffic-manager-subnet-override"></a>创建流量管理器子网替代

若要创建流量管理器子网替代，可以使用 Azure PowerShell 将替代的子网添加到流量管理器终结点。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以在 [Azure Cloud Shell](https://shell.azure.com/powershell) 中运行以下命令，或者在计算机上运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 如果在计算机上运行 PowerShell，则需要 Azure PowerShell 模块 1.0.0 或更高版本。 可以运行 `Get-Module -ListAvailable Az` 来查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 以登录到 Azure。


1. **检索流量管理器终结点：**

    若要启用子网替代，请检索要向其添加替代的终结点，并使用[AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)将其存储在变量中。

    将 "名称"、"ProfileName" 和 "ResourceGroupName" 替换为要更改的终结点的值。

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **将 IP 地址范围添加到终结点：**
    
    若要将 IP 地址范围添加到终结点，你将使用[AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0)来添加范围。

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    添加范围后，使用[AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0)来更新终结点。

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
可以通过使用[AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0)来完成 IP 地址范围的删除。

1.  **检索流量管理器终结点：**

    若要启用子网替代，请检索要向其添加替代的终结点，并使用[AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)将其存储在变量中。

    将 "名称"、"ProfileName" 和 "ResourceGroupName" 替换为要更改的终结点的值。

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
     删除范围后，请使用[AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0)来更新终结点。

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>后续步骤
详细了解流量管理器[流量路由方法](traffic-manager-routing-methods.md)。

了解[子网流量路由方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
