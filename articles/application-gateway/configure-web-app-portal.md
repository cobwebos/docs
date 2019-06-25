---
title: 使用 Azure 应用程序网关管理发往多租户应用（例如应用服务 Web 应用）的流量 - 门户
description: 本文提供有关如何在现有或新的应用程序网关上将 Azure 应用服务 Web 应用配置为后端池成员的指导。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831148"
---
# <a name="configure-app-service-with-application-gateway"></a>使用应用程序网关配置应用服务

使用 Azure 应用程序网关可将应用服务 Web 应用或其他多租户服务配置为后端池成员。 

在本文中，学习如何：

> [!div class="checklist"]
>
> - 创建后端池并将一个应用服务添加到其中
> - 在启用“选取主机名”开关的情况下创建 HTTP 设置和自定义探测

## <a name="prerequisites"></a>必备组件

- 应用程序网关：如果没有应用程序网关，请参阅如何[创建应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- 应用服务：如果没有应用服务，请参阅[应用服务文档](https://docs.microsoft.com/azure/app-service/)。

## <a name="add-app-service-as-backend-pool"></a>将应用服务添加为后端池

1. 在 Azure 门户中，打开应用程序网关的配置视图。

2. 在“后端池”下，单击“添加”以创建新的后端池。  

3. 为后端池提供适当的名称。 

4. 在“目标”下，单击下拉列表并选择“应用服务”作为选项。  

5. 紧靠在“目标”下拉列表的下面会显示另一个下拉列表，其中包含应用服务的列表。  在此下拉列表中，选择要添加为后端池成员的应用服务，然后单击“添加”。

   ![应用服务后端](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>创建应用服务的 HTTP 设置

1. 在“HTTP 设置”下，单击“添加”以创建新的 HTTP 设置。  

2. 输入该 HTTP 设置的名称，并根据要求启用或禁用“基于 Cookie 的相关性”。

3. 根据用例选择“HTTP”或“HTTPS”作为协议。 

4. 选中“用于应用服务”对应的框。这会启用“通过从后端地址中选取主机名来创建探测”和“从后端地址中选取主机名”选项。    此选项还会创建一个启用该开关的探测，并将其关联到此 HTTP 设置。

5. 单击“确定”以创建该 HTTP 设置。 

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>创建用于绑定侦听器、后端池和 HTTP 设置的规则

1. 在“规则”下，单击“基本”以创建新的基本规则。  

2. 提供适当的名称，并选择用于接受应用服务传入请求的侦听器。

3. 在“后端池”下拉列表中，选择前面创建的后端池。 

4. 在“HTTP 设置”下拉列表中，选择前面创建的 HTTP 设置。 

5. 单击“确定”以保存此规则。 

   ![规则](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>限制访问

这些示例中部署的 Web 应用使用可通过 Internet 直接访问的公共 IP 地址。 这有助于在了解新功能和尝试新事物时获取疑难解答。 但若想将功能部署至生产环境，将需要添加更多限制。

若要限制对 Web 应用的访问权限，一种方法是使用 [Azure 应用服务静态 IP 限制](../app-service/app-service-ip-restrictions.md)。 例如，可以限制 Web 应用，使其仅接收来自应用程序网关的流量。 使用应用服务 IP 限制功能列出应用程序网关 VIP，作为具有访问权限的唯一地址。

## <a name="next-steps"></a>后续步骤

若要详细了解应用服务和应用程序网关的其他多租户支持，请参阅[应用程序网关的多租户服务支持](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。

如果应用服务的响应重定向到应用服务的 URL，请参阅如何[排查重定向到应用服务 URL 的问题](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)。
