---
title: 创建支持 HTTP 到 HTTPS 重定向使用 Azure 门户的第一道防线
description: 了解如何创建从 HTTP 到 HTTPS 使用 Azure 门户的重定向流量第一道防线。
services: front-door
author: sharad4u
ms.service: front-door
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: a07b19c49630cc925e719aaa1d46476a1edc58f5
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332443"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>创建支持 HTTP 到 HTTPS 重定向使用 Azure 门户的第一道防线

您可以使用 Azure 门户创建[第一道防线](front-door-overview.md)SSL 终端的证书。 路由规则用于将 HTTP 流量重定向到 HTTPS。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建与现有的 Web 应用资源的第一道防线
> * 添加自定义域使用 SSL 证书 
> * 安装程序的自定义域 HTTPS 的重定向

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>创建与现有的 Web 应用资源的第一道防线

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
2. 单击 Azure 门户左上角的“创建资源”  。
3. 搜索**第一道防线**使用的搜索栏和后发现的资源类型，单击**创建**。
4. 选择订阅然后使用现有资源组，或创建一个新。 请注意，资源组只是要求在 UI 中的位置。 你的第一道防线配置将部署在所有[Azure 第一道防线的 POP 位置](https://docs.microsoft.com/azure/frontdoor/front-door-faq#what-are-the-pop-locations-for-azure-front-door-service)。

    ![为新的第一道防线配置基础知识](./media/front-door-url-redirect/front-door-create-basics.png)

5. 单击**下一步**输入配置选项卡。第一道防线的配置发生在三个步骤-添加默认前端主机、 后端池中添加后端以及然后创建路由规则，以将映射为前端主机路由的行为。 

     ![第一道防线配置设计器](./media/front-door-url-redirect/front-door-designer.png)

6. 单击 **+** 图标_前端主机_若要创建的前端主机，请输入前端的默认宿主的全局唯一名称在第一道防线 (`\<**name**\>.azurefd.net`)。 单击**添加**可前进到下一步。

     ![添加一个前端的主机](./media/front-door-url-redirect/front-door-create-fehost.png)

7. 单击 **+** 图标_后端池_创建后端池。 提供后端池的名称，然后单击**添加后端**。
8. 选择后端主机类型作为_应用服务_。 选择你的 web 应用所在的订阅，然后从下拉列表选择特定的 web 应用**后端主机名**。
9. 单击**外**以保存在后端，然后单击**添加**次以保存后端池配置。 ![在后端池中添加后端](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. 单击 **+** 图标_路由规则_创建路由。 提供的名称路由，说 HttpToHttpsRedirect，然后设置_接受协议_字段**仅 HTTP**。 确保适当_前端主机_处于选中状态。  
11. 上_路由的详细信息_部分中，设置_路由类型_到**重定向**，，请确保_重定向类型_设置为**找到 (302)** 并_协议重定向_设置为**仅 HTTPS**。 
12. 单击添加以保存 HTTP 到 HTTPS 重定向的路由规则。
     ![添加 HTTP 到 HTTPS 重定向的路由](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. 添加用于处理的 HTTPS 通信的另一个路由规则。 单击 **+** 登录_路由规则_并提供一个名称，用于说 DefaultForwardingRoute，然后设置_接受协议_字段向**仅 HTTPS**。 确保适当_前端主机_处于选中状态。
14. 在路由的详细信息部分中，设置_路由类型_到**向前**，确保选择正确的后端池和_转发协议_设置为**仅 HTTPS**。 
15. 单击添加以保存请求转发的路由规则。
     ![添加正向的 HTTPS 流量路由](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. 单击**查看 + 创建**，然后**创建**，以创建你的第一道防线配置文件。 请转到一次创建的资源。

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>将自定义域添加到您指定的地址并在其上启用 HTTPS
以下步骤展示如何在现有的第一道防线资源上添加自定义域，然后启用 HTTP 到 HTTPS 重定向对它。 

### <a name="add-a-custom-domain"></a>添加自定义域

在此示例中，添加的 CNAME 记录`www`子域 (例如， `www.contosonews.com`)。

#### <a name="create-the-cname-record"></a>创建 CNAME 记录

添加 CNAME 记录，以便将子域映射到您指定的地址的默认前端主机 (`<name>.azurefd.net`，其中`<name>`是第一道防线配置文件的名称)。

有关`www.contoso.com`域，例如，添加映射名称的 CNAME 记录`www`到`<name>.azurefd.net`。

添加 CNAME 后，DNS 记录页与以下示例相似：

![将自定义域 CNAME 指向第一道防线](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>加入自定义域上在第一道防线

1. 在第一道防线设计器选项卡上，单击 + 前端主机上的图标部分以添加新的自定义域。 
2. 在自定义主机名称字段中输入完全限定的自定义 DNS 名称的示例`www.contosonews.com`。 
3. 从域到您指定的地址的 CNAME 映射验证后，单击**添加**要添加自定义域。
4. 单击**保存**提交所做的更改。

![自定义域菜单](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>你的自定义域上启用 HTTPS

1. 单击添加自定义域上和部分下**自定义域 HTTPS**，将状态更改为**已启用**。
2. 可以将保留**证书管理类型**设置为_托管第一道防线_保留为免费的证书，可进行管理，并通过第一道防线 autorotated。 您还可以选择使用 Azure 密钥保管库与存储在您自己自定义 SSL 证书。 本教程的第一道防线使用管理证书。
![为自定义域启用 HTTPS](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. 单击**更新**以保存所做选择，然后单击**保存**。
4. 单击**刷新**后几分钟，然后单击自定义的域再次以查看证书预配的进度。 

> [!WARNING]
> 启用 HTTPS 的自定义域可能需要几分钟，也取决于在域所有权验证如果 CNAME 直接未映射到您的第一道防线主机`<name>.azurefd.net`。 详细了解如何[如何为自定义域启用 HTTPS](./front-door-custom-domain-https.md)。

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>配置自定义域的路由规则

1. 单击前面创建的重定向路由规则。
2. 前端主机在下拉列表中单击并选择你的自定义域以应用你的域以及此路由。
3. 单击“更新”  。
4. 转发路线要添加自定义域，即执行其他路由规则以及相同的操作。
5. 单击**保存**提交所做的更改。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
- 详细了解如何[URL 重定向在第一道防线](front-door-url-redirect.md)。
