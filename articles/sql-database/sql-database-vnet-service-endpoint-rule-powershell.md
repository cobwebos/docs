---
title: PowerShell for VNet 终结点和适用于单个和共用数据库的规则
description: 提供 PowerShell 脚本来创建和管理 Azure SQL 数据库和 Azure Synapse 的虚拟服务终结点。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, vanto
ms.date: 03/12/2019
tags: azure-synapse
ms.openlocfilehash: f61403ef50af209fdc6e811191d31ccc83f8da73
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191854"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell：为 SQL 创建虚拟服务终结点和 VNet 规则

*虚拟网络规则*是一种防火墙安全功能，用于控制 Azure [SQL 数据库](sql-database-technical-overview.md)中的单个数据库和弹性池的数据库服务器，或者[是否接受从虚拟网络中的](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)特定子网发送的通信。

> [!IMPORTANT]
> 本文适用于 Azure SQL server，以及在 azure SQL server 上创建的 Azure Synapse 中的 SQL 数据库和数据仓库。 为简单起见，在同时引用 SQL 数据库和 Azure Synapse 时使用 SQL 数据库。 本文不适用于 Azure SQL 数据库中的**托管实例**部署，因为它没有与之关联的服务终结点。

本文将提供并介绍执行以下操作的 PowerShell 脚本：

1. 在子网上创建 Microsoft Azure 虚拟服务终结点。
2. 将终结点添加到 Azure SQL 数据库服务器的防火墙，以创建虚拟网络规则。

用于创建规则的动机在： [AZURE SQL 数据库的虚拟服务终结点][sql-db-vnet-service-endpoint-rule-overview-735r]中进行了介绍。

> [!TIP]
> 如果只需访问或将 SQL 数据库的虚拟服务终结点类型名称添加到子网，则可以直接跳至 [PowerShell 脚本](#a-verify-subnet-is-endpoint-ps-100)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Azure SQL 数据库仍支持 PowerShell Azure 资源管理器模块，但所有将来的开发都适用于 Az .Sql 模块。 有关这些 cmdlet，请参阅[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令的参数完全相同。

## <a name="major-cmdlets"></a>主要 cmdlet

本文重点介绍**AzSqlServerVirtualNetworkRule** cmdlet，该 cmdlet 将子网终结点添加到 Azure SQL 数据库服务器的访问控制列表（ACL），从而创建规则。

以下列表显示了为对**AzSqlServerVirtualNetworkRule**的调用做好准备而必须运行的其他*主要*cmdlet 的顺序。 在本文中，这些调用出现在[脚本 3 虚拟网络规则](#a-script-30) 中：

1. [AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig)：创建子网对象。
2. [AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork)：创建虚拟网络，并为其提供子网。
3. [AzVirtualNetworkSubnetConfig：将](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig)虚拟服务终结点分配给子网。
4. [AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork)：保留对虚拟网络所做的更新。
5. [AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule)：子网为终结点后，会将子网作为虚拟网络规则添加到 Azure SQL 数据库服务器的 ACL 中。
   - 从 Azure RM PowerShell 模块 5.1.1 版开始，此模块提供参数 **-IgnoreMissingVNetServiceEndpoint**。

## <a name="prerequisites-for-running-powershell"></a>运行 PowerShell 的先决条件

- 你可以已登录到 Azure，例如通过[Azure 门户][http-azure-portal-link-ref-477t]。
- 已可以运行 PowerShell 脚本。

> [!NOTE]
> 请确保已为想要添加到服务器的 VNet/子网打开服务终结点，否则创建 VNet 防火墙规则将会失败。

## <a name="one-script-divided-into-four-chunks"></a>一个脚本划分为四个区块

我们的演示 PowerShell 脚本被划分为更小的脚本序列。 这种划分可使学习更为轻松且灵活性更大。 脚本必须在其指示的序列中运行。 如果现在没有时间运行脚本，我们的实际测试结果已在脚本 4 后显示。

<a name="a-script-10" />

### <a name="script-1-variables"></a>脚本 1：变量

第一个 PowerShell 脚本向变量分配值。 后续脚本将依赖于这些变量。

> [!IMPORTANT]
> 运行此脚本前，可以根据需要编辑这些值。 例如，如果已有资源组，则可能需要以分配的值编辑资源组名称。
>
> 应将订阅名称编辑到脚本中。

### <a name="powershell-script-1-source-code"></a>PowerShell 脚本 1 源代码

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>脚本 2：先决条件

此脚本为下一个脚本做准备，即终结点操作所在的位置。 此脚本将为你创建下面列出的各项（仅在这些项不存在的情况下）。 如果确认这些项已存在，则可以跳过脚本 2：

- Azure 资源组
- Azure SQL 数据库服务器

### <a name="powershell-script-2-source-code"></a>PowerShell 脚本 2 源代码

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";
    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else {
    Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; 
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>脚本 3：创建终结点和规则

此脚本将创建含有子网的虚拟网络。 然后，脚本向子网分配 **Microsoft.Sql** 终结点类型。 最后，脚本将子网添加到 SQL 数据库服务器的访问控制列表 (ACL)，从而创建规则。

### <a name="powershell-script-3-source-code"></a>PowerShell 脚本 3 源代码

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>脚本 4：清理

最后的这个脚本将删除为演示而创建的以前脚本的资源。 但是，该脚本在删除以下资源前将要求你确认：

- Azure SQL 数据库服务器
- Azure 资源组

可以在脚本 1 完成后随时运行脚本 4。

### <a name="powershell-script-4-source-code"></a>PowerShell 脚本 4 源代码

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno) {
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>验证子网是否是终结点。

可能已拥有已被分配 Microsoft.Sql 类型名称的子网，这就说明它已是虚拟服务终结点。 可以使用[Azure 门户][http-azure-portal-link-ref-477t]从终结点创建虚拟网络规则。

或者，如果不确定你的子网是否具有 Microsoft.Sql 类型名称， 则可以运行下面的 PowerShell 脚本执行以下操作：

1. 确定你的子网是否具有 Microsoft.Sql 类型名称。
2. （可选）如果它不存在，则分配类型名称。
    - 在对其应用缺少的类型名称前，脚本会要求你确认。

### <a name="phases-of-the-script"></a>脚本阶段

以下是 PowerShell 脚本的各个阶段：

1. 登录到你的 Azure 帐户，每个 PS 会话只需登录一次。  分配变量
2. 搜索你的虚拟网络，然后搜索你的子网。
3. 你的子网是否被标记为 Microsoft.Sql 终结点服务器类型？
4. 在你的子网上，添加类型名称 Microsoft.Sql 的虚拟服务终结点。

> [!IMPORTANT]
> 运行此脚本前，必须编辑分配给 $ 变量的值（在脚本顶部附近）。

### <a name="direct-powershell-source-code"></a>直接 PowerShell 源代码

此 PowerShell 脚本不会更新任何内容，除非在它要求你确认时响应“是”。 此脚本可以将类型名称 Microsoft.Sql 添加到你的子网。 但是脚本只有在你的子网缺少类型名称时才会尝试添加。

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
