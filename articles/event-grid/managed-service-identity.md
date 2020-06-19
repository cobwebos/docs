---
title: 使用托管服务标识进行事件传送
description: 本文介绍如何为 Azure 事件网格主题启用托管服务标识。 用于将事件转发到受支持的目标。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 4d96f28b98cccada2ac5c77589acc6df1430bb02
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700651"
---
# <a name="event-delivery-with-managed-identity"></a>使用托管标识进行事件传送
本文介绍如何为事件网格主题或域启用[托管服务标识](../active-directory/managed-identities-azure-resources/overview.md)。 使用它将事件转发到受支持的目标，如服务总线队列和主题、事件中心和存储帐户。

本文详细介绍了以下步骤：
1. 使用系统分配的标识创建主题或域（或）更新现有主题或域以启用标识。 
2. 将标识添加到目标（示例：服务总线队列）上的相应角色（示例：服务总线数据发送方）
3. 创建事件订阅时，启用标识的使用以将事件传送到目标。 

## <a name="create-a-topic-or-domain-with-an-identity"></a>创建带有标识的主题或域
首先，让我们看看如何创建带有系统托管标识的主题或域。

### <a name="using-azure-portal"></a>使用 Azure 门户
在 Azure 门户中创建主题/域时，可以为其启用系统分配的标识。 下图显示如何为主题启用系统托管标识。 基本上，你可以在主题创建向导的“高级”页面上选择“启用系统分配的标识”选项 。 在域创建向导的“高级”页上也可以看到此选项。 

