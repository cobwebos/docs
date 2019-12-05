---
title: 教程：将托管实例添加到故障转移组
description: 了解如何为 Azure SQL 数据库托管实例配置故障转移组。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: 939606412c55ddad29801776c2385b406dc93a33
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286763"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>教程：将 SQL 数据库托管实例添加到故障转移组

将 SQL 数据库托管实例添加到故障转移组。 本文介绍如何执行以下操作：

> [!div class="checklist"]
> - 创建主托管实例
> - 创建辅助托管实例作为[故障转移组](sql-database-auto-failover-group.md)的一部分。 
> - 测试故障转移

  > [!NOTE]
  > - 在学习本教程时，请确保使用[为托管实例设置故障转移组的先决条件](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)来配置资源。 
  > - 创建托管实例可能需要花费很长时间。 因此，本教程可能需要几个小时才能完成。 有关预配时间的详细信息，请参阅[托管实例管理操作](sql-database-managed-instance.md#managed-instance-management-operations)。 


## <a name="prerequisites"></a>先决条件

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
要完成本教程，请确保具有： 

- Azure 订阅。 如果还没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
若要完成本教程，请确保准备好以下各项：

- Azure 订阅。 如果还没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 - 创建资源组和主托管实例
在此步骤中，将使用 Azure 门户或 PowerShell 为故障转移组创建资源组和主托管实例。 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal) 

使用 Azure 门户创建资源组和主托管实例。 

