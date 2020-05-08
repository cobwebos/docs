---
title: 配置 Azure 存储防火墙和虚拟网络 | Microsoft Docs
description: 配置存储帐户的分层网络安全性。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 911172bd6ef9c08419e74828657c8bdb2f8d1b30
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930635"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>配置 Azure 存储防火墙和虚拟网络

Azure 存储提供分层安全模型。 使用此模型可以根据所用网络的类型和子集，来保护和控制应用程序与企业环境所需的存储帐户访问级别。 配置网络规则后，只有通过指定的网络集请求数据的应用程序才能访问存储帐户。 可将存储帐户的访问权限限制给源自指定的 IP 地址、IP 范围，或 Azure 虚拟网络 (VNet) 中某个子网列表的请求。

存储帐户具有可通过 internet 访问的公共终结点。 你还可以[为存储帐户创建专用终结点，该终结点](storage-private-endpoints.md)将专用 IP 地址从 vnet 分配到存储帐户，并通过专用链接保护 vnet 和存储帐户之间的所有流量。 Azure 存储防火墙提供对你的存储帐户的公共终结点的访问控制访问。 使用专用终结点时，还可以使用防火墙阻止通过公共终结点进行的所有访问。 你的存储防火墙配置还允许选择 "受信任的 Azure 平台服务" 来安全地访问存储帐户。

在网络规则仍然生效的情况下访问存储帐户的应用程序需要获得适当的请求授权。 支持通过 Azure Active Directory (Azure AD) 凭据（适用于 Blob 和队列）、有效的帐户访问密钥或 SAS 令牌进行授权。

