---
title: 将 Azure 媒体服务事件路由到自定义 Web 终结点 | Microsoft Docs
description: 使用 Azure 事件网格订阅媒体服务作业状态更改事件。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: e9df0cd24ef890765b78c25a073d671889be10a7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723739"
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>使用 CLI 将 Azure 媒体服务事件路由到自定义 Web 终结点

Azure 事件网格是针对云的事件处理服务。 在本文中，请使用 Azure CLI 订阅 Azure 媒体服务作业状态更改事件，然后触发可查看结果的事件。 

通常将事件发送到与该事件对应的终结点，例如 webhook 或 Azure Function。 本教程演示如何创建并设置 Webhook。

完成本文所述步骤后，即可看到事件数据已发送到某个终结点。

## <a name="log-in-to-azure"></a>登录 Azure

登录 [Azure 门户](http://portal.azure.com)并启动 CloudShell 以执行 CLI 命令，如后续步骤中所示。

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 CLI，本文要求使用 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

请务必记住用于媒体服务帐户名称、存储名称和资源名称的值。

## <a name="enable-event-grid-resource-provider"></a>启用事件网格资源提供程序

首先需要执行的操作是确保订阅中已启用事件网格资源提供程序。 

在 Azure 门户中执行以下操作：

1. 转到订阅。
2. 选择订阅。
3. 在“设置”下，选择“资源提供程序”。
4. 搜索“EventGrid”。
5. 请确保已注册事件网格。 如果未注册，则按“注册”按钮。  

## <a name="create-a-generic-azure-function-webhook"></a>创建通用 Azure 函数 Webhook 

### <a name="create-a-message-endpoint"></a>创建消息终结点

在订阅事件网格的文章前，创建一个终结点来收集消息，方便你查看。

按照[通用 Webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) 一文所述，创建由通用 Webhook 触发的函数。 本教程中使用了 C# 代码。

创建 Webhook 后，通过单击 Azure 门户窗口顶部的“获取函数 URL”链接复制 URL。 不需要 URL 的末尾部分 (&clientID=default)。

![创建 Webhook](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>验证 Webhook

在向事件网格注册自己的 WebHook 终结点时，事件网格为证明终结点所有权，会向你发送 POST 请求，其中包含简单的验证代码。 应用需要通过回显验证代码，做出响应。 事件网格不会将事件传送到未通过验证的 WebHook 终结点。 有关详细信息，请参阅[事件网格安全性和身份验证](https://docs.microsoft.com/azure/event-grid/security-authentication)。 本节定义了要通过验证必须定义的两个部分。

#### <a name="update-the-source-code"></a>更新源代码

创建 Webhook 后，浏览器中会显示 run.csx 文件。 将默认代码替换为以下代码。 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>更新测试请求正文

在 Azure 门户窗口的右侧，可看到两个选项卡：“查看文件”和“测试”。 选择“测试”选项卡。在“请求正文”中，粘贴以下 json。 可以按原样粘贴，无需更改任何值。

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

在该窗口顶部按“保存并运行”。

![请求正文](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>注册事件网格订阅 

订阅文章是为了告知事件网格要跟踪哪些事件。以下示例订阅所创建的媒体服务帐户，并将创建的 Azure Function Webhook 中的 URL 作为事件通知的终结点传递。 

将 `<event_subscription_name>` 替换为事件订阅的唯一名称。 对于 `<resource_group_name>` 和 `<ams_account_name>`，请使用此前创建的值。  在 `<endpoint_URL>` 中粘贴终结点 URL。 从 URL 删除 &clientID=default。 在订阅时指定终结点，然后事件网格就会负责将事件路由到该终结点。 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

媒体服务帐户资源 ID 值如下所示：

/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>测试事件

运行编码作业。 例如，如[流式处理视频文件](stream-files-dotnet-quickstart.md)快速入门中所述。

你已经触发事件，而事件网格则已将消息发送到你在订阅时配置的终结点。 浏览到之前创建的 Webhook。 单击“监视”和“刷新”。 可看到作业的状态更改事件：“已排队”、“已计划”、“正在处理”、“已完成”、“错误”、“已取消”、“正在取消”。  有关详细信息，请参阅[媒体服务事件架构](media-services-event-schemas.md)。

例如：

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![测试事件](./media/job-state-events-cli-how-to/test_events.png)

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用此存储帐户和事件订阅，请勿清除本文中创建的资源。 如果不打算继续学习，请使用以下命令删除本文中创建的资源。

将 `<resource_group_name>` 替换为上面创建的资源组。

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>后续步骤

[对事件做出响应](reacting-to-media-services-events.md)

## <a name="see-also"></a>另请参阅

[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
