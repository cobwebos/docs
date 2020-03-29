---
title: 虚拟网络
titleSuffix: Azure Cognitive Services
description: 为您的认知服务资源配置分层网络安全。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371216"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>配置 Azure 认知服务虚拟网络

Azure 认知服务提供分层安全模型。 此模型使您能够将认知服务帐户保护到特定的网络子集。 配置网络规则后，只有通过指定网络集请求数据的应用程序才能访问该帐户。 您可以通过请求筛选限制对资源的访问。 仅允许来自指定 IP 地址、IP 范围或[Azure 虚拟网络中](../virtual-network/virtual-networks-overview.md)子网列表的请求。 如果您对此产品感兴趣，则需要[请求预览访问](https://aka.ms/cog-svc-vnet-signup)。

当网络规则生效时访问认知服务资源的应用程序需要授权。 [Azure 活动目录](../active-directory/fundamentals/active-directory-whatis.md)（Azure AD） 凭据或有效的 API 密钥支持授权。

> [!IMPORTANT]
> 默认情况下，打开认知服务帐户的防火墙规则会阻止传入的数据请求。 为了允许请求通过，需要满足以下条件之一：
> * 请求应源自目标认知服务帐户的允许子网列表上的 Azure 虚拟网络 （VNet） 中的服务。 源自 VNet 的请求中的终结点需要设置为认知服务帐户的[自定义子域](cognitive-services-custom-subdomains.md)。
> * 或者请求应源自允许的 IP 地址列表。
>
> 被阻止的请求包括来自其他 Azure 服务、来自 Azure 门户、来自日志记录和指标服务等的请求。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>方案

要保护认知服务资源，应首先配置一条规则，以默认拒绝从所有网络（包括互联网流量）访问流量。 然后，应该配置相应的规则，以便为来自特定 VNet 的流量授予访问权限。 借助此配置，可为应用程序生成安全网络边界。 此外，还可以配置规则为来自特定公共 Internet IP 地址范围的流量授予访问权限，以便能够从特定的 Internet 或本地客户端建立连接。

网络规则在 Azure 认知服务的所有网络协议（包括 REST 和 WebSocket）上强制执行。 要使用 Azure 测试控制台等工具访问数据，必须配置显式网络规则。 您可以将网络规则应用于现有的认知服务资源，或者在创建新的认知服务资源时。 一旦应用网络规则，就会对所有请求强制实施这些规则。

## <a name="supported-regions-and-service-offerings"></a>支持的区域和服务产品

下面列出的认知服务的虚拟网络支持仅限于*美国中部欧盟、**美国中南部*、*美国东部*、*美国西部 2、**北欧*、*南非北部*、*西欧*、*印度中部*、*澳大利亚东部*、*美国西部*和*美国政府弗吉尼亚*Azure 地区。 如果此处未列出服务产品/服务，则不支持虚拟网络。

> [!div class="checklist"]
> * [异常检测器](./anomaly-detector/index.yml)
> * [计算机视觉](./computer-vision/index.yml)
> * [内容审阅者](./content-moderator/index.yml)
> * [自定义愿景](./custom-vision-service/index.yml)
> * [脸](./face/index.yml)
> * [表单识别器](./form-recognizer/index.yml)
> * [路易斯](./luis/index.yml)
> * [个性化体验创建服务](./personalizer/index.yml)
> * [文本分析](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)

下面列出的认知服务的虚拟网络支持仅限于*美国中部欧盟、**美国中南部*、*美国东部*、*美国西部 2、**全球*和*美国政府弗吉尼亚*Azure 区域。
> [!div class="checklist"]
> * [Translator 文本](./translator/index.yml)

## <a name="service-tags"></a>服务标记
除了支持上述服务的虚拟网络服务终结点外，认知服务还支持用于出站网络规则配置的服务标记。 以下服务包含在认知服务管理服务标记中。
> [!div class="checklist"]
> * [异常检测器](./anomaly-detector/index.yml)
> * [计算机视觉](./computer-vision/index.yml)
> * [内容审阅者](./content-moderator/index.yml)
> * [自定义愿景](./custom-vision-service/index.yml)
> * [脸](./face/index.yml)
> * [表单识别器](./form-recognizer/index.yml)
> * [路易斯](./luis/index.yml)
> * [个性化体验创建服务](./personalizer/index.yml)
> * [文本分析](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)
> * [Translator 文本](./translator/index.yml)
> * [语音服务](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>更改默认网络访问规则

默认情况下，认知服务资源接受来自任何网络上的客户端的连接。 若要限制为仅允许选定网络访问，必须先更改默认操作。

> [!WARNING]
> 更改网络规则可能会影响应用程序连接到 Azure 认知服务的能力。 除非还应用了**授予**访问权限的特定网络规则，否则将默认网络规则设置为“拒绝”会阻止对数据的所有访问****。 在将默认规则更改为拒绝访问之前，务必先使用网络规则对所有许可网络授予访问权限。 如果允许列出本地网络的 IP 地址，请确保从本地网络添加所有可能的传出公共 IP 地址。

### <a name="managing-default-network-access-rules"></a>管理默认网络访问规则

您可以通过 Azure 门户、PowerShell 或 Azure CLI 管理认知服务资源的默认网络访问规则。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 转到要保护的认知服务资源。

1. 选择名为 **"虚拟网络**"**的资源管理**菜单。

   ![虚拟网络选项](media/vnet/virtual-network-blade.png)

1. 默认情况下，要拒绝访问，请选择允许从**选定网络**进行访问。 单独设置 **"选定网络**"，仅由配置的**虚拟网络**或**地址范围**陪同 ，因此所有访问都有效被拒绝。 当所有访问被拒绝时，不允许尝试使用认知服务资源的请求。 Azure 门户、Azure PowerShell 或 Azure CLI 仍可用于配置认知服务资源。
1. 要允许来自所有网络的流量，请选择允许从**所有网络**进行访问。

   ![虚拟网络拒绝](media/vnet/virtual-network-deny.png)

1. 单击“保存”应用所做的更改。****

# <a name="powershell"></a>[电源外壳](#tab/powershell)

1. 安装[Azure PowerShell](/powershell/azure/install-az-ps)并[登录](/powershell/azure/authenticate-azureps)，或选择 **"试用"。**

1. 显示认知服务资源的默认规则的状态。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. 将默认规则设置为默认拒绝网络访问。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. 将默认规则设置为默认允许网络访问。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 安装[Azure CLI](/cli/azure/install-azure-cli)并[登录](/cli/azure/authenticate-azure-cli)，或选择 **"试用"。**

1. 显示认知服务资源的默认规则的状态。

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. 将默认规则设置为默认拒绝网络访问。

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. 将默认规则设置为默认允许网络访问。

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>允许从虚拟网络进行访问

您可以将认知服务资源配置为仅允许从特定子网进行访问。 允许的子网可能属于同一订阅中的 VNet 或其他订阅，包括属于其他 Azure 活动目录租户的订阅。

在 VNet 中为 Azure 认知服务启用[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。 服务终结点通过 Azure 认知服务服务的最佳路径路由流量。 子网和虚拟网络的标识也随每个请求进行传输。 然后，管理员可以为认知服务资源配置网络规则，以便从 VNet 中的特定子网接收请求。 通过这些网络规则授予访问权限的客户端必须继续满足认知服务资源访问数据的授权要求。

每个认知服务资源最多支持 100 个虚拟网络规则，这些规则可与[IP 网络规则](#grant-access-from-an-internet-ip-range)结合使用。

### <a name="required-permissions"></a>所需的权限

要将虚拟网络规则应用于认知服务资源，用户必须具有要添加的子网的适当权限。 所需的权限是默认*的"参与者"* 角色或*认知服务参与者*角色。 如果需要的权限也可以添加到自定义角色定义中。

认知服务资源和授予访问权限的虚拟网络可能位于不同的订阅中，包括属于其他 Azure AD 租户的订阅。

> [!NOTE]
> 目前，仅支持通过 Powershell、CLI 和 REST API 来配置对属于不同 Azure Active Directory 租户的虚拟网络中的子网授予访问权限的规则。 无法通过 Azure 门户配置此类规则，但可以在门户中查看此类规则。

### <a name="managing-virtual-network-rules"></a>管理虚拟网络规则

您可以通过 Azure 门户、PowerShell 或 Azure CLI 管理认知服务资源的虚拟网络规则。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 转到要保护的认知服务资源。

1. 选择名为 **"虚拟网络**"**的资源管理**菜单。

1. 检查并确保已选择允许从“所选网络”进行访问****。

1. 要授予使用现有网络规则访问虚拟网络的权限，请在**虚拟网络**下，选择 **"添加现有虚拟网络**"。

   ![添加现有 vNet](media/vnet/virtual-network-add-existing.png)

1. 选择**虚拟网络和****子网**选项，然后选择**启用**。

   ![添加现有 vNet 详细信息](media/vnet/virtual-network-add-existing-details.png)

1. 要创建新的虚拟网络并授予其访问权限，请选择"**添加新虚拟网络**"。

   ![添加新 vNet](media/vnet/virtual-network-add-new.png)

1. 提供创建新虚拟网络所需的信息，然后选择 **"创建**"。

   ![创建 vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > 如果以前未为所选虚拟网络和子网配置 Azure 认知服务的服务终结点，则可以将其配置为此操作的一部分。
    >
    > 目前，在创建规则期间，只会显示属于同一 Azure Active Directory 租户的虚拟网络供用户选择。 若要对属于其他租户的虚拟网络中的子网授予访问权限，请使用 Powershell、CLI 或 REST API。

1. 要删除虚拟网络或子网规则，请选择 **..."** 以打开虚拟网络或子网的上下文菜单，然后选择 **"删除**"。

   ![删除 vNet](media/vnet/virtual-network-remove.png)

1. 单击“保存”应用所做的更改。****

# <a name="powershell"></a>[电源外壳](#tab/powershell)

1. 安装[Azure PowerShell](/powershell/azure/install-az-ps)并[登录](/powershell/azure/authenticate-azureps)，或选择 **"试用"。**

1. 列出虚拟网络规则。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. 在现有虚拟网络和子网上为 Azure 认知服务启用服务终结点。

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. 为虚拟网络和子网添加网络规则。

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > 若要为属于其他 Azure AD 租户的 VNet 中的子网添加网络规则，请使用“/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name”格式的完全限定的 **VirtualNetworkResourceId** 参数。

1. 为虚拟网络和子网删除网络规则。

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 安装[Azure CLI](/cli/azure/install-azure-cli)并[登录](/cli/azure/authenticate-azure-cli)，或选择 **"试用"。**

1. 列出虚拟网络规则。

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. 在现有虚拟网络和子网上为 Azure 认知服务启用服务终结点。

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. 为虚拟网络和子网添加网络规则。

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > 若要为属于其他 Azure AD 租户的 VNet 中的子网添加规则，请使用“/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name”格式的完全限定的子网 ID。
    > 
    > 可以使用 **subscription** 参数检索属于其他 Azure AD 租户的 VNet 的子网 ID。

1. 为虚拟网络和子网删除网络规则。

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果****。

## <a name="grant-access-from-an-internet-ip-range"></a>允许从 Internet IP 范围进行访问

您可以配置认知服务资源以允许从特定的公共互联网 IP 地址范围进行访问。 此配置允许访问特定服务和本地网络，从而有效阻止一般互联网流量。

使用表单`16.17.18.0/24`中的[CIDR 表示法](https://tools.ietf.org/html/rfc4632)或作为单个 IP 地址（如`16.17.18.19`）提供允许的互联网地址范围。

   > [!Tip]
   > 不支持使用“/31”或“/32”前缀大小的小型地址范围。 这些范围应使用单独的 IP 地址规则配置。

IP 网络规则仅适用于**公共 Internet** IP 地址。 IP 规则不允许使用为专用网络保留的 IP 地址范围（如 [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) 中所定义）。 专用网络`10.*`包括以 和`172.16.*` - `172.31.*`开头的地址。 `192.168.*`

   > [!NOTE]
   > IP 网络规则对来自与认知服务资源相同的 Azure 区域的请求没有影响。 请使用[虚拟网络规则](#grant-access-from-a-virtual-network)来允许相同区域的请求。

目前仅支持 IPV4 地址。 每个认知服务资源最多支持 100 个 IP 网络规则，这些规则可能与[虚拟网络规则](#grant-access-from-a-virtual-network)结合使用。

### <a name="configuring-access-from-on-premises-networks"></a>配置从本地网络的访问

要使用 IP 网络规则从本地网络授予对认知服务资源的访问权限，必须标识网络使用的面向 Internet 的 IP 地址。 若要获得帮助，请联系网络管理员。

如果您正在本地使用[ExpressRoute](../expressroute/expressroute-introduction.md)进行公共对等互连或 Microsoft 对等互连，则需要标识 NAT IP 地址。 对于公共对等互连，默认情况下每个 ExpressRoute 电路使用两个 NAT IP 地址。 当流量进入 Microsoft Azure 网络主干网时，每个都应用于 Azure 服务流量。 对于 Microsoft 对等互连，使用的 NAT IP 地址是客户提供的，或者由服务提供商提供。 若要允许访问服务资源，必须在资源 IP 防火墙设置中允许这些公共 IP 地址。 若要查找公共对等互连 ExpressRoute 线路 IP 地址，请通过 Azure 门户[开具 ExpressRoute 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 详细了解[适用于 ExpressRoute 公共对等互连和 Microsoft 对等互连的 NAT](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)。

### <a name="managing-ip-network-rules"></a>管理 IP 网络规则

您可以通过 Azure 门户、PowerShell 或 Azure CLI 管理认知服务资源的 IP 网络规则。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 转到要保护的认知服务资源。

1. 选择名为 **"虚拟网络**"**的资源管理**菜单。

1. 检查并确保已选择允许从“所选网络”进行访问****。

1. 要授予对互联网 IP 范围的访问权限，请在**防火墙** > **地址范围**下输入 IP 地址或地址范围[（CIDR 格式](https://tools.ietf.org/html/rfc4632)）。 仅接受有效的公共 IP（非保留）地址。

   ![添加 IP 范围](media/vnet/virtual-network-add-ip-range.png)

1. 要删除 IP 网络规则，请选择地址范围<span class="docon docon-delete x-hidden-focus"></span>旁边的垃圾罐图标。

   ![删除 IP 范围](media/vnet/virtual-network-delete-ip-range.png)

1. 单击“保存”应用所做的更改。****

# <a name="powershell"></a>[电源外壳](#tab/powershell)

1. 安装[Azure PowerShell](/powershell/azure/install-az-ps)并[登录](/powershell/azure/authenticate-azureps)，或选择 **"试用"。**

1. 列出 IP 网络规则。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. 为单个 IP 地址添加网络规则。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. 为 IP 地址范围添加网络规则。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. 为单个 IP 地址删除网络规则。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. 为 IP 地址范围删除网络规则。

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 安装[Azure CLI](/cli/azure/install-azure-cli)并[登录](/cli/azure/authenticate-azure-cli)，或选择 **"试用"。**

1. 列出 IP 网络规则。

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. 为单个 IP 地址添加网络规则。

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. 为 IP 地址范围添加网络规则。

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. 为单个 IP 地址删除网络规则。

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. 为 IP 地址范围删除网络规则。

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果****。

## <a name="next-steps"></a>后续步骤

* 探索各种[Azure 认知服务](welcome.md)
* 了解有关[Azure 虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)的更多信息