1. 在 Azure 门户的左侧菜单中选择“Azure SQL”。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择“+ 添加”以打开“选择 SQL 部署选项”页。 通过选择 "数据库" 磁贴上的 "显示详细信息"，可以查看有关不同数据库的其他信息。
1. 在 " **SQL 托管实例**" 磁贴上选择 "**创建**"。 

    ![选择托管实例](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. 在“创建 Azure SQL 数据库托管实例”页的“基本信息”选项卡上
    1. 在“项目详细信息”下，从下拉列表中选择你的订阅，然后选择“创建新资源组”。 键入资源组的名称，例如 `myResourceGroup`。 
    1. 在“托管实例详细信息”下，提供托管实例的名称，以及要将托管实例部署到的区域。 将“计算 + 存储”保留默认值。 
    1. 在“管理员帐户”下，提供管理员登录名（例如 **）和复杂的管理员密码。** `azureuser` 

    ![创建主托管实例](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. 将剩余设置保留默认值，然后选择“查看 + 创建”检查托管实例设置。 
1. 选择“创建”以创建主托管实例。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 创建资源组和主托管实例。 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary managed instance created successfully."
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建 Azure 资源组。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 创建虚拟网络。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 向虚拟网络添加子网配置。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 获取资源组中的虚拟网络。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 获取虚拟网络中的子网。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 创建网络安全组。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 创建路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虚拟网络的子网配置。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虚拟网络。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 获取网络安全组。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 将网络安全规则配置添加到网络安全组。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新网络安全组。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 将路由添加到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 创建 Azure SQL 数据库托管实例。  |

---

## <a name="2---create-secondary-virtual-network"></a>2-创建辅助虚拟网络
如果要使用 Azure 门户来创建托管实例，则需要单独创建虚拟网络，因为主和辅助托管实例的子网不具有重叠的范围。 如果要使用 PowerShell 配置托管实例，请跳到步骤3。 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal) 
若要验证主虚拟网络的子网范围，请执行以下步骤：
1. 在 [Azure 门户](https://portal.azure.com)中导航到你的资源组，并选择主实例的虚拟网络。 
1. 在“设置”下选择“子网”，并记下“地址范围”。 辅助托管实例的虚拟网络的子网地址范围不能与此重叠。 


   ![主子网](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

若要创建虚拟网络，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”并搜索“虚拟网络”。 
1. 选择 Microsoft 发布的“虚拟网络”选项，然后在下一页选择“创建”。 
1. 填写必填字段以配置辅助托管实例的虚拟网络，然后选择“创建”。 

   下表显示了辅助虚拟网络所需的值：

    | **字段** | 值 |
    | --- | --- |
    | **Name** |  辅助托管实例要使用的虚拟网络的名称，例如 `vnet-sql-mi-secondary`。 |
    | **地址空间** | 虚拟网络的地址空间，例如 `10.128.0.0/16`。 | 
    | **订阅** | 主托管实例和资源组所在的订阅。 |
    | **区域** | 将部署辅助托管实例的位置。 |
    | **子网** | 子网的名称。 默认会提供 `default`。 |
    | **地址范围**| 子网的地址范围。 此范围必须不同于主托管实例的虚拟网络使用的子网地址范围，例如 `10.128.0.0/24`。  |
    | &nbsp; | &nbsp; |

    ![辅助虚拟网络值](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

仅当使用 Azure 门户部署托管实例时，才需要执行此步骤。 如果使用的是 PowerShell，请跳到步骤3。 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 - 创建辅助托管实例
此步骤将在 Azure 门户中创建一个辅助托管实例，同时在两个托管实例之间配置网络。 

第二个托管实例必须：
- 是空的。 
- 与主托管实例的子网和 IP 范围不同。 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal) 

使用 Azure 门户创建辅助托管实例。 

1. 在 Azure 门户的左侧菜单中选择“Azure SQL”。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择“+ 添加”以打开“选择 SQL 部署选项”页。 通过选择 "数据库" 磁贴上的 "显示详细信息"，可以查看有关不同数据库的其他信息。
1. 在 " **SQL 托管实例**" 磁贴上选择 "**创建**"。 

    ![选择托管实例](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. 在“创建 Azure SQL 数据库托管实例”页的“基本信息”选项卡上，填写必填字段以配置辅助托管实例。 

   下表显示了辅助托管实例所需的值：
 
    | **字段** | 值 |
    | --- | --- |
    | **订阅** |  主托管实例所在的订阅。 |
    | **资源组**| 主托管实例所在的资源组。 |
    | **托管实例名称** | 新辅助托管实例的名称，例如 `sql-mi-secondary`  | 
    | **区域**| 辅助托管实例的位置。  |
    | **托管实例管理员登录名** | 要用于新辅助托管实例的登录名，例如 `azureuser`。 |
    | **密码** | 新辅助托管实例的管理员登录名使用的复杂密码。  |
    | &nbsp; | &nbsp; |

1. 在“网络”选项卡下，对于“虚拟网络”，请从下拉列表中选择为辅助托管实例创建的虚拟网络。

   ![辅助托管实例网络](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. 在“其他设置”选项卡下，对于“异地复制”，请选择“用作故障转移辅助实例”旁边的“是”。 从下拉列表中选择主托管实例。 
    1. 确保排序规则和时区与主托管实例匹配。 在本教程中创建的主托管实例使用了默认的 `SQL_Latin1_General_CP1_CI_AS` 排序规则和 `(UTC) Coordinated Universal Time` 时区。 

   ![辅助托管实例网络](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. 选择“查看 + 创建”以检查辅助托管实例的设置。 
1. 选择“创建”以创建辅助托管实例。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 创建辅助托管实例。 

   ```powershell-interactive
   # Configure secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary managed instance created successfully."
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建 Azure 资源组。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 创建虚拟网络。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 向虚拟网络添加子网配置。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 获取资源组中的虚拟网络。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 获取虚拟网络中的子网。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 创建网络安全组。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 创建路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虚拟网络的子网配置。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虚拟网络。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 获取网络安全组。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 将网络安全规则配置添加到网络安全组。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新网络安全组。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 将路由添加到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 创建 Azure SQL 数据库托管实例。  |

---

## <a name="4---create-primary-gateway"></a>4-创建主网关 
对于要参与故障转移组的两个托管实例，必须在这两个托管实例的虚拟网络之间配置一个网关，以便能够进行网络通信。 您可以使用 Azure 门户创建主托管实例的网关。 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户创建主托管实例的虚拟网络的网关。 


1. 在 [Azure 门户](https://portal.azure.com)中转到你的资源组，并选择主托管实例的“虚拟网络”资源。 
1. 在“设置”下选择“子网”，然后选择添加新的“网关子网”。 保留默认值。 

   ![为主托管实例添加网关](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. 创建子网网关后，在左侧导航窗格中选择“创建资源”，然后在搜索框中键入 **。** `Virtual network gateway` 选择“Microsoft”发布的“虚拟网络网关”资源。 

   ![创建新的虚拟网络网关](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. 填写必填字段，为主托管实例配置网关。 

   下表显示了主托管实例的网关所需的值：
 
    | **字段** | 值 |
    | --- | --- |
    | **订阅** |  主托管实例所在的订阅。 |
    | **Name** | 虚拟网络网关的名称，例如 `primary-mi-gateway`。 | 
    | **区域** | 辅助托管实例所在的区域。 |
    | **网关类型** | 选择“VPN”。 |
    | **VPN 类型** | 选择“基于路由” |
    | **SKU**| 保留默认值 `VpnGw1`。 |
    | **位置**| 主托管实例和主虚拟网络所在的位置。   |
    | **虚拟网络**| 选择在第 2 部分创建的虚拟网络，例如 `vnet-sql-mi-primary`。 |
    | **公共 IP 地址**| 选择“新建”。 |
    | **公共 IP 地址名称**| 输入 IP 地址的名称，例如 `primary-gateway-IP`。 |
    | &nbsp; | &nbsp; |

1. 将其他值保留为默认值，然后选择“查看 + 创建”以检查虚拟网络网关的设置。

   ![主网关设置](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. 选择“创建”以创建新的虚拟网络网关。 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 创建主托管实例的虚拟网络的网关。 

   ```powershell-interactive
   # Create primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 获取资源组中的虚拟网络。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 向虚拟网络添加子网配置。 | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虚拟网络。  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 获取虚拟网络中的子网。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 创建公共 IP 地址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 为虚拟网络网关创建 IP 配置 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 创建虚拟网络网关 |


---


## <a name="5---create-secondary-gateway"></a>5-创建辅助网关 
在此步骤中，使用 Azure 门户创建辅助托管实例的虚拟网络的网关。 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户，重复上一部分中的步骤，为辅助托管实例创建虚拟网络子网和网关。 填写必填字段，为辅助托管实例配置网关。 

   下表显示了辅助托管实例的网关所需的值：

   | **字段** | 值 |
   | --- | --- |
   | **订阅** |  辅助托管实例所在的订阅。 |
   | **Name** | 虚拟网络网关的名称，例如 `secondary-mi-gateway`。 | 
   | **区域** | 辅助托管实例所在的区域。 |
   | **网关类型** | 选择“VPN”。 |
   | **VPN 类型** | 选择“基于路由” |
   | **SKU**| 保留默认值 `VpnGw1`。 |
   | **位置**| 辅助托管实例和辅助虚拟网络所在的位置。   |
   | **虚拟网络**| 选择在第 2 部分创建的虚拟网络，例如 `vnet-sql-mi-secondary`。 |
   | **公共 IP 地址**| 选择“新建”。 |
   | **公共 IP 地址名称**| 输入 IP 地址的名称，例如 `secondary-gateway-IP`。 |
   | &nbsp; | &nbsp; |

   ![辅助网关设置](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 为辅助托管实例的虚拟网络创建网关。 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 获取资源组中的虚拟网络。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 向虚拟网络添加子网配置。 | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虚拟网络。  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 获取虚拟网络中的子网。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 创建公共 IP 地址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 为虚拟网络网关创建 IP 配置 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 创建虚拟网络网关 |

---


## <a name="6---connect-the-gateways"></a>6 - 连接网关
在此步骤中，创建两个虚拟网络的两个网关之间的双向连接。 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户连接两个网关。 


1. 从 " [Azure 门户](https://portal.azure.com)中选择"**创建资源**"。
1. 在 "搜索" 框中键入 `connection`，然后按 enter 搜索，这会将你转到 Microsoft 发布的**连接**资源。
1. 选择 "**创建**" 以创建连接。 
1. 在 "**基本**信息" 选项卡上，选择以下值，然后选择 **"确定"** 。 
    1. 为**连接类型**选择 `VNet-to-VNet`。 
    1. 从下拉列表中选择订阅。 
    1. 在下拉中选择托管实例的资源组。 
    1. 从下拉位置中选择主托管实例的位置 
1. 在 "**设置**" 选项卡上，选择或输入以下值，然后选择 **"确定"** ：
    1. 选择**第一个虚拟网络网关**的主网络网关，如 `Primary-Gateway`。  
    1. 为**第二个虚拟网络网关**（如 `Secondary-Gateway`）选择辅助网络网关。 
    1. 选中 "**建立双向连接**" 旁边的复选框。 
    1. 保留默认的主连接名称，或将其重命名为你选择的值。 
    1. 提供连接的**共享密钥（PSK）** ，如 `mi1m2psk`。 

   ![创建网关连接](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. 在 "**摘要**" 选项卡上，查看双向连接的设置，然后选择 **"确定"** 以创建连接。 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 连接两个网关。 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 创建两个虚拟网络网关之间的连接。   |

---


## <a name="7---create-a-failover-group"></a>7 - 创建故障转移组
此步骤将创建故障转移组，并将两个托管实例添加到其中。 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
使用 Azure 门户创建故障转移组。 


1. 在 **Azure 门户**的左侧菜单中选择“Azure SQL”[](https://portal.azure.com)。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择在第一部分中创建的主托管实例，例如 `sql-mi-primary`。 
1. 在“设置”下，导航到“实例故障转移组”，然后选择“添加组”打开“实例故障转移组”页。 

   ![添加故障转移组](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. 在“实例故障转移组”页上键入故障转移组的名称（例如 **），然后从下拉列表中选择辅助托管实例（例如** ）。`failovergrouptutorial``sql-mi-secondary` 选择“创建”以创建故障转移组。 

   ![创建故障转移组](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. 故障转移组部署完成后，你将返回到“故障转移组”页。 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
使用 PowerShell 创建故障转移组。 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 创建新的 Azure SQL 数据库托管实例故障转移组。  |


---


## <a name="8---test-failover"></a>8 - 测试故障转移
此步骤将故障转移组故障转移到辅助服务器，然后使用 Azure 门户故障回复。 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
使用 Azure 门户测试故障转移。 


1. 在 [Azure 门户](https://portal.azure.com)中导航到你的托管实例，然后在“设置”下选择“实例故障转移组”。 
1. 查看哪个托管实例是主实例，哪个托管实例是辅助实例。 
1. 选择“故障转移”，然后在有关正在断开 TDS 会话的警告中选择“是”。 

   ![将故障转移组故障转移](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. 查看哪个托管实例是主实例，哪个实例是辅助实例。 如果故障转移成功，这两个实例的角色应会交换。 

   ![故障转移后托管实例的角色已交换](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. 再次选择“故障转移”，将主实例故障回复为主角色。 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
使用 PowerShell 测试故障转移。 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


将故障转移组还原为主服务器：

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | 获取或列出托管实例故障转移组。| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | 执行托管实例故障转移组的故障转移。 | 

---



## <a name="clean-up-resources"></a>清理资源
若要清理资源，请依次删除托管实例、虚拟群集、所有剩余资源和资源组。 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. 在 [Azure 门户](https://portal.azure.com)中导航到你的资源组。 
1. 选择托管实例，然后选择 "**删除**"。 在文本框中键入 `yes` 以确认你要删除该资源，然后选择“删除”。 此过程可能需要一段时间才能在后台完成，在完成之前，你将无法删除*虚拟群集*或任何其他从属资源。 在“活动”选项卡中监视删除过程，以确认托管实例已删除。 
1. 删除托管实例后，请删除虚拟群集，方法是在资源组中将其选中，然后选择“删除”。 在文本框中键入 `yes` 以确认你要删除该资源，然后选择“删除”。 
1. 删除任何剩余资源。 在文本框中键入 `yes` 以确认你要删除该资源，然后选择“删除”。 
1. 删除资源组：选择“删除资源组”，键入资源组的名称 **，然后选择“删除”。** `myResourceGroup` 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

需要删除资源组两次。 第一次删除资源组将删除托管实例和虚拟群集，但随后会失败，并显示错误消息 `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`。 再次运行 Remove-AzResourceGroup 命令，删除所有残留资源以及资源组。

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组。 |

---

## <a name="full-script"></a>完整脚本

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建 Azure 资源组。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 创建虚拟网络。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 向虚拟网络添加子网配置。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 获取资源组中的虚拟网络。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 获取虚拟网络中的子网。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 创建网络安全组。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 创建路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虚拟网络的子网配置。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虚拟网络。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 获取网络安全组。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 将网络安全规则配置添加到网络安全组。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新网络安全组。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 将路由添加到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 创建 Azure SQL 数据库托管实例。  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| 返回有关 Azure SQL 托管数据库实例的信息。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 创建公共 IP 地址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 为虚拟网络网关创建 IP 配置 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 创建虚拟网络网关 |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 创建两个虚拟网络网关之间的连接。   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 创建新的 Azure SQL 数据库托管实例故障转移组。  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | 获取或列出托管实例故障转移组。| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | 执行托管实例故障转移组的故障转移。 | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组。 | 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal) 

没有适用于 Azure 门户的脚本。

---

## <a name="next-steps"></a>后续步骤

在本教程中，你已在两个托管实例之间配置了一个故障转移组。 你已了解如何：

> [!div class="checklist"]
> - 创建主托管实例
> - 创建辅助托管实例作为[故障转移组](sql-database-auto-failover-group.md)的一部分。 
> - 测试故障转移

继续学习下一篇快速入门，了解如何连接到托管实例，以及如何将数据库还原到托管实例： 

> [!div class="nextstepaction"]
> [连接到托管实例](sql-database-managed-instance-configure-vm.md)
> [将数据库还原到托管实例](sql-database-managed-instance-get-started-restore.md)


