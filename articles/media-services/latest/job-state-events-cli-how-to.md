---
title: 使用 CLI 通过事件网格监视 Azure 媒体服务事件 | Microsoft Docs
description: 本文介绍如何订阅事件网格以监视 Azure 媒体服务事件。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: f6243bbc21466361aed7cbb7193f3a7b7c7e539f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322649"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>使用 Azure CLI 创建并通过事件网格监视 Azure 媒体服务事件

Azure 事件网格是针对云的事件处理服务。 此服务使用[事件订阅](../../event-grid/concepts.md#event-subscriptions)事件消息路由到订阅服务器。 媒体服务事件包含响应数据中的更改所需的所有信息。 可以识别媒体服务事件，因为 eventType 属性以“Microsoft.Media”开头。 有关详细信息，请参阅[媒体服务事件架构](media-services-event-schemas.md)。

本文将使用 Azure CLI 订阅 Azure 媒体服务帐户的事件。 然后，触发事件以查看结果。 通常，你会将事件发送到处理事件数据并执行操作的终结点。 在本文中，将事件发送到收集并显示消息的 Web 应用。

## <a name="prerequisites"></a>必备组件

- 一个有效的 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 在本地安装并使用 CLI，本文要求使用 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

    目前，并非所有[媒体服务 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中运行。 建议在本地使用 CLI。

- [创建媒体服务帐户](create-account-cli-how-to.md)。

    请务必记住用于资源组名称和媒体服务帐户名称的值。

## <a name="create-a-message-endpoint"></a>创建消息终结点

在订阅媒体服务帐户的事件之前，先创建事件消息的终结点。 通常情况下，终结点基于事件数据执行操作。 在本文中，将部署用于显示事件消息的[预建 Web 应用](https://github.com/Azure-Samples/azure-event-grid-viewer)。 所部署的解决方案包括应用服务计划、应用服务 Web 应用和 GitHub 中的源代码。

1. 选择“部署到 Azure”将解决方案部署到你的订阅。 在 Azure 门户中，为参数提供值。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. 部署可能需要几分钟才能完成。 部署成功后，请查看 Web 应用以确保它正在运行。 在 Web 浏览器中导航到 `https://<your-site-name>.azurewebsites.net`

如果切换到“Azure 事件网格查看器”站点，将看到其中尚不具有任何事件。
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>设置 Azure 订阅

在以下命令中，为媒体服务帐户提供想要使用的 Azure 订阅 ID。 导航到[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)即可查看有权访问的订阅列表。

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>订阅媒体服务事件

订阅文章是为了告知事件网格要跟踪哪些事件。以下示例订阅所创建的媒体服务帐户，并将所创建网站中的 URL 作为事件通知的终结点进行传递。 

将 `<event_subscription_name>` 替换为事件订阅的唯一名称。 对于 `<resource_group_name>` 和 `<ams_account_name>`，使用在创建媒体服务帐户时使用的值。 对于 `<endpoint_URL>`，请提供你的 Web 应用的 URL，并将 `api/updates` 添加到主页 URL。 在订阅时指定终结点，然后事件网格就会负责将事件路由到该终结点。 

1. 获取资源 ID

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    例如：

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. 订阅事件

    ```azurecli
    az eventgrid event-subscription create \
    --resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    例如：

    ```
    az eventgrid event-subscription create --resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > 可能会收到验证握手警告。 等待几分钟后握手验证应会完成。

现在，让我们触发事件，看事件网格如何将消息分发到终结点。

## <a name="send-an-event-to-your-endpoint"></a>向终结点发送事件

可通过运行编码作业来触发媒体服务帐户的事件。 可遵照[本快速入门](stream-files-dotnet-quickstart.md)来对文件进行编码并开始发送事件。 

再次查看 Web 应用，并注意现已向该应用发送了订阅验证事件。 事件网格发送验证事件，以便终结点可以验证它是否想要接收事件数据。 终结点必须将 `validationResponse` 设置为 `validationCode`。 有关详细信息，请参阅[事件网格安全性和身份验证](../../event-grid/security-authentication.md)。 可以查看 Web 应用代码以了解它如何验证订阅。

> [!TIP]
> 选择眼睛图标以展开事件数据。 如果要查看所有事件，请勿刷新页面。

![查看订阅事件](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>后续步骤

[上传、编码和流式处理](stream-files-tutorial-with-api.md)

