---
title: 使用门户管理多租户应用的流量
titleSuffix: Azure Application Gateway
description: 本文提供有关如何在现有或新的应用程序网关上将 Azure 应用服务 Web 应用配置为后端池成员的指导。
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: victorh
ms.openlocfilehash: df92e08e91761d77c606ccb5389eee7dc219c101
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323358"
---
# <a name="configure-app-service-with-application-gateway"></a>使用应用程序网关配置应用服务

由于应用服务是多租户服务而不是专用部署，因此会使用传入请求中的主机标头来解析发给正确应用服务终结点的请求。 通常，应用程序的 DNS 名称（也是与面向应用服务的应用程序网关关联的 DNS 名称）不同于后端应用服务的域名。 因此，应用程序网关收到的原始请求中的主机标头不同于后端服务的主机名。 正因如此，除非从应用程序网关发往后端的请求中的主机标头已更改为后端服务的主机名，否则多租户后端无法将请求解析为正确的终结点。

应用程序网关提供一个名为 `Pick host name from backend target` 的开关，将请求从应用程序网关路由到后端时，该开关将使用后端的主机名来替代请求中的主机标头。 使用此功能可以支持 Azure 应用服务和 API 管理等多租户后端。 

在本文中，学习如何：

- 编辑后端池并向其添加应用服务
- 编辑 HTTP 设置并启用 "选取主机名" 开关

## <a name="prerequisites"></a>先决条件

- 应用程序网关：创建不含后端池目标的应用程序网关。 有关详细信息，请参阅 [快速入门：包含 Azure 应用程序网关的直接 web 流量-Azure 门户](quick-create-portal.md)

- 应用服务：如果没有应用服务，请参阅[应用服务文档](https://docs.microsoft.com/azure/app-service/)。

## <a name="add-app-service-as-backend-pool"></a>将应用服务添加为后端池

1. 在 Azure 门户中，选择你的应用程序网关。

2. 在 " **后端池**" 下，选择后端池。

4. 在 " **目标类型**" 下，选择 " **应用服务**"。

5. 在 " **目标** " 下，选择应用服务。

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="应用服务后端":::
   
   > [!NOTE]
   > 下拉列表仅填充与你的应用程序网关位于同一订阅中的应用服务。 若要使用与应用程序网关不同的订阅中的应用服务，请在“目标”下拉列表中选择“应用服务”，选择“IP 地址或主机名”选项，然后输入应用服务的主机名（例如   应用服务的 azurewebsites.net) 。
1. 选择“保存”。

## <a name="edit-http-settings-for-app-service"></a>编辑应用服务的 HTTP 设置

1. 在 " **HTTP 设置**" 下，选择现有 HTTP 设置。

2. 在 " **替代新主机名**" 下，选择 **"是"**。
3. 在 " **主机名替代**" 下，选择 " **从后端目标选取主机名**"。
4. 选择“保存”。

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="应用服务后端":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>重定向到应用服务的相对路径时的其他配置

当应用服务将重定向响应发送到客户端以重定向到其相对路径 (例如，重定向 `contoso.azurewebsites.net/path1` 到 `contoso.azurewebsites.net/path2`) 时，它会在其响应的 location 标头中使用与从应用程序网关接收的请求中的相同主机名。 因此，客户端将直接发出请求， `contoso.azurewebsites.net/path2` 而不是通过应用程序网关 (`contoso.com/path2`) 。 不应该绕过应用程序网关。

如果在你的用例中，应用服务有时需要将重定向响应发送到客户端，请执行[重写位置标头的附加步骤](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration)。

## <a name="restrict-access"></a>限制访问

这些示例中部署的 Web 应用使用可通过 Internet 直接访问的公共 IP 地址。 这有助于在了解新功能和尝试新事物时获取疑难解答。 但若想将功能部署至生产环境，将需要添加更多限制。

若要限制对 Web 应用的访问权限，一种方法是使用 [Azure 应用服务静态 IP 限制](../app-service/app-service-ip-restrictions.md)。 例如，可以限制 Web 应用，使其仅接收来自应用程序网关的流量。 使用应用服务 IP 限制功能列出应用程序网关 VIP，作为具有访问权限的唯一地址。

## <a name="next-steps"></a>后续步骤

若要详细了解应用服务和应用程序网关的其他多租户支持，请参阅[应用程序网关的多租户服务支持](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。
