---
title: 使用 Azure 应用程序网关管理发往多租户应用（例如应用服务 Web 应用）的流量 - 门户
description: 本文提供有关如何在现有或新的应用程序网关上将 Azure 应用服务 Web 应用配置为后端池成员的指导。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: dee4859c57172a703517848510a31b70ff1f24cd
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370429"
---
# <a name="configure-app-service-with-application-gateway"></a>使用应用程序网关配置应用服务

由于应用服务是一个多租户服务, 而不是一种专门的部署, 因此它使用传入请求中的主机标头来将请求解析为正确的应用服务终结点。 通常, 应用程序的 DNS 名称, 而应用程序网关与应用程序网关关联的 DNS 名称前方应用服务与后端应用服务的域名不同。 因此，应用程序网关收到的原始请求中的主机标头不同于后端服务的主机名。 正因如此，除非从应用程序网关发往后端的请求中的主机标头已更改为后端服务的主机名，否则多租户后端无法将请求解析为正确的终结点。

应用程序网关提供一个`Pick host name from backend address`称为的开关, 该开关在请求从应用程序网关路由到后端时, 使用后端的主机名替代请求中的主机标头。 此功能支持多租户后端, 如 Azure 应用服务和 API 管理。 

在本文中，学习如何：

> [!div class="checklist"]
>
> - 创建后端池并将一个应用服务添加到其中
> - 在启用“选取主机名”开关的情况下创建 HTTP 设置和自定义探测

## <a name="prerequisites"></a>系统必备

- 应用程序网关：如果没有应用程序网关，请参阅如何[创建应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- 应用服务：如果没有应用服务，请参阅[应用服务文档](https://docs.microsoft.com/azure/app-service/)。

## <a name="add-app-service-as-backend-pool"></a>将应用服务添加为后端池

1. 在 Azure 门户中，打开应用程序网关的配置视图。

2. 在“后端池”下，单击“添加”以创建新的后端池。

3. 为后端池提供适当的名称。 

4. 在“目标”下，单击下拉列表并选择“应用服务”作为选项。

5. 紧靠在“目标”下拉列表的下面会显示另一个下拉列表，其中包含应用服务的列表。 在此下拉列表中，选择要添加为后端池成员的应用服务，然后单击“添加”。

   ![应用服务后端](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > 下拉列表将只填充与应用程序网关位于同一订阅中的应用服务。 如果要使用的应用服务所使用的订阅不同于应用程序网关所在的订阅, 请选择 " **IP 地址或主机名**" 选项, 而不是在 "**目标**" 下拉列表中选择 "**应用服务**", 然后输入主机名 (示例)。 azurewebsites.net)。

## <a name="create-http-settings-for-app-service"></a>创建应用服务的 HTTP 设置

1. 在“HTTP 设置”下，单击“添加”以创建新的 HTTP 设置。

2. 输入该 HTTP 设置的名称，并根据要求启用或禁用“基于 Cookie 的相关性”。

3. 根据用例选择“HTTP”或“HTTPS”作为协议。 

   > [!NOTE]
   > 如果你选择 HTTPS, 则无需将任何身份验证证书或受信任的根证书上传到应用服务后端的白名单, 因为应用服务是受信任的 Azure 服务。

4. 选中 "用于**应用服务**的" 框。 请注意, 会`Create a probe with pick host name from backend address`自动`Pick host name from backend address`启用交换机和。`Pick host name from backend address` 当请求从应用程序网关路由到后端时, 将用后端的主机名替代请求中的主机标头。  

   `Create a probe with pick host name from backend address`将自动创建运行状况探测, 并将其关联到此 HTTP 设置。 不需要为此 HTTP 设置创建任何其他运行状况探测。 您可以检查运行状况探测列表中是否已<HTTP Setting name>添加新的具有该名称<Unique GUID>的探测, 并且它已经具有开关`Pick host name from backend http settings enabled`。

   如果你已有一个或多个正在用于应用服务的 http 设置, 并且这些 http 设置使用的协议与你要在其中创建的 http 设置相同, 则`Create a probe with pick host name from backend address`将显示一个下拉列表来选择其中一个 cu 探测。 这是因为, 由于已存在一个包含应用服务的 HTTP 设置, 因此还存在一个具有开关`Pick host name from backend http settings enabled`的运行状况探测。 从下拉列表中选择 "自定义探测"。

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

当应用服务将重定向响应发送到客户端以重定向到其相对路径 (例如, 从 contoso.azurewebsites.net/path1 到 contoso.azurewebsites.net/path2 的重定向) 时, 它将在其响应的 location 标头中使用相同的主机名作为请求中的一个, 它从应用程序网关接收。 因此, 客户端将直接向 contoso.azurewebsites.net/path2 发出请求, 而不是通过应用程序网关 (contoso.com/path2)。 不需要跳过应用程序网关。

如果在用例中, 应用服务需要将重定向响应发送到客户端, 请执行[其他步骤以重写位置标头](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration)。

## <a name="restrict-access"></a>限制访问

这些示例中部署的 Web 应用使用可通过 Internet 直接访问的公共 IP 地址。 这有助于在了解新功能和尝试新事物时获取疑难解答。 但若想将功能部署至生产环境，将需要添加更多限制。

若要限制对 Web 应用的访问权限，一种方法是使用 [Azure 应用服务静态 IP 限制](../app-service/app-service-ip-restrictions.md)。 例如，可以限制 Web 应用，使其仅接收来自应用程序网关的流量。 使用应用服务 IP 限制功能列出应用程序网关 VIP，作为具有访问权限的唯一地址。

## <a name="next-steps"></a>后续步骤

若要详细了解应用服务和应用程序网关的其他多租户支持，请参阅[应用程序网关的多租户服务支持](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。
