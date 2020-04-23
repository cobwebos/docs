---
title: 更新 Azure 事件网格主题或域的定价层
description: 本文介绍如何使用 Azure 门户、Azure CLI 和 Azure PowerShell，将 Azure 事件网格主题或域（初级到高级、高级到基本）的定价层更新。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: caea8d515964510fce432eb0497e3af19ecc1369
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101631"
---
# <a name="update-pricing-tier"></a>更新定价层 
本文介绍如何使用 Azure 门户、Azure CLI 和 Azure PowerShell 更新 Azure 事件网格主题或域的定价层。 

## <a name="use-azure-portal"></a>使用 Azure 门户
本部分说明如何在 Azure 门户中更改主题或域的定价层。 

### <a name="overview-page"></a>概述页
您可以在 "**概述**" 页上更改主题或域的定价层。 下面的示例演示如何将主题从基本层升级到高级层。 从高级层降级到基本层的步骤类似。

1. 在[Azure 门户](https://portal.azure.com)中，导航到 "主题" 或 "域" 页。 
2. 在 "**概述**" 页上，选择当前定价层（在以下示例中为 "**基本**"）。
    
    ![选择当前定价层](./media/update-tier/select-tier.png)
3. 在 "**定价层**" 页上更改层，然后选择 **"确定"**。 

    ![更新定价层](./media/update-tier/change-tier.png)
4. 在 "**通知**" 页中查看操作的状态。

    ![更新状态](./media/update-tier/status.png)    
5. 确认在 "**概述**" 页上看到更新的层。 

    ![更新状态](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>网络页面
你可以从 "基本" 层**升级**到 "**网络**" 页上的 "高级" 层。 不过，在此页上，不能从高级层降级到基本层。 

1. 在[Azure 门户](https://portal.azure.com)中，导航到 "主题" 或 "域" 页。 
2. 在 "**网络**" 页上，切换到**专用终结点连接（预览）** 选项卡。 
3. 如果当前定价层为 "**基本**"，则会看到以下消息。 选择它。 

    ![更新专用终结点连接页上的层](./media/update-tier/private-endpoint-connections-page.png)
4. 在 "**更新到高级定价层**" 页上，选择 **"是"**。 
    
    ![确认升级](./media/update-tier/message-private-endpoint-connection.png)
5. 在 "**通知**" 页中查看操作的状态。

    ![更新状态](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>使用 Azure CLI
本部分演示如何使用 Azure CLI 命令来更改主题的定价层。 若要更新域的定价层，请使用`az eventgrid domain update`类似方式的命令。

### <a name="prerequisites"></a>先决条件
通过运行以下命令更新适用于 CLI 的 Azure 事件网格扩展： 

```azurecli-interactive
az extension update -n eventgrid
```

如果未安装该扩展，请运行以下命令进行安装： 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>将主题从基本版升级到高级版

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Premium" 
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>将主题从高级层降级到基本

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Basic" 
```


## <a name="use-azure-powershell"></a>使用 Azure PowerShell
本部分介绍如何使用 PowerShell 命令更改主题或域的定价层。 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>将主题从基本版升级到高级版

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>将主题从高级层降级到基本

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>将域从 "基本" 升级到 "高级"

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>将域从高级降级为基本

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>后续步骤
对于高级层主题和域，你可以将专用终结点配置为仅从所选的虚拟网络中限制访问。 有关分步说明，请参阅[配置专用终结点](configure-private-endpoints.md)。
