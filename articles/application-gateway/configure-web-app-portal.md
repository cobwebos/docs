---
title: 管理流量多租户应用程序，如应用服务 web 应用与 Azure 应用程序网关-门户
description: 本文指导如何将 Azure 应用服务 web 应用配置为在上现有的或新的应用程序网关的后端池成员。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176202"
---
# <a name="configure-app-service-with-application-gateway"></a>使用应用程序网关配置应用服务

应用程序网关，可将 Azure 应用服务 web 应用或其他多租户服务作为后端池成员。 

在本文中，学习如何：

> [!div class="checklist"]
>
> - 创建后端池，并向其添加一个应用服务
> - 使用"选择主机名"开关启用创建 HTTP 设置和自定义探测

## <a name="prerequisites"></a>必备组件

- 应用程序网关：如果没有现有的应用程序网关，请参阅如何[创建应用程序网关](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- 应用服务：如果没有现有的应用服务，请参阅[应用服务文档](https://docs.microsoft.com/azure/app-service/)。

## <a name="add-app-service-as-backend-pool"></a>将应用服务添加为后端池

1. 在 Azure 门户中，打开你应用程序网关配置视图。

2. 下**后端池**，单击**添加**若要创建新的后端池。

3. 提供给后端池的合适名称。 

4. 下**目标**，单击下拉列表中，然后选择**应用服务**作为选项。

5. 下拉列表中下一行**目标**下拉列表中将显示其中包含应用服务的列表。 在此下拉列表中，选择你想要添加为后端池成员，并单击添加应用服务。

   ![应用服务后端](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>创建应用服务的 HTTP 设置

1. 下**Http-settings**，单击**添加**若要创建新的 HTTP 设置。

2. 输入 HTTP 设置的名称和您可以启用或禁用基于 Cookie 的相关性，具体请根据要求。

3. 选择根据你的用例为 HTTP 或 HTTPS 协议。 

4. 选中的复选框**用于应用服务**将开启**选取主机名从后端地址创建探测**和**选取主机名从后端地址**选项。 此选项还将使用开关已启用自动创建一个探测，并将其关联到此 HTTP 设置。

5. 单击**确定**创建 HTTP 设置。

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>创建规则将侦听器、 后端池和 HTTP 设置绑定

1. 下**规则**，单击**基本**创建新的基本规则。

2. 提供合适的名称并选择侦听器它将接受传入请求的应用服务。

3. 在中**后端池**下拉列表中，选择上面创建的后端池。

4. 在中**HTTP 设置**下拉列表中，选择设置，您在上面创建的 HTTP。

5. 单击**确定**保存此规则。

   ![规则](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>限制访问

这些示例中部署的 Web 应用使用可通过 Internet 直接访问的公共 IP 地址。 这有助于在了解新功能和尝试新事物时获取疑难解答。 但若想将功能部署至生产环境，将需要添加更多限制。

若要限制对 Web 应用的访问权限，一种方法是使用 [Azure 应用服务静态 IP 限制](../app-service/app-service-ip-restrictions.md)。 例如，可以限制 Web 应用，使其仅接收来自应用程序网关的流量。 使用应用服务 IP 限制功能列出应用程序网关 VIP，作为具有访问权限的唯一地址。

## <a name="next-steps"></a>后续步骤

若要了解有关应用服务和其他应用程序网关的多租户支持的详细信息，请参阅[应用程序网关的多租户服务支持](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。

如果你的应用服务的响应重定向到应用服务的 URL，请参阅如何[对重定向到应用服务的 URL 问题进行故障排除](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url)。
