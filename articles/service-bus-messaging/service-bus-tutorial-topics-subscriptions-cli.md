---
title: 使用 Azure CLI 创建服务总线主题和订阅
description: 本快速入门将介绍如何使用 Azure CLI 创建服务总线主题和该主题的订阅
ms.date: 06/23/2020
ms.topic: quickstart
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 080b089efa276779420f6d9bc8e76272f1e65788
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90069690"
---
# <a name="use-azure-cli-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>使用 Azure CLI 创建一个服务总线主题和该主题的订阅
在本快速入门中，你将使用 Azure CLI 创建服务总线主题，然后创建该主题的订阅。 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>什么是服务总线主题和订阅？
服务总线主题和订阅支持 *发布/订阅* 消息通信模型。 在使用主题和订阅时，分布式应用程序的组件不会直接相互通信，而是通过充当中介的主题交换消息。

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

与每条消息都由单个使用方处理的服务总线队列相比，主题和订阅通过发布/订阅模式提供一对多通信方式。 可向一个主题注册多个订阅。 当消息发送到主题时，每个订阅会分别对该消息进行处理。 主题订阅类似于接收发送至该主题的消息副本的虚拟队列。 可以选择基于每个订阅注册主题的筛选规则，这样就可以筛选或限制哪些主题订阅接收发送至某个主题的哪些消息。

利用服务总线主题和订阅，可以进行扩展以处理跨大量用户和应用程序的许多消息。

## <a name="prerequisites"></a>先决条件
如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][free account]。

在本快速入门中，你将使用 Azure Cloud Shell，在登录 Azure 门户后即可启动该服务。 有关 Azure Cloud Shell 的详细信息，请参阅 [Azure Cloud Shell 概述](../cloud-shell/overview.md)。 你还可以在计算机上[安装](/cli/azure/install-azure-cli)和使用 Azure PowerShell。 

## <a name="create-a-service-bus-topic-and-subscriptions"></a>创建“服务总线”主题和订阅
每个[对主题的订阅](service-bus-messaging-overview.md#topics)都可以接收每条消息的副本。 主题在协议和语义方面与服务总线队列完全兼容。 服务总线主题支持一系列选择规则，这些规则具有筛选条件和用来设置或修改消息属性的可选操作。 规则每次匹配时，都会生成一条消息。 若要深入了解规则、筛选器和操作，请单击此[链接](topic-filters.md)。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 通过选择下图中显示的图标启动 Azure Cloud Shell。 如果 Cloud Shell 处于 PowerShell 模式，请切换到 Bash 模式 。 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="启动 Cloud Shell":::
3. 运行以下命令来创建 Azure 资源组。 如有需要，请更新资源组名称和位置。 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```
4. 运行以下命令以创建服务总线消息命名空间。 更新命名空间的名称，使其成为唯一名称。 

    ```azurecli-interactive
    namespaceName=MyNameSpace$RANDOM
    az servicebus namespace create --resource-group MyResourceGroup --name $namespaceName --location eastus
    ```
5. 运行以下命令以在命名空间中创建主题。 

    ```azurecli-interactive
    az servicebus topic create --resource-group MyResourceGroup   --namespace-name $namespaceName --name MyTopic
    ```
6. 创建主题的第一个订阅
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S1    
    ```
6. 创建主题的第二个订阅
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S2    
    ```
6. 创建主题的第三个订阅
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S3    
    ```
7. 通过使用自定义属性的筛选条件（`StoreId` 是 `Store1`、`Store2` 和 `Store3` 之一）对第一个订阅创建筛选器。

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"    
    ```
8. 通过使用自定义属性的筛选条件 (`StoreId = Store4`) 对第二个订阅创建筛选器

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"    
    ```
9. 通过使用自定义属性的筛选条件（`StoreId` 不是 `Store1`、`Store2`、`Store3` 或 `Store4`）对第三个订阅创建筛选器。

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S3 --name MyThirdFilter --filter-sql-expression "StoreId NOT IN ('Store1','Store2','Store3', 'Store4')"     
    ```
10. 运行以下命令，获取命名空间的主连接字符串。 使用此连接字符串连接到队列并发送和接收消息。 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group MyResourceGroup --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    请记下该连接字符串和主题名称。 使用它们发送和接收消息。 
    

## <a name="next-steps"></a>后续步骤
要了解如何将消息发送到主题并通过订阅接收这些消息，请参阅以下文章：在目录中选择编程语言。 

> [!div class="nextstepaction"]
> [发布和订阅消息](service-bus-dotnet-how-to-use-topics-subscriptions.md)


[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
