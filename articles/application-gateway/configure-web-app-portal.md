---
title: 使用门户管理到多租户应用的流量
titleSuffix: Azure Application Gateway
description: 本文提供有关如何在现有或新的应用程序网关上将 Azure 应用服务 Web 应用配置为后端池成员的指导。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075161"
---
# <a name="configure-app-service-with-application-gateway"></a>使用应用程序网关配置应用服务

由于应用服务是多租户服务而不是专用部署，因此会使用传入请求中的主机标头来解析发给正确应用服务终结点的请求。 通常，应用程序的 DNS 名称（也是与面向应用服务的应用程序网关关联的 DNS 名称）不同于后端应用服务的域名。 因此，应用程序网关收到的原始请求中的主机标头不同于后端服务的主机名。 正因如此，除非从应用程序网关发往后端的请求中的主机标头已更改为后端服务的主机名，否则多租户后端无法将请求解析为正确的终结点。

应用程序网关提供一个名为 `Pick host name from backend address` 的开关，将请求从应用程序网关路由到后端时，该开关将使用后端的主机名来替代请求中的主机标头。 使用此功能可以支持 Azure 应用服务和 API 管理等多租户后端。 

在本文中，学习如何：

> [!div class="checklist"]
>
> - 创建后端池并将一个应用服务添加到其中
> - 在启用“选取主机名”开关的情况下创建 HTTP 设置和自定义探测

## <a name="prerequisites"></a>先决条件

- 应用程序网关：如果没有现有的应用程序网关，请参阅如何[创建应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- 应用服务：如果你没有现有的应用服务，请参阅[应用服务文档](https://docs.microsoft.com/azure/app-service/)。

## <a name="add-app-service-as-backend-pool"></a>将应用服务添加为后端池

1. 在 Azure 门户中，打开应用程序网关的配置视图。

2. 在“后端池”下，单击“添加”以创建新的后端池。

3. 为后端池提供适当的名称。 

4. 在“目标”下，单击下拉列表并选择“应用服务”作为选项。

5. 紧靠在“目标”下拉列表的下面会显示另一个下拉列表，其中包含应用服务的列表。 在此下拉列表中，选择要添加为后端池成员的应用服务，然后单击“添加”。

   ![应用服务后端](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > 下拉列表中只填充了与应用程序网关位于同一订阅中的应用服务。 若要使用与应用程序网关不同的订阅中的应用服务，请在“目标”下拉列表中选择“应用服务”，选择“IP 地址或主机名”选项，然后输入应用服务的主机名（例如 azurewebsites.net）。

## <a name="create-http-settings-for-app-service"></a>创建应用服务的 HTTP 设置

1. 在“HTTP 设置”下，单击“添加”以创建新的 HTTP 设置。

2. 输入该 HTTP 设置的名称，并根据要求启用或禁用“基于 Cookie 的相关性”。

3. 根据用例选择“HTTP”或“HTTPS”作为协议。 

   > [!NOTE]
   > 如果选择“HTTPS”，则无需上传任何身份验证证书或受信任的根证书即可将应用服务后端加入允许列表，因为应用服务是受信任的 Azure 服务。

4. 选中“用于应用服务”对应的框。 请注意，开关 `Create a probe with pick host name from backend address` 和 `Pick host name from backend address` 会自动启用。`Pick host name from backend address` 会在将请求从应用程序网关路由到后端时，使用后端的主机名来替代请求中的主机标头。  

   `Create a probe with pick host name from backend address` 会自动创建运行状况探测，并将其关联到此 HTTP 设置。 无需为此 HTTP 设置创建任何其他运行状况探测。 可以检查名为 <HTTP Setting name><Unique GUID> 的新探测是否已添加运行状况探测列表并已包含开关 `Pick host name from backend http settings enabled`。

   如果你已将一个或多个 HTTP 设置用于应用服务，并且这些 HTTP 设置使用的协议与正在创建的设置中使用的协议相同，则你不会获得 `Create a probe with pick host name from backend address` 开关，而是看到一个下拉列表，可在其中选择某个自定义探测。 这是因为，已存在一个包含应用服务的 HTTP 设置，因此也存在一个具有开关 `Pick host name from backend http settings enabled` 的运行状况探测。 从下拉列表中选择该自定义探测。

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

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>重定向到应用服务的相对路径时的其他配置

当应用服务将重定向响应发送到客户端以重定向到其相对路径（例如，从 contoso.azurewebsites.net/path1 到 contoso.azurewebsites.net/path2 的重定向）时，它会在其响应的 location 标头中使用与从应用程序网关接收的请求中的主机名相同的主机名。 因此，客户端将直接向 contoso.azurewebsites.net/path2 发出请求，而不是通过应用程序网关（contoso.com/path2）。 不应该绕过应用程序网关。

如果在你的用例中，应用服务有时需要将重定向响应发送到客户端，请执行[重写位置标头的附加步骤](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration)。

## <a name="restrict-access"></a>限制访问

这些示例中部署的 Web 应用使用可通过 Internet 直接访问的公共 IP 地址。 这有助于在了解新功能和尝试新事物时获取疑难解答。 但若想将功能部署至生产环境，将需要添加更多限制。

若要限制对 Web 应用的访问权限，一种方法是使用 [Azure 应用服务静态 IP 限制](../app-service/app-service-ip-restrictions.md)。 例如，可以限制 Web 应用，使其仅接收来自应用程序网关的流量。 使用应用服务 IP 限制功能列出应用程序网关 VIP，作为具有访问权限的唯一地址。

## <a name="next-steps"></a>后续步骤

若要详细了解应用服务和应用程序网关的其他多租户支持，请参阅[应用程序网关的多租户服务支持](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。
