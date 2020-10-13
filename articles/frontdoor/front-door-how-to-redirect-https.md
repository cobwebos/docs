---
title: 使用 Azure 门户的 HTTP 到 HTTPS 重定向创建前门
description: 了解如何使用 Azure 门户通过 HTTP 到 HTTPS 的重定向流量创建前门。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 19908b3cba63bc76a205097ef8d16e612d58503b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91626600"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>使用 Azure 门户的 HTTP 到 HTTPS 重定向创建前门

你可以使用 Azure 门户创建具有 TLS 终止证书的 [前门](quickstart-create-front-door.md) 。 路由规则用于将 HTTP 流量重定向到 HTTPS。

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>使用现有的 Web 应用资源创建前门

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

1. 选择 "创建在 Azure 门户左上角找到 **的资源** "。

1. 使用搜索栏搜索 **前门** ，找到资源类型后，选择 " **创建**"。

1. 选择一个 *订阅* ，然后使用现有的资源组或创建一个新的资源组。 选择 " **下一步** " 以输入配置选项卡。

    > [!NOTE]
    > 用户界面中的位置仅用于资源组。 你的前门配置将部署到所有 [Azure 前门的 POP 位置](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)。

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="配置新前门的基本知识&quot;:::

1. 前门的配置发生在三个步骤中：添加默认前端主机，在后端池中添加后端，然后创建路由规则，以映射前端主机的路由行为。 选择 **+** _前端主机_ 上的 &quot;" 图标创建前端主机。

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="配置新前门的基本知识&quot;:::

1. 前门的配置发生在三个步骤中：添加默认前端主机，在后端池中添加后端，然后创建路由规则，以映射前端主机的路由行为。 选择 **+** _前端主机_ 上的 &quot;" 以继续执行下一步。

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="配置新前门的基本知识&quot;:::

1. 前门的配置发生在三个步骤中：添加默认前端主机，在后端池中添加后端，然后创建路由规则，以映射前端主机的路由行为。 选择 **+** _前端主机_ 上的 &quot;":::

### <a name="create-backend-pool"></a>创建后端池

1. 选择 **+** _后端池_ 上的 "" 图标创建后端池。 提供后端池的名称，然后选择 " **添加后端**"。

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="配置新前门的基本知识&quot;:::

1. 前门的配置发生在三个步骤中：添加默认前端主机，在后端池中添加后端，然后创建路由规则，以映射前端主机的路由行为。 选择 **+** _前端主机_ 上的 &quot;" **后端主机名**"。

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="配置新前门的基本知识&quot;:::

1. 前门的配置发生在三个步骤中：添加默认前端主机，在后端池中添加后端，然后创建路由规则，以映射前端主机的路由行为。 选择 **+** _前端主机_ 上的 &quot;" 以保存后端池配置。 

## <a name="create-http-to-https-redirect-rule"></a>创建 HTTP 到 HTTPS 的重定向规则

1. 选择 **+** *路由规则* 上的 "" 图标来创建路由。 提供路由的名称（例如 "HttpToHttpsRedirect"），然后将 " *接受的协议* " 字段设置为 **"仅 HTTP"**。 确保选择适当的 *前端/域* 。  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="配置新前门的基本知识&quot;:::

1. 前门的配置发生在三个步骤中：添加默认前端主机，在后端池中添加后端，然后创建路由规则，以映射前端主机的路由行为。 选择 **+** _前端主机_ 上的 &quot;" **仅 HTTPS**"。 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="配置新前门的基本知识&quot;:::

1. 前门的配置发生在三个步骤中：添加默认前端主机，在后端池中添加后端，然后创建路由规则，以映射前端主机的路由行为。 选择 **+** _前端主机_ 上的 &quot;" 以保存用于 HTTP 到 HTTPS 重定向的路由规则。

## <a name="create-forwarding-rule"></a>创建转发规则

1. 添加另一个路由规则来处理 HTTPS 通信。 选择 " **+** 登录 *路由规则* " 和 "提供路由名称"，例如 "DefaultForwardingRoute"。 然后，将 " *接受的协议* " 字段设置为 " **仅 HTTPS**"。 确保选择适当的 *前端/域* 。

1. 在 "路由详细信息" 部分中，将 " *路由类型* " 设置为 " **转发**"。 确保选择正确的后端池，并且 *转发协议* 设置为 " **仅 HTTPS**"。 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="配置新前门的基本知识&quot;:::

1. 前门的配置发生在三个步骤中：添加默认前端主机，在后端池中添加后端，然后创建路由规则，以映射前端主机的路由行为。 选择 **+** _前端主机_ 上的 &quot;" border="false":::

1. 选择 " **添加** " 以保存请求转发的路由规则。

1. 选择 "查看" " **+ 创建** "，然后单击 " **创建**"，创建前门配置文件。 创建后，请立即进入资源。

## <a name="next-steps"></a>后续步骤

- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
- 在前门上了解有关 [URL 重定向的](front-door-url-redirect.md)详细信息。
