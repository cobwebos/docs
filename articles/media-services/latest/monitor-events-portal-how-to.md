---
title: 使用门户监视带有事件网格的 Azure 媒体服务事件 | Microsoft Docs
description: 本文介绍如何订阅事件网格以监视 Azure 媒体服务事件。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure 媒体服务, 流, 广播, 实时, 脱机
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 4e6527bf115f327635a0b0fe187094dafb320598
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380530"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>使用门户创建并监视带有事件网格的 Azure 媒体服务事件

Azure 事件网格是针对云的事件处理服务。 在本文中，使用 Azure 门户订阅 Azure 媒体服务帐户的事件。 然后，触发事件以查看结果。 通常，你会将事件发送到处理事件数据并执行操作的终结点。 在本文中，将事件发送到收集并显示消息的 Web 应用。

完成后即可看到事件数据已发送到 Web 应用。

## <a name="prerequisites"></a>先决条件 

* 拥有一个有效的 Azure 订阅。
* 按照[本快速入门](create-account-cli-quickstart.md)所述，创建新的 Azure 媒体服务帐户。

## <a name="create-a-message-endpoint"></a>创建消息终结点

在订阅媒体服务帐户的事件之前，先创建事件消息的终结点。 通常情况下，终结点基于事件数据执行操作。 在本文中，将部署用于显示事件消息的[预建 Web 应用](https://github.com/Azure-Samples/azure-event-grid-viewer)。 所部署的解决方案包括应用服务计划、应用服务 Web 应用和 GitHub 中的源代码。

1. 选择“部署到 Azure”将解决方案部署到你的订阅。 在 Azure 门户中，为参数提供值。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. 部署可能需要几分钟才能完成。 部署成功后，请查看 Web 应用以确保它正在运行。 在 Web 浏览器中导航到 `https://<your-site-name>.azurewebsites.net`

如果切换到“Azure 事件网格查看器”站点，将看到其中尚不具有任何事件。
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>订阅媒体服务事件

订阅主题，以告知事件网格要跟踪哪些事件，以及要将事件发送到何处。

1. 在门户中，选择媒体服务帐户并选择“事件”。
1. 若要将事件发送到查看器应用，请为终结点使用 Webhook。 

   ![选择“Webhook”](./media/monitor-events-portal/select-web-hook.png)

1. 事件订阅中预先填写了媒体服务帐户的值。 
1. 对于“终结点类型”，选择“Web 挂钩”。
1. 在本主题中，让“订阅所有事件类型”保持选中状态。 不过可以取消选中，然后筛选特定事件类型。 
1. 单击“选择终结点”链接。

    对于 Webhook 终结点，请提供你的 Web 应用的 URL，并将 `api/updates` 添加到主页 URL。 

1. 按“确认选择”。
1. 按“创建”。
1. 为你的订阅提供一个名称。

   ![选择日志](./media/monitor-events-portal/create-subscription.png)

1. 再次查看 Web 应用，并注意现已向该应用发送了订阅验证事件。 

    事件网格发送验证事件，以便终结点可以验证它是否想要接收事件数据。 终结点必须将 `validationResponse` 设置为 `validationCode`。 有关详细信息，请参阅[事件网格安全性和身份验证](../../event-grid/security-authentication.md)。 可以查看 Web 应用代码以了解它如何验证订阅。

现在，让我们触发事件，看事件网格如何将消息分发到终结点。

## <a name="send-an-event-to-your-endpoint"></a>向终结点发送事件

可通过运行编码作业来触发媒体服务帐户的事件。 可遵照[本快速入门](stream-files-dotnet-quickstart.md)来对文件进行编码并开始发送事件。 如果订阅了所有事件，将看到与以下类似的屏幕：

> [!TIP]
> 选择眼睛图标以展开事件数据。 如果要查看所有事件，请勿刷新页面。

![查看订阅事件](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>后续步骤

[上传、编码和流式处理](stream-files-tutorial-with-api.md)
