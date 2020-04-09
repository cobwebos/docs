---
title: 使用 Azure 门户创建具有 HTTP 到 HTTPS 重定向的前门
description: 了解如何使用 Azure 门户创建具有从 HTTP 重定向流量重定向到 HTTPS 的前门。
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: f1b8c033a3ec230d60c30f6168de8ce013a80ac6
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877994"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>使用 Azure 门户创建具有 HTTP 到 HTTPS 重定向的前门

可以使用 Azure 门户创建具有 TLS 终止证书[的前门](front-door-overview.md)。 路由规则用于将 HTTP 流量重定向到 HTTPS。

在本文中，学习如何：

> [!div class="checklist"]
> * 使用现有的 Web 应用资源创建前门
> * 使用 TLS/SSL 证书添加自定义域 
> * 设置在自定义域上重定向 HTTPS

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>使用现有的 Web 应用资源创建前门

1. 登录到 中的[https://portal.azure.com](https://portal.azure.com)Azure 门户。
2. 单击 Azure 门户左上角的“创建资源”****。
3. 使用搜索栏搜索**前门**，找到资源类型后，单击"**创建**"。
4. 选择订阅，然后使用现有资源组或创建新资源组。 请注意，UI 中请求的位置仅适用于资源组。 您的前门配置将部署在 Azure 前门的所有[POP 位置](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)。

    ![为新前门配置基础知识](./media/front-door-url-redirect/front-door-create-basics.png)

5. 单击 **"下一步**"输入配置选项卡。前门的配置分三个步骤进行 - 添加默认前端主机，在后端池中添加后端，然后创建路由规则以映射前端主机的路由行为。 

     ![前门配置设计器](./media/front-door-url-redirect/front-door-designer.png)

6. 单击**+**_前端主机_上的''图标以创建前端主机，为前门的默认前端主机输入全局唯一名称 （）。`\<**name**\>.azurefd.net` 单击"**添加**"以继续下一步。

     ![添加前端主机](./media/front-door-url-redirect/front-door-create-fehost.png)

7. 单击**+**_后端池_上的''图标以创建后端池。 提供后端池的名称，然后单击"**添加后端**"。
8. 选择"后端主机类型"作为_应用服务_。 选择托管 Web 应用的订阅，然后从**后端主机名**的下拉列表中选择特定的 Web 应用。
9. 单击"**添加"** 以保存后端，然后单击"再次**添加**"以保存后端池配置。  ![在后端池中添加后端](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. 单击**+**_路由规则_上的 '' 图标以创建路由。 提供路由的名称，例如"HttpToHttpsRedirect"，然后将 _"接受的协议"_ 字段设置为 **"仅 HTTP"。** 确保选择了适当的_前端主机_。  
11. 在 _"路径详细信息_"部分，将 _"路由类型_"设置为**重定向**，确保_重定向类型_设置为 **"找到"（302），** 重定向_协议_设置为**仅 HTTPS。** 
12. 单击"添加"将 HTTP 的路由规则保存到 HTTPS 重定向。
     ![将 HTTP 添加到 HTTPS 重定向路由](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. 添加用于处理 HTTPS 流量的另一个路由规则。 单击**+**_路由规则_上的''符号，并为路由提供名称，例如"默认转发路由"，然后将 _"接受的协议"_ 字段设置为 **"仅 HTTPS"。** 确保选择了适当的_前端主机_。
14. 在"路径详细信息"部分，将_路由类型_设置为 **"转发**"，确保选择了正确的后端池，并且 _"转发协议_**"设置为仅 HTTPS。** 
15. 单击"添加"以保存路由规则以进行请求转发。
     ![为 HTTPS 流量添加转发路由](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. 单击 **"审阅 + 创建**"，然后**创建**，以创建前门配置文件。 转到创建后的资源。

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>将自定义域添加到前门，并在其上启用 HTTPS
以下步骤演示如何在现有前门资源上添加自定义域，然后启用 HTTP 到 HTTPS 重定向。 

### <a name="add-a-custom-domain"></a>添加自定义域

在此示例中，为`www`子域添加 CNAME 记录（例如， `www.contosonews.com`。

#### <a name="create-the-cname-record"></a>创建 CNAME 记录

添加 CNAME 记录以将子域映射到前门的默认前端主机（，`<name>.azurefd.net`前门配置文件的名称）。 `<name>`

对于`www.contoso.com`域，例如，添加将名称`www`映射到`<name>.azurefd.net`的 CNAME 记录。

添加 CNAME 后，DNS 记录页与以下示例相似：

![CNAME 自定义域到前门](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>在前门上的自定义域上

1. 在"前门设计器"选项卡上，单击"前端主机"部分上的"+"图标以添加新的自定义域。 
2. 在自定义主机名字段中输入完全限定的自定义 DNS 名称，`www.contosonews.com`示例 。 
3. 验证从域到前门的 CNAME 映射后，单击"**添加"** 以添加自定义域。
4. 单击 **"保存"** 以提交更改。

![自定义域菜单](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>在自定义域上启用 HTTPS

1. 单击已添加的自定义域，并在 **"自定义域 HTTPS"** 部分下将状态更改为 **"已启用**"。
2. 您可以将**证书管理类型**设置为_前门管理_，以便由前门维护、管理和自动旋转免费证书。 您还可以选择使用与 Azure 密钥保管库一起存储的自定义 TLS/SSL 证书。 本教程假定使用前门托管证书。
![为自定义域启用 HTTPS](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. 单击 **"更新"** 以保存所选内容，然后单击"**保存**"。
4. 单击几分钟后**刷新**，然后再次单击自定义域以查看证书预配的进度。 

> [!WARNING]
> 为自定义域启用 HTTPS 可能需要几分钟时间，并且如果 CNAME 未直接映射到前门主机`<name>.azurefd.net`，则还要依赖于域所有权验证。 详细了解[如何为自定义域启用 HTTPS。](./front-door-custom-domain-https.md)

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>配置自定义域的路由规则

1. 单击之前创建的重定向路由规则。
2. 单击 Frontend 主机的下拉列表，并选择自定义域以为您的域应用此路由。
3. 单击“更新”****。
4. 对其他路由规则执行相同的操作，即为转发路由添加自定义域。
5. 单击 **"保存"** 以提交更改。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
- 了解有关前门[URL 重定向](front-door-url-redirect.md)的更多信息。
