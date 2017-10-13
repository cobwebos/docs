---
title: "配置 Azure 存储防火墙和虚拟网络（预览版）| Microsoft Docs"
description: "配置存储帐户的分层网络安全性。"
services: storage
documentationcenter: 
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/25/2017
ms.author: cbrooks
ms.openlocfilehash: de52e9cb32e28d2f40a56743ed759b5d5d0a63f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks-preview"></a>配置 Azure 存储防火墙和虚拟网络（预览版）
Azure 存储提供一种分层安全模型，用于保护存储帐户，使其仅可供一组特定的许可网络访问。  配置网络规则时，只有来自许可网络的应用程序才能访问存储帐户。  从许可网络进行调用时，应用程序仍需获得正确的授权（有效的访问密钥或 SAS 令牌）才能访问存储帐户。

## <a name="preview-availability-and-support"></a>预览版的可用性与支持
存储防火墙和虚拟网络为预览版。  此功能当前可用于以下区域的新存储帐户或现有存储帐户：
- 美国东部
- 美国西部
- 美国西部 2
- 美国中西部
- 澳大利亚东部
- 澳大利亚东南部

> [!NOTE]
> 预览期间不支持生产工作负荷。
>

## <a name="scenarios"></a>方案
可以将存储帐户配置为默认拒绝来自所有网络的流量（包括 Internet 流量）的访问。  可以向来自特定 Azure 虚拟网络的流量授予访问权限，为应用程序构建安全的网络边界。  也可以向公共 Internet IP 地址范围授予访问权限，支持来自特定 Internet 或本地客户端的连接。

对于面向 Azure 存储的所有网络协议（包括 REST 和 SMB），将强制实施网络规则。  强制实施网络规则后，如果从 Azure 门户、存储资源管理器和 AZCopy 等工具访问数据，则需要授予访问权限的显式网络规则。

网络规则可以应用于现有存储帐户，也可以在新建存储帐户的过程中应用。

一旦应用网络规则，就会对所有请求强制实施这些规则。  用于向特定 IP 地址服务授予访问权限的 SAS 令牌可**限制**令牌持有者的访问权限，但不越过已配置的网络规则授予新的访问权限。 

虚拟机磁盘流量（包括装载和卸载操作以及磁盘 IO）**不**受网络规则影响。  在预览期间，不支持为受保护的存储帐户备份非托管磁盘。  对页 blob（用于虚拟机磁盘）的 REST 访问受网络规则保护。

经典存储帐户**不**支持防火墙和虚拟网络。

## <a name="change-the-default-network-access-rule"></a>更改默认网络访问规则
默认情况下，存储帐户接受来自任何网络上客户端的连接。  若要限制为仅允许选定网络访问，必须先更改默认操作。

> [!WARNING]
> 更改网络规则可能会使应用程序无法正常连接到 Azure 存储。  将默认网络规则设置为“拒绝”会阻止对数据的所有访问，除非还应用了*授予*访问权限的特定网络规则。  在将默认规则更改为拒绝访问之前，务必先使用网络规则对所有许可网络授予访问权限。
>

#### <a name="azure-portal"></a>Azure 门户
1. 导航到要保护的存储帐户。  
> [!NOTE]
> 确保存储帐户位于公共预览版支持的区域之一。
>

2. 单击名为“防火墙和虚拟网络”的设置菜单。
3. 若要默认拒绝访问，请选择允许从“所选网络”进行访问。  若要允许来自所有网络的流量，请选择允许从“所有网络”进行访问。
4. 单击“保存”应用所做的更改。

#### <a name="powershell"></a>PowerShell
1. 安装最新的 [Azure PowerShell](/powershell/azure/install-azurerm-ps) 并[登录](/powershell/azure/authenticate-azureps)。

