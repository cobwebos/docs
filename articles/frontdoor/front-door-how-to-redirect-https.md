---
title: 使用 Azure 门户的 HTTP 到 HTTPS 重定向创建前门
description: 了解如何使用 Azure 门户通过 HTTP 到 HTTPS 的重定向流量创建前门。
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: f1b8c033a3ec230d60c30f6168de8ce013a80ac6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80877994"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>使用 Azure 门户的 HTTP 到 HTTPS 重定向创建前门

你可以使用 Azure 门户创建具有 TLS 终止证书的[前门](front-door-overview.md)。 路由规则用于将 HTTP 流量重定向到 HTTPS。

在本文中，学习如何：

> [!div class="checklist"]
> * 使用现有的 Web 应用资源创建前门
> * 使用 TLS/SSL 证书添加自定义域 
> * 在自定义域上设置 HTTPS 重定向

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>使用现有的 Web 应用资源创建前门

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
2. 单击 Azure 门户左上角的“创建资源”****。
3. 使用搜索栏搜索**前门**，找到资源类型后，单击 "**创建**"。
4. 选择一个订阅，然后使用现有的资源组或创建一个新的资源组。 请注意，UI 中请求的位置仅适用于资源组。 你的前门配置将部署到所有[Azure 前门的 POP 位置](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)。

    ![配置新前门的基本知识](./media/front-door-url-redirect/front-door-create-basics.png)

5. 单击 "**下一步**" 输入配置选项卡。前门的配置发生在三个步骤中：添加默认前端主机，在后端池中添加后端，然后创建路由规则，以映射前端主机的路由行为。 

     ![前门配置设计器](./media/front-door-url-redirect/front-door-designer.png)

6. 单击_前端主机_上的 "`\<**name**\>.azurefd.net`**+**" 图标创建前端主机，为前门输入默认前端主机的全局唯一名称（）。 单击 "**添加**" 以继续进行下一步。

     ![添加前端主机](./media/front-door-url-redirect/front-door-create-fehost.png)

7. 单击**+**_后端池_上的 "" 图标创建后端池。 提供后端池的名称，然后单击 "**添加后端**"。
8. 选择 "后端" 主机类型作为_应用服务_。 选择托管 web 应用的订阅，然后从下拉列表中选择 "**后端主机名**"。
9. 单击 "**添加**" 以保存后端，然后再次单击 "**添加**" 以保存后端池配置。  ![在后端池中添加后端](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. 单击**+**_路由规则_上的 "" 图标创建路由。 提供路由的名称，如 "HttpToHttpsRedirect"，然后将 "_接受的协议_" 字段设置为 **"仅 HTTP"**。 确保选择适当的_前端主机_。  
11. 在 "_路由详细信息_" 部分中，将 "_路由类型_" 设置为 "**重定向**"，确保将 "_重定向类型_" 设置为 "**找到（302）** "，并重_定向协议_设置为 "**仅 HTTPS**" 
12. 单击 "添加" 以保存用于 HTTP 到 HTTPS 重定向的路由规则。
     ![向 HTTPS 重定向路由添加 HTTP](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. 添加另一个用于处理 HTTPS 流量的路由规则。 单击 "登录**+**_路由规则_" 和 "提供路由名称" （例如 "DefaultForwardingRoute"），然后将 "_接受的协议_" 字段设置为 **"仅 HTTPS"**。 确保选择适当的_前端主机_。
14. 在 "路由详细信息" 部分中，将 "_路由类型_" 设置为 "**转发**"，确保选择了正确的后端池，并且_转发协议_设置为 "**仅 HTTPS**"。 
15. 单击 "添加" 以保存请求转发的路由规则。
     ![为 HTTPS 流量添加转发路由](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. 单击 "**查看 + 创建**"，然后单击 "**创建**"，创建前门配置文件。 创建后，请立即进入资源。

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>将自定义域添加到前门并在其上启用 HTTPS
以下步骤展示了如何在现有前门资源上添加自定义域，并在其上启用 HTTP 到 HTTPS 的重定向。 

### <a name="add-a-custom-domain"></a>添加自定义域

在此示例中，将为`www`子域（例如`www.contosonews.com`）添加 CNAME 记录。

#### <a name="create-the-cname-record"></a>创建 CNAME 记录

添加一条 CNAME 记录，以便将子域映射到前门的默认前端主机（`<name>.azurefd.net`，其中`<name>`是前门配置文件的名称）。

例如， `www.contoso.com`对于域，添加将名称`www`映射到`<name>.azurefd.net`的 CNAME 记录。

添加 CNAME 后，DNS 记录页与以下示例相似：

![向前门的 CNAME 自定义域](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>在前门上载入自定义域

1. 在 "前门设计器" 选项卡上，单击 "前端主机" 部分的 "+" 图标以添加新的自定义域。 
2. 在 "自定义主机名" 字段中输入完全限定的自定义`www.contosonews.com`DNS 名称，例如。 
3. 验证从域到前门的 CNAME 映射后，请单击 "**添加**" 以添加自定义域。
4. 单击 "**保存**" 以提交更改。

![自定义域菜单](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>在自定义域上启用 HTTPS

1. 单击已添加的自定义域，并在**自定义域 HTTPS**部分下，将状态更改为 "**已启用**"。
2. 你可以将 "**证书管理类型**" 设置为 "_管理_"，使其按前门维护、管理和 autorotated 的免费证书。 你还可以选择使用存储在 Azure Key Vault 中的自定义 TLS/SSL 证书。 本教程假定使用前门托管证书。
![为自定义域启用 HTTPS](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. 单击 "**更新**" 以保存所选内容，然后单击 "**保存**"。
4. 几分钟后单击 "**刷新**"，然后再次单击自定义域，查看证书设置的进度。 

> [!WARNING]
> 为自定义域启用 HTTPS 可能需要几分钟的时间，并且如果 CNAME 未直接映射到前台主机`<name>.azurefd.net`，则还会依赖于域所有权验证。 详细了解[如何为自定义域启用 HTTPS](./front-door-custom-domain-https.md)。

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>配置自定义域的路由规则

1. 单击前面创建的重定向路由规则。
2. 单击 "前端主机" 的下拉列表，并选择你的自定义域，同时为你的域应用此路由。
3. 单击“更新”  。
4. 为其他路由规则执行相同的操作，也就是，为转发路由添加自定义域。
5. 单击 "**保存**" 提交更改。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
- 在前门上了解有关[URL 重定向的](front-door-url-redirect.md)详细信息。
