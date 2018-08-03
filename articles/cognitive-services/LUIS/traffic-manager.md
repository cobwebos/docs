---
title: 使用 Microsoft Azure 流量管理器增加语言理解 (LUIS) 中的终结点配额 - Azure | Microsoft Docs
description: 使用 Microsoft Azure 流量管理器将终结点配额分布在语言理解 ( LUIS ) 的多个订阅中，以便增加终结点配额
author: v-geberr
manager: kaiqb
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/07/2018
ms.author: v-geberr
ms.openlocfilehash: 8c8228b13c972c65596f0389e2fdfde585f8a742
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110307"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>使用 Microsoft Azure 流量管理器管理密钥之间的终结点配额
语言理解 (LUIS) 提供增加终结点请求配额的功能，可超出单个密钥的配额。 可通过以下方法实现此功能：为 LUIS 创建多个密钥，并在“资源和密钥”部分中的“发布”页面上将其添加到 LUIS 应用程序。 

客户端应用程序必须管理密钥之间的流量。 LUIS 不执行此操作。 

本文介绍如何使用 Azure [流量管理器][traffic-manager-marketing]管理密钥之间的流量。 必须具有已训练和已发布的 LUIS 应用。 如果还没有，请按照预生成的域[快速入门](luis-get-started-create-app.md)操作。 