2. 显示存储帐户默认规则的状态。
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. 将默认规则设置为默认拒绝网络访问。  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. 将默认规则设置为默认允许网络访问。
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [安装 Azure CLI 2.0](/cli/azure/install-azure-cli) 并[登录](/cli/azure/authenticate-azure-cli)。
2. 显示存储帐户默认规则的状态。
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkAcls.defaultAction
```

3. 将默认规则设置为默认拒绝网络访问。  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. 将默认规则设置为默认允许网络访问。
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>允许从虚拟网络进行访问
可以将存储帐户配置为仅允许从特定的 Azure 虚拟网络进行访问。 

通过在虚拟网络内为 Azure 存储启用[服务终结点](/azure/virtual-network/virtual-network-service-endpoints-overview)，可确保以最佳方式将流量路由到 Azure 存储服务。 虚拟网络和子网的标识也随每个请求进行传输。  随后，管理员可以配置存储帐户的网络规则，允许从虚拟网络中的特定子网接收请求。  通过这些网络规则获得访问权限的客户端必须继续满足存储帐户的授权要求，才能访问数据。

每个存储帐户最多可支持 100 个虚拟网络规则，这些规则可与 [IP 网络规则](#grant-access-from-an-internet-ip-range)组合使用。

### <a name="available-virtual-network-regions"></a>可用的虚拟网络区域
服务终结点一般在位于同一 Azure 区域的虚拟网络和服务实例之间运行。  对 Azure 存储使用服务终结点时，此范围扩大到包含[配对区域](/azure/best-practices-availability-paired-regions)。  这样一来，不仅可以在区域性故障转移期间提供连续性，还允许对只读异地冗余存储 (RA-GRS) 实例进行无缝访问。  允许从虚拟网络访问存储帐户的网络规则同样允许访问所有 RA-GRS 实例。

在区域性服务中断期间计划灾难恢复时，应该在配对区域中提前预配虚拟网络。 应启用 Azure 存储的服务终结点，并向异地冗余存储帐户应用允许从这些备用虚拟网络进行访问的网络规则。

> [!NOTE]
> 服务终结点不适用于位于虚拟网络所在区域和指定区域对之外的流量。  允许从虚拟网络访问存储帐户的网络规则只能应用于存储帐户主区域或指定配对区域中的虚拟网络。
>

### <a name="required-permissions"></a>所需的权限
若要向存储帐户应用虚拟网络规则，用户必须对要添加的子网拥有“向子网加入服务”的权限。  此权限包含在“存储帐户参与者”内置角色中，可将此权限添加到自定义角色定义。

存储帐户和获得访问权限的虚拟网络**可以**位于不同的订阅中，但这些订阅必须属于同一个 Azure Active Directory 租户。

### <a name="managing-virtual-network-rules"></a>管理虚拟网络规则
可以通过 Azure 门户、PowerShell 或 CLIv2 管理存储帐户的虚拟网络规则。

#### <a name="azure-portal"></a>Azure 门户
1. 导航到要保护的存储帐户。  
2. 单击名为“防火墙和虚拟网络”的设置菜单。
3. 确保已选择允许从“所选网络”进行访问。
4. 若要使用新的网络规则向虚拟网络授予访问权限，请在“虚拟网络”下，单击“添加现有项”以选择现有虚拟网络和子网，然后单击“添加”。  若要创建新的虚拟网络，并向其授予访问权限，请单击“添加新项”，提供创建新虚拟网络所需的信息，然后单击“创建”。

> [!NOTE]
> 如果之前没有为所选的虚拟网络和子网配置 Azure 存储的服务终结点，可以在执行此操作时配置。
>

5. 若要删除虚拟网络或子网规则，请单击“...”打开虚拟网络或子网的上下文菜单，单击“删除”。
6. 单击“保存”应用所做的更改。

#### <a name="powershell"></a>PowerShell
1. 安装最新的 [Azure PowerShell](/powershell/azure/install-azurerm-ps) 并[登录](/powershell/azure/authenticate-azureps)。
2. 列出虚拟网络规则
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. 在现有虚拟网络和子网上启用 Azure 存储的服务终结点
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. 为虚拟网络和子网添加网络规则。  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. 为虚拟网络和子网删除网络规则。  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果。
>

#### <a name="cliv2"></a>CLIv2
1. [安装 Azure CLI 2.0](/cli/azure/install-azure-cli) 并[登录](/cli/azure/authenticate-azure-cli)。
2. 列出虚拟网络规则
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. 在现有虚拟网络和子网上启用 Azure 存储的服务终结点
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. 为虚拟网络和子网添加网络规则。  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "TestVNET" --name "default" --query id --output tsv)
az storage account network-rule add --resource-group myresourcegroup --account-name mystorageaccount --subnet $subnetid
```

