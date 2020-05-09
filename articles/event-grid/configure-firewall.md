---
title: 为 Azure 事件网格主题或域配置 IP 防火墙（预览版）
description: 本文介绍如何配置事件网格主题或域的防火墙设置。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: 0db6c2c346a6eb6ef016340fcfc2974c85958e6c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858109"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>为 Azure 事件网格主题或域配置 IP 防火墙（预览版）
默认情况下，只要请求附带有效的身份验证和授权，就可以从 internet 访问主题和域。 使用 IP 防火墙，你可以将其进一步限制为仅一组 IPv4 地址或[CIDR （无类别域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法中的 ipv4 地址范围。 源自任何其他 IP 地址的发布者将被拒绝，并将收到403（禁止）响应。 有关事件网格支持的网络安全功能的详细信息，请参阅[事件网格的网络安全](network-security.md)。

本文介绍如何为 Azure 事件网格主题或域配置 IP 防火墙设置。

## <a name="use-azure-portal"></a>使用 Azure 门户
本部分说明如何使用 Azure 门户创建入站 IP 防火墙规则。 本部分中所示的步骤适用于主题。 你可以使用类似的步骤为**域**创建入站 IP 规则。 

1. 在[Azure 门户](https://portal.azure.com)中，导航到事件网格主题或域，并切换到 "**网络**" 选项卡。
2. 选择 "**公用网络**" 以允许所有网络（包括 internet）访问资源。 

    可以使用基于 IP 的防火墙规则限制流量。 在无类域间路由（CIDR）表示法中指定单个 IPv4 地址或一系列 IP 地址。 

    ![公用网络页面](./media/configure-firewall/public-networks-page.png)
3. **仅**允许专用终结点连接访问此资源。 使用此页上的 "**专用终结点连接**" 选项卡可管理连接。 

    ![公用网络页面](./media/configure-firewall/private-endpoints-page.png)
4. 在工具栏上选择“保存”。  



## <a name="use-azure-cli"></a>使用 Azure CLI
本部分说明如何使用 Azure CLI 命令创建具有入站 IP 规则的主题。 本部分中所示的步骤适用于主题。 你可以使用类似的步骤为**域**创建入站 IP 规则。 


### <a name="prerequisites"></a>必备条件
通过运行以下命令更新适用于 CLI 的 Azure 事件网格扩展： 

```azurecli-interactive
az extension update -n eventgrid
```

如果未安装该扩展，请运行以下命令进行安装： 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="enable-or-disable-public-network-access"></a>启用或禁用公共网络访问
默认情况下，将为主题和域启用公共网络访问。 还可以显式启用或禁用该方法。 可以通过配置入站 IP 防火墙规则来限制流量。 

#### <a name="enable-public-network-access-while-creating-a-topic"></a>创建主题时启用公用网络访问

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled
```


#### <a name="disable-public-network-access-while-creating-a-topic"></a>创建主题时禁用公共网络访问

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access disabled
```

> [!NOTE]
> 如果为主题或域禁用了公用网络访问，则不允许通过公共 internet 的流量。 仅允许专用终结点连接访问这些资源。 


#### <a name="enable-public-network-access-for-an-existing-topic"></a>为现有主题启用公共网络访问

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled 
```

#### <a name="disable-public-network-access-for-an-existing-topic"></a>对现有主题禁用公共网络访问权限 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```

### <a name="create-a-topic-with-single-inbound-ip-rule"></a>使用单个入站 ip 规则创建主题
下面的示例 CLI 命令使用入站 IP 规则创建事件网格主题。 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow 
```

### <a name="create-a-topic-with-multiple-inbound-ip-rules"></a>创建具有多个入站 ip 规则的主题

下面的示例 CLI 命令在一步中创建事件网格主题两个入站 IP 规则： 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="update-an-existing-topic-to-add-inbound-ip-rules"></a>更新现有主题以添加入站 IP 规则
此示例首先创建一个事件网格主题，然后在单独的命令中为该主题添加入站 IP 规则。 它还将更新在第二个命令中设置的入站 IP 规则。 

```azurecli-interactive

# create the event grid topic first
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# add inbound IP rules to an existing topic
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow

# later, update topic with additional ip rules
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="remove-an-inbound-ip-rule"></a>删除入站 IP 规则
以下命令将删除在上一步中创建的第二个规则，方法是在更新设置时仅指定第一个规则。 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow
```


## <a name="use-powershell"></a>使用 PowerShell
本部分演示如何使用 Azure PowerShell 命令通过入站 IP 防火墙规则创建 Azure 事件网格主题。 本部分中所示的步骤适用于主题。 你可以使用类似的步骤为**域**创建入站 IP 规则。 

### <a name="prerequisites"></a>必备条件
按照[如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)中的说明来创建 Azure Active Directory 应用程序，并记下以下值：

- 目录(租户) ID
- 应用程序（客户端） ID
- 应用程序（客户端）机密

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>准备 REST API 调用的令牌和标头 
运行以下先决条件命令以获取用于 REST API 调用的身份验证令牌，以及授权和其他标头信息。 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>为现有主题启用公共网络访问
默认情况下，将为主题和域启用公共网络访问。 可以通过配置入站 IP 防火墙规则来限制流量。 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>对现有主题禁用公共网络访问权限
如果为主题或域禁用了公用网络访问，则不允许通过公共 internet 的流量。 仅允许专用终结点连接访问这些资源。 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>在一个步骤中使用入站规则创建事件网格主题

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>首先创建事件网格主题，然后添加入站 ip 规则

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature is available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"}, @{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>后续步骤

* 有关监视事件传送的信息，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 有关身份验证密钥的详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