![创建主题时启用标识](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>使用 Azure CLI
你还可以使用 Azure CLI 创建具有系统分配标识的主题或域。 使用 `az eventgrid topic create` 命令，将 `--identity` 参数设置为 `systemassigned`。 如果不为此参数指定值，则使用默认值 `noidentity`。 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

类似地，可以使用 `az eventgrid domain create` 命令创建具有系统托管标识的域。

## <a name="enable-identity-for-an-existing-topic-or-domain"></a>为现有主题或域启用标识
上一部分已介绍如何在创建主题或域时启用系统托管标识。 本部分将介绍如何为现有主题或域启用系统托管标识。 

### <a name="using-azure-portal"></a>使用 Azure 门户
1. 导航到 [Azure 门户](https://portal.azure.com)
2. 在搜索栏中搜索“事件网格主题”。
3. 选择要为其启用托管标识的主题。 
4. 切换到“标识”选项卡。 
5. 打开开关以启用标识。 

    你可以使用类似的步骤为事件网格域启用标识。

### <a name="using-azure-cli"></a>使用 Azure CLI
使用将 `az eventgrid topic update` 命令并将 `--identity` 设置为 `systemassigned`为现有主题启用系统分配的标识。 如果要禁用标识，请将该值指定为 `noidentity`。 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

更新现有域的命令也是类似的 (`az eventgrid domain update`)。

## <a name="supported-destinations-and-role-based-access-check-rbac-roles"></a>支持的目标和基于角色的访问检查 (RBAC) 角色
为事件网格主题或域启用标识后，Azure 会自动在 Azure Active Directory (Azure AD) 中创建标识。 将此标识添加到适当的 RBAC 角色，以便主题或域可以将事件转发到受支持的目标。 例如，将标识添加到事件中心命名空间的“Azure 事件中心数据发送方”角色，以便事件网格主题可以将事件转发到该命名空间中的事件中心。  

当前，Azure 事件网格支持配置有系统分配的托管标识的主题或域，以将事件转发到以下目标。 此表还为你提供标识应位于的角色，以便主题可以转发事件。

| 目标 | RBAC 角色 | 
| ----------- | --------- | 
| 服务总线队列和主题 | [Azure 服务总线数据发送方](../service-bus-messaging/authenticate-application.md#built-in-rbac-roles-for-azure-service-bus) |
| 事件中心 | [Azure 事件中心数据发送方](../event-hubs/authorize-access-azure-active-directory.md#built-in-rbac-roles-for-azure-event-hubs) | 
| Blob 存储 | [存储 Blob 数据参与者](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) |
| 队列存储 |[存储队列数据消息发送方](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-identity-to-rbac-roles-on-destinations"></a>向目标上的 RBAC 角色添加标识
本部分介绍如何将主题或域的标识添加到 RBAC 角色。 

### <a name="using-azure-portal"></a>使用 Azure 门户
你可以使用 Azure 门户将主题/域标识分配给相应的角色，以便让主题/域可以将事件转发到目标。 

以下示例将名为“msitesttopic”的事件网格主题的托管标识添加到包含队列或主题资源的服务总线“命名空间”的“Azure 服务总线数据发送方”角色  。 在命名空间级别添加到角色时，主题可以将事件转发到命名空间中的所有实体。 

1. 在 [Azure 门户](https://portal.azure.com)中导航到你的“服务总线命名空间”。 
2. 在左窗格中选择“访问控制”。 
3. 在“添加角色分配”部分中选择“添加” 。 
4. 在“添加角色分配”页上执行以下步骤：
    1. 选择角色。 在本例中，它是“Azure 服务总线数据发送方”。 
    2. 为主题或域选择“标识”。 
    3. 选择“保存”以保存配置。

这些步骤与向表中提到的其他角色添加标识相似。 

### <a name="using-azure-cli"></a>使用 Azure CLI
本部分中的示例演示如何使用“Azure CLI”向 RBAC 角色添加标识。 示例命令适用于事件网格主题。 适用于事件网格域的命令也是类似的。 

#### <a name="get-principal-id-for-the-topics-system-identity"></a>获取主题的系统标识的主体 ID 
首先，获取主题的系统托管标识的主体 ID，并将该标识分配给相应的角色。

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>在不同范围内创建事件中心的角色分配 
以下 CLI 示例演示如何在命名空间级别或事件中心级别将主题的标识添加到“Azure 事件中心数据发送方”角色。 如果在命名空间中创建角色分配，则主题可以将事件转发到该命名空间中的所有事件中心。 如果在事件中心级别创建角色分配，则主题只能将事件转发到该特定事件中心。 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-service-bus-topic-at-various-scopes"></a>在不同的范围内创建服务总线主题的角色分配 
以下 CLI 示例演示如何在命名空间级别或服务总线主题级别将主题的标识添加到“Azure 服务总线数据发送方”角色。 如果在命名空间中创建角色分配，则事件网格主题可以向该命名空间中的所有实体（服务总线队列或主题）转发事件。 如果在服务总线队列或主题级别创建角色分配，则事件网格主题只能将事件转发到该特定的服务总线队列或主题。 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-identity"></a>创建使用标识的事件订阅
在拥有具有系统托管标识的主题或域并将该标识添加到目标上的相应角色后，就可以创建使用该标识的订阅了。 

### <a name="using-azure-portal"></a>使用 Azure 门户
创建事件订阅时，你将在“终结点详细信息”部分看到一个选项，用于启用该终结点的系统托管标识。 

![在为服务总线队列创建事件订阅时启用标识](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

还可以在“附加功能”选项卡上启用系统分配的标识以用于死信。 

![为死信启用系统分配的标识](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="using-azure-cli---service-bus-queue"></a>使用 Azure CLI - 服务总线队列 
在本部分中，你将了解如何使用“Azure CLI”启用系统分配的标识将事件传送到服务总线队列。 标识必须是“Azure 服务总线数据发送方”角色的成员。 它还必须是用于死信的存储帐户上的“存储 Blob 数据参与者”角色的成员。 

#### <a name="define-variables"></a>定义变量
首先，指定要在 CLI 命令中使用的以下变量的值。 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery"></a>使用用于传送的托管标识创建事件订阅 
此示例命令为终结点类型设置为“服务总线队列”的事件网格主题创建事件订阅。 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery-and-dead-lettering"></a>使用用于传送和死信的托管标识创建事件订阅
此示例命令为终结点类型设置为“服务总线队列”的事件网格主题创建事件订阅。 它还指定要用于死信的系统托管标识。 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="azure-cli---event-hubs"></a>Azure CLI - 事件中心 
在本部分中，你将了解如何使用“Azure CLI”启用系统分配的标识，以将事件传送到事件中心。 该标识必须是“Azure 事件中心数据发送方”角色的成员。 它还必须是用于死信的存储帐户上的“存储 Blob 数据参与者”角色的成员。 

#### <a name="define-variables"></a>定义变量
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>使用用于传送的托管标识创建事件订阅 
此示例命令为终结点类型设置为“事件中心”的事件网格主题创建事件订阅。 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>使用用于传送和死信的托管标识创建事件订阅 
此示例命令为终结点类型设置为“事件中心”的事件网格主题创建事件订阅。 它还指定要用于死信的系统托管标识。 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="azure-cli---azure-storage-queue"></a>Azure CLI - Azure 存储队列 
在本部分中，你将了解如何使用“Azure CLI”启用系统分配的标识，以将事件传送到 Azure 存储队列。 该标识必须是存储帐户上“存储 Blob 数据参与者”角色的成员。

#### <a name="define-variables"></a>定义变量  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>使用用于传送的托管标识创建事件订阅 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>使用用于传送和死信的托管标识创建事件订阅 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```



## <a name="next-steps"></a>后续步骤
有关托管服务标识的详细信息，请参阅[什么是 Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 