4. 为虚拟网络和子网删除网络规则。 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "TestVNET" --name "default" --query id --output tsv)
az storage account network-rule remove --resource-group myresourcegroup --account-name mystorageaccount --subnet $subnetid
```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果。
>

## <a name="grant-access-from-an-internet-ip-range"></a>允许从 Internet IP 范围进行访问
可以将存储帐户配置为允许从特定的公共 Internet IP 地址范围进行访问。  此配置允许在一般 Internet 流量受阻时，向基于 Internet 的特定服务和本地网络授予访问权限。

许可的 Internet 地址范围可以用 [CIDR 表示法](https://tools.ietf.org/html/rfc4632)以 *16.17.18.0/24* 的形式表示，也可以用单独的 IP 地址（如 *16.17.18.19*）表示。

> [!NOTE]
> 不支持使用“/31”或“/32”前缀大小的小型地址范围。  这些范围应使用单独的 IP 地址规则配置。
>

IP 网络规则仅适用于**公共 Internet** IP 地址。  IP 规则不允许为专用网络保留的 IP 地址范围（如 RFC 1918 中所定义）。  专用网络包括以 *10.\**、*172.16.\** 和 *192.168.\** 开头的地址。

目前仅支持 IPV4 地址。

每个存储帐户最多可支持 100 个 IP 网络规则，这些规则可与 [虚拟网络规则](#grant-access-from-a-virtual-network)组合使用

### <a name="configuring-access-from-on-premises-networks"></a>配置从本地网络的访问
若要使用 IP 网络规则授予本地网络访问存储帐户的权限，则必须标识网络所用的面向 Internet 的 IP 地址。  若要获得帮助，请联系网络管理员。

如果网络使用 [ExpressRoute](/azure/expressroute/expressroute-introduction) 连接到 Azure 网络，那么每条线路在 Microsoft Edge 均配置有两个公共 IP 地址，用于通过 [Azure 公共对等互连](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains)连接到 Azure 存储等 Microsoft 服务。  若要允许从线路访问 Azure 存储，则必须为线路的公共 IP 地址创建 IP 网络规则。  若要查找 ExpressRoute 线路的公共 IP 地址，请通过 Azure 门户[开具 ExpressRoute 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。


### <a name="managing-ip-network-rules"></a>管理 IP 网络规则
可以通过 Azure 门户、PowerShell 或 CLIv2 管理存储帐户的 IP 网络规则。

#### <a name="azure-portal"></a>Azure 门户
1. 导航到要保护的存储帐户。  
2. 单击名为“防火墙和虚拟网络”的设置菜单。
3. 确保已选择允许从“所选网络”进行访问。
4. 若要向 Internet IP 范围授予访问权限，请在“防火墙”的“地址范围”下输入 IP 地址或地址范围（采用 CIDR 格式）。
5. 若要删除 IP 网络规则，请单击“...”打开该规则的上下文菜单，单击“删除”。
6. 单击“保存”应用所做的更改。

#### <a name="powershell"></a>PowerShell
1. 安装最新的 [Azure PowerShell](/powershell/azure/install-azurerm-ps) 并[登录](/powershell/azure/authenticate-azureps)。
2. 列出 IP 网络规则。
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. 为单个 IP 地址添加网络规则。  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. 为 IP 地址范围添加网络规则。  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. 为单个 IP 地址删除网络规则。 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. 为 IP 地址范围删除网络规则。  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果。
>

#### <a name="cliv2"></a>CLIv2
1. [安装 Azure CLI 2.0](/cli/azure/install-azure-cli) 并[登录](/cli/azure/authenticate-azure-cli)。
2. 列出 IP 网络规则
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. 为单个 IP 地址添加网络规则。
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. 为 IP 地址范围添加网络规则。  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. 为单个 IP 地址删除网络规则。  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. 为 IP 地址范围删除网络规则。  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果。
>

## <a name="exceptions"></a>异常
在大多数情况下，网络规则可以实现安全的网络配置，但在某些情况下，必须允许一些例外才能启用完整功能。  可以为存储帐户针对受信任的 Microsoft 服务和存储分析数据访问配置例外。

### <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服务
某些与存储帐户交互的 Microsoft 服务在网络上运行，但这些网络无法通过网络规则获得访问权限。 

若要允许此类服务按预期方式工作，可以允许受信任的 Microsoft 服务集绕过网络规则。 然后，这些服务使用强身份验证访问存储帐户。

启用“受信任的 Microsoft 服务”例外后，以下服务（在订阅中注册后）有权访问存储帐户：

|服务|资源提供程序名称|目的|
|:------|:---------------------|:------|
|Azure 开发测试实验室|Microsoft.DevTestLab|自定义映像创建和项目安装。  [了解详细信息](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-overview)。|
|Azure 事件网格|Microsoft.EventGrid|启用 Blob 存储事件发布。  [了解详细信息](https://docs.microsoft.com/en-us/azure/event-grid/overview)。|
|Azure 事件中心|Microsoft.EventHub|使用事件中心捕获功能存档数据。  [了解详细信息](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-capture-overview)。|
|Azure HDInsight|Microsoft.HDInsight|群集预配和安装。  [了解详细信息](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-blob-storage)。|
|Azure 网络|Microsoft.Networking|存储和分析网络流量日志。  [了解详细信息](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-packet-capture-overview)。|
|Azure SQL 数据仓库|Microsoft.Sql|数据导入和导出。  [了解详细信息](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-overview-load#load-from-azure-blob-storage)。|
||||

### <a name="storage-analytics-data-access"></a>存储分析数据访问
在某些情况下，需要从网络边界外访问读取诊断日志和指标。  可以允许网络规则例外读取访问存储帐户日志文件和/或指标表。 [详细了解如何使用存储分析。](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>管理例外
可以通过 Azure 门户、PowerShell 或 Azure CLI v2 管理网络规则例外。

#### <a name="azure-portal"></a>Azure 门户
1. 导航到要保护的存储帐户。  
2. 单击名为“防火墙和虚拟网络”的设置菜单。
3. 确保已选择允许从“所选网络”进行访问。
4. 在“例外”下，选择要允许的例外。
5. 单击“保存”应用所做的更改。

#### <a name="powershell"></a>PowerShell
1. 安装最新的 [Azure PowerShell](/powershell/azure/install-azurerm-ps) 并[登录](/powershell/azure/authenticate-azureps)。
2. 显示存储帐户的网络规则例外。
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. 配置存储帐户的网络规则例外。
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. 删除存储帐户的网络规则例外。
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则，删除例外操作不会有任何效果。
>

#### <a name="cliv2"></a>CLIv2
1. [安装 Azure CLI 2.0](/cli/azure/install-azure-cli) 并[登录](/cli/azure/authenticate-azure-cli)。
2. 显示存储帐户的网络规则例外。
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkAcls.bypass
```

3. 配置存储帐户的网络规则例外。
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. 删除存储帐户的网络规则例外。
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则，删除例外操作不会有任何效果。
>

## <a name="next-steps"></a>后续步骤
详细了解[服务终结点](/azure/virtual-network/virtual-network-service-endpoints-overview)中的 Azure 网络服务终结点。

深入了解 [Azure 存储安全指南](storage-security-guide.md)中的 Azure 存储安全。