> [!IMPORTANT]
> 默认情况下，除非请求源自在 Azure 虚拟网络 (VNet) 中运行的服务或者源自允许的公共 IP 地址，否则启用存储帐户的防火墙规则会阻止数据传入请求。 被阻止的请求包括来自其他 Azure 服务、来自 Azure 门户、来自日志记录和指标服务等的请求。
>
> 可以通过允许来自托管服务实例的子网的流量，为从 VNet 内部运行的 Azure 服务授予访问权限。 此外，可以通过下面所述的[例外](#exceptions)机制，启用有限数量的方案。 若要通过 Azure 门户访问存储帐户中的数据，需要从设置的可信边界（IP 或 VNet）内的计算机进行访问。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>方案

若要保护存储帐户，应该先配置一个规则，以便在公共终结点上默认拒绝来自所有网络的流量（包括 Internet 流量）进行访问。 然后，应该配置相应的规则，以便为来自特定 VNet 的流量授予访问权限。 此外，还可以配置规则为来自特定公共 Internet IP 地址范围的流量授予访问权限，以便能够从特定的 Internet 或本地客户端建立连接。 借助此配置，可为应用程序生成安全网络边界。

可在同一存储帐户中，将允许从特定虚拟网络以及从公共 IP 地址范围进行访问的防火墙规则组合到一起。 可对现有的存储帐户应用存储防火墙规则，或者在创建新存储帐户时应用这些规则。

存储防火墙规则将应用到存储帐户的公共终结点。 不需要任何防火墙访问规则即可允许存储帐户的专用终结点的流量。 批准专用终结点的创建的过程授予对托管专用终结点的子网的流量的隐式访问权限。

对于面向 Azure 存储的所有网络协议（包括 REST 和 SMB），将强制实施网络规则。 若要使用 Azure 门户、存储资源管理器和 AZCopy 等工具访问数据，必须配置显式网络规则。

一旦应用网络规则，就会对所有请求强制实施这些规则。 用于向特定 IP 地址授予访问权限的 SAS 令牌可限制令牌持有者的访问权限，但不会越过已配置的网络规则授予新的访问权限。

虚拟机磁盘流量（包括装载和卸载操作以及磁盘 IO）不受网络规则影响。 对页 blob 的 REST 访问受网络规则保护。

经典存储帐户不支持防火墙和虚拟网络。

可通过创建例外，使用应用了网络规则的存储帐户中的非托管磁盘来备份和还原 VM。 此过程在本文的[例外](#exceptions)部分中记录。 防火墙例外不适用于托管磁盘，因为它们已由 Azure 托管。

## <a name="change-the-default-network-access-rule"></a>更改默认网络访问规则

默认情况下，存储帐户接受来自任何网络上客户端的连接。 若要限制为仅允许选定网络访问，必须先更改默认操作。

> [!WARNING]
> 更改网络规则可能会使应用程序无法正常连接到 Azure 存储。 除非还应用了**授予**访问权限的特定网络规则，否则将默认网络规则设置为“拒绝”会阻止对数据的所有访问****。 在将默认规则更改为拒绝访问之前，务必先使用网络规则对所有许可网络授予访问权限。

### <a name="managing-default-network-access-rules"></a>管理默认网络访问规则

可以通过 Azure 门户、PowerShell 或 CLIv2 管理存储帐户的默认网络访问规则。

#### <a name="azure-portal"></a>Azure 门户

1. 转至要保护的存储帐户。

1. 单击名为“防火墙和虚拟网络”**** 的设置菜单。

1. 若要在默认情况下拒绝访问，请选择允许从**所选网络**进行访问。 若要允许来自所有网络的流量，请选择允许从**所有网络**进行访问。

1. 单击“保存”**** 应用所做的更改。

#### <a name="powershell"></a>PowerShell

1. 安装 [Azure PowerShell](/powershell/azure/install-Az-ps) 并 [登录](/powershell/azure/authenticate-azureps)。

1. 显示存储帐户默认规则的状态。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. 将默认规则设置为默认拒绝网络访问。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. 将默认规则设置为默认允许网络访问。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. 安装 [Azure CLI](/cli/azure/install-azure-cli) 并 [登录](/cli/azure/authenticate-azure-cli)。

1. 显示存储帐户默认规则的状态。

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. 将默认规则设置为默认拒绝网络访问。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. 将默认规则设置为默认允许网络访问。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>允许从虚拟网络进行访问

可将存储帐户配置为仅允许从特定子网进行访问。 允许的子网可以属于同一订阅中的 VNet，也可以属于不同订阅（包括属于不同 Azure Active Directory 租户的订阅）中的 VNet。

在 VNet 内为 Azure 存储启用[服务终结点](/azure/virtual-network/virtual-network-service-endpoints-overview)。 服务终结点通过最佳路径将流量从 VNet 路由到 Azure 存储服务。 子网和虚拟网络的标识也随每个请求进行传输。 管理员随后可以配置存储帐户的网络规则，允许从 VNet 中的特定子网接收请求。 通过这些网络规则获得访问权限的客户端必须继续满足存储帐户的授权要求，才能访问数据。

每个存储帐户最多支持 100 条虚拟网络规则，这些规则可与 [IP 网络规则](#grant-access-from-an-internet-ip-range)组合使用。

### <a name="available-virtual-network-regions"></a>可用的虚拟网络区域

服务终结点一般在位于同一 Azure 区域的虚拟网络和服务实例之间运行。 将服务终结点与 Azure 存储配合使用时，此范围扩大到包含[配对区域](/azure/best-practices-availability-paired-regions)。 服务终结点可以在区域性故障转移期间提供连续性，并允许访问读取访问权限异地冗余存储 (RA-GRS) 实例。 允许从虚拟网络访问存储帐户的网络规则同样允许访问所有 RA-GRS 实例。

在计划区域性服务中断期间的灾难恢复时，应该在配对区域中提前创建 VNet。 为 Azure 存储启用服务终结点，并提供允许从这些备用虚拟网络进行访问的网络规则。 然后将这些规则应用于异地冗余存储帐户。

> [!NOTE]
> 服务终结点不适用于位于虚拟网络所在区域和指定区域对之外的流量。 可以将允许从虚拟网络访问存储帐户的网络规则仅应用于存储帐户主区域或指定配对区域中的存储帐户。

### <a name="required-permissions"></a>所需的权限

若要向存储帐户应用虚拟网络规则，用户必须对要添加的子网拥有适当的权限。 所需的权限为*向子网加入服务*权限，该权限包含在*存储帐户参与者*内置角色中。 该权限还可以添加到自定义角色定义中。

存储帐户和获得访问权限的虚拟网络可以位于不同的订阅中，包括属于不同 Azure AD 租户的订阅。

> [!NOTE]
> 目前，仅支持通过 Powershell、CLI 和 REST API 来配置对属于不同 Azure Active Directory 租户的虚拟网络中的子网授予访问权限的规则。 无法通过 Azure 门户配置此类规则，但可以在门户中查看此类规则。

### <a name="managing-virtual-network-rules"></a>管理虚拟网络规则

可以通过 Azure 门户、PowerShell 或 CLIv2 管理存储帐户的虚拟网络规则。

#### <a name="azure-portal"></a>Azure 门户

1. 转至要保护的存储帐户。

1. 单击名为“防火墙和虚拟网络”**** 的设置菜单。

1. 检查并确保已选择允许从“所选网络”进行访问****。

1. 若要使用新的网络规则向虚拟网络授予访问权限，请在“虚拟网络”下，单击“添加现有虚拟网络”，选择“虚拟网络”和“子网”选项，然后单击“添加”********************。 若要创建新的虚拟网络并授予其访问权限，请单击“添加新的虚拟网络”****。 提供创建新的虚拟网络所需的信息，然后单击“创建”****。

    > [!NOTE]
    > 如果之前没有为所选的虚拟网络和子网配置 Azure 存储的服务终结点，则可在执行此操作时进行配置。
    >
    > 目前，在创建规则期间，只会显示属于同一 Azure Active Directory 租户的虚拟网络供用户选择。 若要对属于其他租户的虚拟网络中的子网授予访问权限，请使用 Powershell、CLI 或 REST API。

1. 若要删除虚拟网络或子网规则，请单击“...”打开虚拟网络或子网的上下文菜单，然后单击“删除”********。

1. 单击“保存”**** 应用所做的更改。

#### <a name="powershell"></a>PowerShell

1. 安装 [Azure PowerShell](/powershell/azure/install-Az-ps) 并 [登录](/powershell/azure/authenticate-azureps)。

1. 列出虚拟网络规则。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. 在现有虚拟网络和子网上启用 Azure 存储的服务终结点。

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. 为虚拟网络和子网添加网络规则。

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > 若要为属于其他 Azure AD 租户的 VNet 中的子网添加网络规则，请使用“/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name”格式的完全限定的 **VirtualNetworkResourceId** 参数。

1. 为虚拟网络和子网删除网络规则。

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果****。

#### <a name="cliv2"></a>CLIv2

1. 安装 [Azure CLI](/cli/azure/install-azure-cli) 并 [登录](/cli/azure/authenticate-azure-cli)。

1. 列出虚拟网络规则。

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. 在现有虚拟网络和子网上启用 Azure 存储的服务终结点。

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. 为虚拟网络和子网添加网络规则。

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > 若要为属于其他 Azure AD 租户的 VNet 中的子网添加规则，请使用“/subscriptions/\<subscription-ID\>/resourceGroups/\<resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-name\>/subnets/\<subnet-name\>”格式的完全限定的子网 ID。
    >
    > 可以使用 **subscription** 参数检索属于其他 Azure AD 租户的 VNet 的子网 ID。

1. 为虚拟网络和子网删除网络规则。

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果****。

## <a name="grant-access-from-an-internet-ip-range"></a>允许从 Internet IP 范围进行访问

可以将存储帐户配置为允许从特定的公共 Internet IP 地址范围进行访问。 此配置向基于 Internet 的特定服务和本地网络授予访问权限，并阻止一般 Internet 流量。

使用 [CIDR 表示法](https://tools.ietf.org/html/rfc4632)以 *16.17.18.0/24* 的形式，或使用单独的 IP 地址（如 *16.17.18.19*）提供允许的 Internet 地址范围。

   > [!NOTE]
   > 不支持使用“/31”或“/32”前缀大小的小型地址范围。 这些范围应使用单独的 IP 地址规则配置。

IP 网络规则仅适用于**公共 Internet** IP 地址。 IP 规则不允许使用为专用网络保留的 IP 地址范围（如 [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) 中所定义）。 专用网络包括以 _10.*_、_172.16.*_ - _172.31.*_ 和 _192.168.*_ 开头的地址。

   > [!NOTE]
   > IP 网络规则对源自与存储帐户相同的 Azure 区域的请求不起作用。 请使用[虚拟网络规则](#grant-access-from-a-virtual-network)来允许相同区域的请求。

  > [!NOTE]
  > 在存储帐户所在的同一区域中部署的服务将使用专用 Azure IP 地址进行通信。 因此，不能根据特定的 Azure 服务的公共出站 IP 地址范围限制对这些服务的访问。

仅支持使用 IPV4 地址配置存储防火墙规则。

每个存储帐户最多支持 100 个 IP 网络规则。

### <a name="configuring-access-from-on-premises-networks"></a>配置从本地网络的访问

若要使用 IP 网络规则授予本地网络访问存储帐户的权限，则必须标识网络所用的面向 Internet 的 IP 地址。 若要获得帮助，请联系网络管理员。

如果是在本地使用 [ExpressRoute](/azure/expressroute/expressroute-introduction)，则在进行公共对等互连或 Microsoft 对等互连时，需标识所用的 NAT IP 地址。 进行公共对等互连时，每条 ExpressRoute 线路默认情况下会使用两个 NAT IP 地址。当流量进入 Microsoft Azure 网络主干时，会向 Azure 服务流量应用这些地址。 对于 Microsoft 对等互连，所用 NAT IP 地址要么由客户提供，要么由服务提供商提供。 若要允许访问服务资源，必须在资源 IP 防火墙设置中允许这些公共 IP 地址。 若要查找公共对等互连 ExpressRoute 线路 IP 地址，请通过 Azure 门户[开具 ExpressRoute 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 详细了解[适用于 ExpressRoute 公共对等互连和 Microsoft 对等互连的 NAT](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)。

### <a name="managing-ip-network-rules"></a>管理 IP 网络规则

可以通过 Azure 门户、PowerShell 或 CLIv2 管理存储帐户的 IP 网络规则。

#### <a name="azure-portal"></a>Azure 门户

1. 转至要保护的存储帐户。

1. 单击名为“防火墙和虚拟网络”**** 的设置菜单。

1. 检查并确保已选择允许从“所选网络”进行访问****。

1. 若要授予对 internet IP 范围的访问权限，请在 "**防火墙** > **地址范围**" 下输入 IP 地址或地址范围（采用 CIDR 格式）。

1. 若要删除某个 IP 网络规则，请单击该地址范围旁边的垃圾桶图标。

1. 单击“保存”**** 应用所做的更改。

#### <a name="powershell"></a>PowerShell

1. 安装 [Azure PowerShell](/powershell/azure/install-Az-ps) 并 [登录](/powershell/azure/authenticate-azureps)。

1. 列出 IP 网络规则。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. 为单个 IP 地址添加网络规则。

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. 为 IP 地址范围添加网络规则。

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. 为单个 IP 地址删除网络规则。

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. 为 IP 地址范围删除网络规则。

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果  。

#### <a name="cliv2"></a>CLIv2

1. 安装 [Azure CLI](/cli/azure/install-azure-cli) 并 [登录](/cli/azure/authenticate-azure-cli)。

1. 列出 IP 网络规则。

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. 为单个 IP 地址添加网络规则。

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. 为 IP 地址范围添加网络规则。

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. 为单个 IP 地址删除网络规则。

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. 为 IP 地址范围删除网络规则。

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果****。

## <a name="exceptions"></a>异常

在大多数情况下，网络规则有助于为应用程序与数据之间的连接创建安全环境。 不过，某些应用程序依赖于无法通过虚拟网络或 IP 地址规则单独隔离的 Azure 服务。 但是，必须授予此类服务访问存储的权限，才能完全实现应用程序的功能。 在这种情况下，可以使用“允许受信任的 Microsoft 服务...”设置来允许此类服务访问数据、日志或分析。******

### <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服务

某些 Microsoft 服务在网络规则中无法包含的网络中运行。 你可以向此类受信任的 Microsoft 服务授予对存储帐户的访问权限，同时为其他应用维护网络规则。 然后，这些受信任的服务将使用强身份验证安全连接到存储帐户。 我们为 Microsoft 服务启用了两种可信访问模式。

- 某些服务的资源**在注册到订阅**后，可在**同一订阅**中访问存储帐户以执行特定的操作，例如写入日志或备份。
- 可通过以下方式向某些服务的资源授予对存储帐户的显式访问权限：**将 RBAC 角色分配**到其系统分配的托管标识。


如果启用“允许受信任的 Microsoft 服务...”设置，则会向以下服务的、已注册到存储帐户所在的同一订阅的资源授予对有限一组操作的访问权限，如下所述：****

| 服务                  | 资源提供程序名称     | 允许的操作                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure 备份             | Microsoft.RecoveryServices | 在 IAAS 虚拟机中运行非托管磁盘的备份和还原。 （不是托管磁盘的必需操作）。 [了解详细信息](/azure/backup/backup-introduction-to-azure-backup)。 |
| Azure Data Box           | Microsoft.DataBox          | 允许使用 Data Box 将数据导入到 Azure。 [了解详细信息](/azure/databox/data-box-overview)。 |
| Azure 开发测试实验室       | Microsoft.DevTestLab       | 自定义映像创建和项目安装。 [了解详细信息](/azure/devtest-lab/devtest-lab-overview)。 |
| Azure 事件网格         | Microsoft.EventGrid        | 启用 Blob 存储事件发布并允许事件网格发布到存储队列。 了解有关 [blob 存储事件](/azure/event-grid/event-sources)和[发布到队列](/azure/event-grid/event-handlers)的信息。 |
| Azure 事件中心         | Microsoft.EventHub         | 使用事件中心捕获功能存档数据。 [了解更多](/azure/event-hubs/event-hubs-capture-overview)。 |
| Azure 文件同步          | Microsoft.StorageSync      | 使你能够将本地文件服务器转换为 Azure 文件共享的缓存。 允许多站点同步、快速灾难恢复和云端备份。 [了解详细信息](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | 为新的 HDInsight 群集预配默认文件系统的初始内容。 [了解详细信息](/azure/hdinsight/hdinsight-hadoop-use-blob-storage)。 |
| Azure 导入导出      | Microsoft.ImportExport     | 允许使用导入/导出服务将数据导入 Azure 以及从 Azure 导出数据。 [了解详细信息](/azure/storage/common/storage-import-export-service)。  |
| Azure Monitor            | Microsoft.Insights         | 允许将监视数据写入受保护的存储帐户，包括资源日志、Azure Active Directory 登录和审核日志以及 Microsoft Intune 日志。 [了解详细信息](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security)。 |
| Azure 网络         | Microsoft.Network          | 存储和分析网络流量日志。 [了解详细信息](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)。 |
| Azure Site Recovery      | Microsoft.SiteRecovery     | 使用启用了防火墙的缓存、源或目标存储帐户时，请启用复制，以实现 Azure IaaS 虚拟机的灾难恢复。  [了解详细信息](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)。 |

如果已显式[将 RBAC 角色分配](storage-auth-aad.md#assign-rbac-roles-for-access-rights)到以下服务的特定实例的[系统分配的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，则“允许受信任的 Microsoft 服务...”设置也允许该资源实例访问存储帐户。**** 在这种情况下，该实例的访问权限范围对应于分配到托管标识的 RBAC 角色。

| 服务                        | 资源提供程序名称                 | 目标            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure 认知搜索         | Microsoft.Search/searchServices        | 使认知搜索服务可以访问用于索引、处理和查询的存储帐户。 |
| Azure 容器注册表任务 | Microsoft.ContainerRegistry/registries | 在构建容器映像时，ACR 任务可以访问存储帐户。 |
| Azure 数据工厂             | Microsoft.DataFactory/factories        | 用于通过 ADF 运行时访问存储帐户。 |
| Azure Data Share               | Microsoft.DataShare/accounts           | 允许通过数据共享访问存储帐户。 |
| Azure 逻辑应用               | Microsoft.Logic/workflows              | 使逻辑应用能够访问存储帐户。 [了解详细信息](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity)。 |
| Azure 机器学习服务 | Microsoft.MachineLearningServices      | 授权 Azure 机器学习工作区将试验输出、模型和日志写入 Blob 存储并读取数据。 [了解详细信息](/azure/machine-learning/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace)。 | 
| Azure SQL 数据仓库       | Microsoft.Sql                          | 用于通过 PolyBase 从特定 SQL 数据库实例导入和导出数据。 [了解详细信息](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)。 |
| Azure 流分析         | Microsoft.StreamAnalytics             | 用于将流式处理作业中的数据写入 Blob 存储。 此功能目前以预览版提供。 [了解详细信息](/azure/stream-analytics/blob-output-managed-identity)。 |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces          | 允许从 Synapse Analytics 访问 Azure 存储中的数据。 |


### <a name="storage-analytics-data-access"></a>存储分析数据访问

在某些情况下，从网络边界外部需要对读取资源日志和指标的访问。 配置受信任服务对存储帐户的访问权限时，可以允许对日志文件和/或指标表进行读取访问。 [详细了解如何使用存储分析。](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>管理例外

可以通过 Azure 门户、PowerShell 或 Azure CLI v2 管理网络规则例外。

#### <a name="azure-portal"></a>Azure 门户

1. 转至要保护的存储帐户。

1. 单击名为“防火墙和虚拟网络”**** 的设置菜单。

1. 检查并确保已选择允许从“所选网络”进行访问****。

1. 在 "**例外**" 下，选择要授予的例外。

1. 单击“保存”**** 应用所做的更改。

#### <a name="powershell"></a>PowerShell

1. 安装 [Azure PowerShell](/powershell/azure/install-Az-ps) 并 [登录](/powershell/azure/authenticate-azureps)。

1. 显示存储帐户的网络规则例外。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. 配置存储帐户的网络规则例外。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. 删除存储帐户的网络规则例外。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则，删除例外操作不会有任何效果****。

#### <a name="cliv2"></a>CLIv2

1. 安装 [Azure CLI](/cli/azure/install-azure-cli) 并 [登录](/cli/azure/authenticate-azure-cli)。

1. 显示存储帐户的网络规则例外。

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. 配置存储帐户的网络规则例外。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. 删除存储帐户的网络规则例外。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则，删除例外操作不会有任何效果****。

## <a name="next-steps"></a>后续步骤

详细了解[服务终结点](/azure/virtual-network/virtual-network-service-endpoints-overview)中的 Azure 网络服务终结点。

深入了解[azure 存储安全指南](../blobs/security-recommendations.md)中的 azure 存储安全性。