## <a name="connect-to-powershell-in-the-azure-portal"></a>在 Azure 门户中连接到 PowerShell
在 [Azure][azure-portal] 门户中，打开 PowerShell 窗口。 PowerShell 窗口的图标是顶部导航栏中的“>_”。 从门户中使用 PowerShell，即表示已获得最新 PowerShell 版本并且通过了身份验证。 门户中的 PowerShell 需要 [Azure 存储](https://azure.microsoft.com/services/storage/)帐户。 

![打开 Powershell 窗口的 Azure 门户的屏幕截图](./media/traffic-manager/azure-portal-powershell.png)

以下各节使用[流量管理器 PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/?view=azurermps-6.2.0#traffic_manager)。

## <a name="create-azure-resource-group-with-powershell"></a>使用 PowerShell 创建 Azure 资源组
创建 Azure 资源前，请创建包含所有资源的资源组。 将资源组命名为 `luis-traffic-manager`，并使用区域 `West US`。 资源组的区域可存储有关组的元数据。 如果它们位于另一区域，则不会拖慢资源。 

使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.2.0) cmdlet 创建资源组：

```PowerShell
New-AzureRmResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>创建 LUIS 密钥以增加总终结点配额
1. 在 Azure 门户中，创建两个语言理解密钥，一个位于 `West US`，一个位于 `East US`。 使用上一节中创建的名为 `luis-traffic-manager` 现有资源组。 

    ![luis-traffic-manager 资源组中带有两个 LUIS 密钥的 Azure 门户的屏幕截图](./media/traffic-manager/luis-keys.png)

2. 在 [LUIS][LUIS] 网站中的“发布”页上，将密钥添加到应用中，然后重新发布该应用。 

    ![“发布”页上有两个 LUIS 密钥的 LUIS 门户的屏幕截图](./media/traffic-manager/luis-keys-in-luis.png)

    “终结点”列中的示例 URL 使用具有终结点密钥的 GET 请求作为查询参数。 复制这两个新密钥的终结点 URL。 本文后面的流量管理器配置中会用到它们。

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>使用流量管理器管理密钥之间的 LUIS 终结点请求
流量管理器为终结点创建新的 DNS 访问点。 它并不充当网关或代理，而是严格处于 DNS 级别。 此示例不会更改任何 DNS 记录。 它使用 DNS 库与流量管理器进行通信，以获取该特定请求的正确终结点。 针对 LUIS 的每个请求首先需要流量管理器请求来确定使用哪个 LUIS 终结点。 

### <a name="polling-uses-luis-endpoint"></a>轮询会使用 LUIS 终结点
流量管理器定期轮询终结点，以确保终结点仍然可用。 轮询的流量管理器 URL 需要能够通过 GET 请求访问，并返回 200。 “发布”页上的终结点 URL 可执行此操作。 由于每个终结点密钥具有不同的路由和查询字符串参数，因此每个终结点密钥需要不同的轮询路径。 流量管理器每次轮询时都会使用配额请求。 LUIS 终结点的查询字符串参数“q”是发送给 LUIS 的陈述。 此参数不用于发送陈述，而是用于将流量管理器轮询添加到 LUIS 终结点日志，以用作调试技术并同时对流量管理器进行配置。

由于每个 LUIS 终结点需要自己的路径，因此也需要其自己的流量管理器配置文件。 若要跨配置文件进行管理，请创建[嵌套流量管理器](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles)体系结构。 一个父配置文件指向子配置文件，并管理它们之间的流量。

配置流量管理器后，请记得更改路径以使用 logging = false 查询字符串参数，使日志不会被轮询填满。

## <a name="configure-traffic-manager-with-nested-profiles"></a>使用嵌套配置文件配置流量管理器
以下各节创建了两个子配置文件，一个用于东部 LUIS 密钥，一个用于西部 LUIS 密钥。 然后创建了一个父配置文件，并将两个子配置文件添加到该父配置文件。 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>使用 PowerShell 创建美国东部流量管理器配置文件
若要创建美国东部流量管理器配置文件，其步骤如下：创建配置文件、添加终结点，然后设置终结点。 流量管理器配置文件可具有多个终结点，但每个终结点的验证路径相同。 由于东部和西部订阅的 LUIS 终结点 URL 因区域和终结点密钥而异，因此每个 LUIS 终结点必须是配置文件中的单个终结点。 

1. 使用 [New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile?view=azurermps-6.2.0) cmdlet 创建配置文件

    使用以下 cmdlet 创建配置文件。 请确保更改 `appIdLuis` 和 `subscriptionKeyLuis`。 subscriptionKey 用于美国东部 LUIS 密钥。 如果路径（包括 LUIS 应用 ID 和终结点密钥）不正确，则流量管理器轮询的状态为 `degraded`，因为流量管理无法成功请求 LUIS 终结点。 请确保 `q` 的值为 `traffic-manager-east`，以便在 LUIS 终结点日志中查看此值。

    ```PowerShell
    $eastprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    此表介绍了 cmdlet 中的每个变量：
    
    |配置参数|变量名或值|目的|
    |--|--|--|
    |-Name|luis-profile-eastus|Azure 门户中的流量管理器名称|
    |-ResourceGroupName|luis-traffic-manager|在上一节中创建|
    |-TrafficRoutingMethod|性能|有关详细信息，请参阅[流量管理器路由方法][routing-methods]。 如果使用性能，则对流量管理器的 URL 请求必须来自用户的区域。 如果通过聊天机器人或其他应用程序，则聊天机器人应负责模拟对流量管理器的调用中的区域。 |
    |-RelativeDnsName|luis-dns-eastus|这是服务 luis-dns-eastus.trafficmanager.net 的子域|
    |-Ttl|30|轮询间隔，30 秒|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|LUIS 的端口和协议为 HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|将 <appIdLuis> 和 <subscriptionKeyLuis> 替换为自己的值。|
    
    成功请求没有响应。

2. 使用 [Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/add-azurermtrafficmanagerendpointconfig?view=azurermps-6.2.0) cmdlet 添加美国东部终结点

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    此表介绍了 cmdlet 中的每个变量：

    |配置参数|变量名或值|目的|
    |--|--|--|
    |-EndpointName|luis-east-endpoint|配置文件下显示的终结点名称|
    |-TrafficManagerProfile|$eastprofile|使用步骤 1 中创建的配置文件对象|
    |-Type|ExternalEndpoints|有关详细信息，请参阅[流量管理器终结点][traffic-manager-endpoints] |
    |-Target|eastus.api.cognitive.microsoft.com|这是 LUIS 终结点的域。|
    |-EndpointLocation|“eastus”|终结点的区域|
    |-EndpointStatus|已启用|创建时启用终结点|

    成功响应如下所示：

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. 使用 [Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/set-azurermtrafficmanagerprofile?view=azurermps-6.2.0) cmdlet 设置美国东部终结点

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    成功响应与步骤 2 的响应相同。

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>使用 PowerShell 创建美国西部流量管理器配置文件
若要创建美国西部流量管理器配置文件，请按照相同的步骤操作：创建配置文件、添加终结点，然后设置终结点。

1. 使用 [New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0) cmdlet 创建配置文件

    使用以下 cmdlet 创建配置文件。 请确保更改 `appIdLuis` 和 `subscriptionKeyLuis`。 subscriptionKey 用于美国东部 LUIS 密钥。 如果路径（包括 LUIS 应用 ID 和终结点密钥）不正确，则流量管理器轮询的状态为 `degraded`，因为流量管理无法成功请求 LUIS 终结点。 请确保 `q` 的值为 `traffic-manager-west`，以便在 LUIS 终结点日志中查看此值。

    ```PowerShell
    $westprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    此表介绍了 cmdlet 中的每个变量：
    
    |配置参数|变量名或值|目的|
    |--|--|--|
    |-Name|luis-profile-westus|Azure 门户中的流量管理器名称|
    |-ResourceGroupName|luis-traffic-manager|在上一节中创建|
    |-TrafficRoutingMethod|性能|有关详细信息，请参阅[流量管理器路由方法][routing-methods]。 如果使用性能，则对流量管理器的 URL 请求必须来自用户的区域。 如果通过聊天机器人或其他应用程序，则聊天机器人应负责模拟对流量管理器的调用中的区域。 |
    |-RelativeDnsName|luis-dns-westus|这是服务 luis-dns-westus.trafficmanager.net 的子域|
    |-Ttl|30|轮询间隔，30 秒|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|LUIS 的端口和协议为 HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|将 <appId> 和 <subscriptionKey> 替换为自己的值。 请记住此终结点密钥与东部终结点密钥不同|
    
    成功请求没有响应。

2. 使用 [Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0) cmdlet 添加美国西部终结点

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    此表介绍了 cmdlet 中的每个变量：

    |配置参数|变量名或值|目的|
    |--|--|--|
    |-EndpointName|luis-west-endpoint|配置文件下显示的终结点名称|
    |-TrafficManagerProfile|$westprofile|使用步骤 1 中创建的配置文件对象|
    |-Type|ExternalEndpoints|有关详细信息，请参阅[流量管理器终结点][traffic-manager-endpoints] |
    |-Target|westus.api.cognitive.microsoft.com|这是 LUIS 终结点的域。|
    |-EndpointLocation|“westus”|终结点的区域|
    |-EndpointStatus|已启用|创建时启用终结点|

    成功响应如下所示：

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. 使用 [Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0) cmdlet 设置美国西部终结点

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    成功响应与步骤 2 的响应相同。

### <a name="create-parent-traffic-manager-profile"></a>创建父流量管理器配置文件
创建父流量管理器配置文件，并将两个子流量管理器配置文件链接到该父级。

1. 使用 [New-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0) cmdlet 创建父配置文件

    ```PowerShell
    $parentprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    此表介绍了 cmdlet 中的每个变量：

    |配置参数|变量名或值|目的|
    |--|--|--|
    |-Name|luis-profile-parent|Azure 门户中的流量管理器名称|
    |-ResourceGroupName|luis-traffic-manager|在上一节中创建|
    |-TrafficRoutingMethod|性能|有关详细信息，请参阅[流量管理器路由方法][routing-methods]。 如果使用性能，则对流量管理器的 URL 请求必须来自用户的区域。 如果通过聊天机器人或其他应用程序，则聊天机器人应负责模拟对流量管理器的调用中的区域。 |
    |-RelativeDnsName|luis-dns-parent|这是服务 luis-dns-parent.trafficmanager.net 的子域|
    |-Ttl|30|轮询间隔，30 秒|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|LUIS 的端口和协议为 HTTPS/443|
    |-MonitorPath|`/`|此路径并不重要，因为已改用子终结点路径。|

    成功请求没有响应。

2. 使用 [Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0) 和 NestedEndpoints 类型将美国东部子配置文件添加到父级

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    此表介绍了 cmdlet 中的每个变量：

    |配置参数|变量名或值|目的|
    |--|--|--|
    |-EndpointName|child-endpoint-useast|东部配置文件|
    |-TrafficManagerProfile|$parentprofile|要向其分配此终结点的配置文件|
    |-Type|NestedEndpoints|有关详细信息，请参阅 [Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)。 |
    |-TargetResourceId|$eastprofile.Id|子配置文件的 ID|
    |-EndpointStatus|已启用|添加到父级后的终结点状态|
    |-EndpointLocation|“eastus”|资源的 [Azure 区域名称](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|子终结点的最小数量|

    成功响应如下所示，并包括新的 `child-endpoint-useast` 终结点：    

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. 使用 [Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0) cmdlet 和 NestedEndpoints 类型将美国西部子配置文件添加到父级

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    此表介绍了 cmdlet 中的每个变量：

    |配置参数|变量名或值|目的|
    |--|--|--|
    |-EndpointName|child-endpoint-uswest|西部配置文件|
    |-TrafficManagerProfile|$parentprofile|要向其分配此终结点的配置文件|
    |-Type|NestedEndpoints|有关详细信息，请参阅 [Add-AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)。 |
    |-TargetResourceId|$westprofile.Id|子配置文件的 ID|
    |-EndpointStatus|已启用|添加到父级后的终结点状态|
    |-EndpointLocation|“westus”|资源的 [Azure 区域名称](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|子终结点的最小数量|

    成功响应如下所示，并同时包括之前的 `child-endpoint-useast` 终结点和新的 `child-endpoint-uswest` 终结点：

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. 使用 [Set-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0) cmdlet 设置终结点 

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    成功响应与步骤 3 的响应相同。

### <a name="powershell-variables"></a>PowerShell 变量
在前面各节中，创建了三个 PowerShell 变量：`$eastprofile`、`$westprofile`、`$parentprofile`。 流量管理器配置要结束时会使用这些变量。 如果选择不创建变量、忘记创建变量，或 PowerShell 窗口超时，则可以使用 PowerShell cmdlet [Get AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Get-AzureRmTrafficManagerProfile?view=azurermps-6.2.0) 再次获取配置文件，并将其分配给变量。 

将尖括号 `<>` 中的项目替换为所需三个配置文件中每个文件的正确值。 

```PowerShell
$<variable-name> = Get-AzureRmTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>验证流量管理器的工作
若要验证流量管理器配置文件是否正常工作，配置文件的状态需要为 `Online`。此状态基于终结点的轮询路径。 

### <a name="view-new-profiles-in-the-azure-portal"></a>在 Azure 门户中查看新配置文件
可通过查看 `luis-traffic-manager` 资源组中的资源来验证是否已创建所有三个配置文件。

![Azure 资源组 luis-traffic-manager 的屏幕截图](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>验证配置文件处于联机状态
流量管理器轮询每个终结点的路径，确保其处于联机状态。 如果处于联机状态，则子配置文件的状态为 `Online`。 每个配置文件的“概述”页上会显示此信息。 

![显示联机监控状态的 Azure 流量管理器配置文件概述的屏幕截图](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>验证流量管理器轮询的工作
验证流量管理器轮询是否正常工作的另一种方法是使用 LUIS 终结点日志。 在 [LUIS][LUIS] 网站应用列表页上，导出应用程序的终结点日志。 由于流量管理器通常轮询两个终结点，因此，即使它们仅进行了几分钟，日志中也有条目。 请务必查找其中的查询以 `traffic-manager-` 开头的条目。

```text
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>验证流量管理器 DNS 响应的工作
若要验证 DNS 响应是否返回 LUIS 终结点，请使用 DNS 客户端库请求流量管理父配置文件 DNS。 父配置文件的 DNS 名称为 `luis-dns-parent.trafficmanager.net`。

以下 Node.js 代码请求父配置文件，并返回 LUIS 终结点：

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

具有 LUIS 终结点的成功响应是：

```cmd
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>使用流量管理器父配置文件
若要管理终结点之间的流量，需要插入对流量管理器 DNS 的调用来查找 LUIS 终结点。 此调用针对每个 LUIS 终结点请求进行，并需要模拟 LUIS 客户端应用程序用户的地理位置。 在 LUIS 客户端应用程序和 LUIS 请求之间添加 DNS 响应代码，以进行终结点预测。 


## <a name="clean-up"></a>清理
删除两个 LUIS 终结点密钥、三个流量管理器配置文件以及包含这五个资源的资源组。 此操作在 Azure 门户中完成。 从资源列表中删除五个资源。 然后删除资源组。 

## <a name="next-steps"></a>后续步骤

查看 BotFramework v4 中的“[中间件](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler)”选项，了解如何将此流量管理代码添加到 BotFramework 机器人。 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
