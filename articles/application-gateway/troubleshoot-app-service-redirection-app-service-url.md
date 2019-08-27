---
title: Azure 应用程序通过应用服务的网关进行故障排除–重定向到应用服务 URL
description: 本文介绍如何排查将 Azure 应用程序网关与 Azure 应用服务配合使用时出现的重定向问题
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 4b233117bc0f967368aeac7baec8c4875aa16826
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051422"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>在应用程序网关上排查应用服务问题

了解如何诊断并解决 Azure App Service 用作 Azure 应用程序网关的后端目标时可能遇到的问题。

## <a name="overview"></a>概述

本文介绍如何解决以下问题:

> [!div class="checklist"]
> * 当存在重定向时, 将在浏览器中公开应用服务 URL。
> * 应用服务 ARRAffinity cookie 域设置为应用服务主机名 example.azurewebsites.net, 而不是原始主机。

当后端应用程序发送重定向响应时, 你可能需要将客户端重定向到与后端应用程序指定的 URL 不同的 URL。 在应用程序网关后托管应用服务并要求客户端执行到其相对路径的重定向时, 可能需要执行此操作。 例如, 从 contoso.azurewebsites.net/path1 到 contoso.azurewebsites.net/path2 的重定向。 

当应用服务发送重定向响应时, 它会在其响应的 location 标头中使用与从应用程序网关接收的请求相同的主机名。 例如, 客户端直接向 contoso.azurewebsites.net/path2 发出请求, 而不是通过应用程序网关 contoso.com/path2。 不希望绕过应用程序网关。

此问题可能是由于以下原因造成的:

- 已在应用服务中配置重定向。 只需在请求中添加一个尾随的斜杠即可配置重定向。
- 你具有 Azure Active Directory 身份验证, 这将导致重定向。

此外, 在应用程序网关后使用应用服务时, 与应用程序网关 (example.com) 关联的域名不同于应用服务的域名 (例如, example.azurewebsites.net)。 应用服务设置的 ARRAffinity cookie 的域值携带 example.azurewebsites.net 域名, 这并不是必需的。 原始主机名 example.com 应该是 cookie 中的域名值。

## <a name="sample-configuration"></a>示例配置

- HTTP 侦听器:基本或多站点
- 后端地址池：应用服务
- HTTP 设置:**从后端地址中选取主机名**
- 探测：**从启用的 HTTP 设置选取主机名**

## <a name="cause"></a>原因

应用服务是多租户服务, 因此它使用请求中的主机标头将请求路由到正确的终结点。 应用服务的默认域名 azurewebsites.net (例如, contoso.azurewebsites.net) 不同于应用程序网关的域名 (例如, contoso.com)。 

来自客户端的原始请求包含应用程序网关的域名 contoso.com, 作为主机名。 需要配置应用程序网关, 以便在将请求路由到应用服务后端时, 将原始请求中的主机名更改为应用服务的主机名。 在应用程序网关的 "HTTP 设置" 配置中使用开关 "**从后端地址中选取主机名**"。 在运行状况探测配置的**后端 HTTP 设置**中使用开关 "选取主机名"。



![应用程序网关更改主机名](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

当应用服务执行重定向时, 它将在 location 标头中使用重写的主机名 contoso.azurewebsites.net, 而不是 contoso.com 的原始主机名, 除非另外配置了。 检查下面的示例请求和响应标头。
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
在上面的示例中, 请注意, 响应标头的状态代码为 301, 用于重定向。 Location 标头包含应用服务的主机名, 而不是原始主机名 www.contoso.com。

## <a name="solution-rewrite-the-location-header"></a>解决方案：重写位置标头

将 location 标头中的主机名设置为应用程序网关的域名。 为此, 请创建一个包含条件的[重写规则](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers), 该规则评估响应中的 location 标头是否包含 azurewebsites.net。 它还必须执行操作来重写位置标头, 使其包含应用程序网关的主机名。 有关详细信息, 请参阅[如何重写 location 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)的说明。

> [!NOTE]
> HTTP 标头重写支持仅适用于应用程序网关的[Standard_v2 和 WAF_V2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 。 如果使用 v1 SKU, 则建议[从 v1 迁移到 v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2)。 需要使用 v2 SKU 可用的重写和其他[高级功能](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku)。

## <a name="alternate-solution-use-a-custom-domain-name"></a>备用解决方案:使用自定义域名

如果使用 v1 SKU, 则不能重写 location 标头。 此功能仅适用于 v2 SKU。 若要解决重定向问题, 还应将应用程序网关接收的同一主机名传递给应用服务, 而不是执行主机重写。

现在, 应用服务在同一原始主机标头上执行重定向 (如果有), 而不是指向应用程序网关, 而不是其自身。

您必须拥有自定义域并按照以下过程操作:

- 将该域注册到应用服务的自定义域列表。 你的自定义域中必须有指向应用服务的 FQDN 的 CNAME。 有关详细信息，请参阅[将现有的自定义 DNS 名称映射到 Azure 应用服务](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)。

    ![应用服务自定义域列表](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 应用服务已准备好接受主机名称 www.contoso.com。 更改 DNS 中的 CNAME 条目, 将其指向应用程序网关的 FQDN, 例如, appgw.eastus.cloudapp.azure.com。

- 执行 DNS 查询时, 请确保域 www.contoso.com 解析为应用程序网关的 FQDN。

- 将自定义探测设置为禁止**从后端 HTTP 设置选取主机名**。 在 Azure 门户中, 清除 "探测设置" 中的复选框。 在 PowerShell 中, 不要在**AzApplicationGatewayProbeConfig**命令中使用 **-PickHostNameFromBackendHttpSettings**开关。 在探测的 "主机名" 字段中, 输入应用服务的 FQDN example.azurewebsites.net。 从应用程序网关发送的探测请求会在主机标头中携带此 FQDN。

  > [!NOTE]
  > 在下一步中, 请确保自定义探测未关联到后端 HTTP 设置。 此时, 你的 HTTP 设置仍将**从后端地址开关启用 "选取主机名**"。

- 将应用程序网关的 HTTP 设置设置为禁用 "**从后端地址中选取主机名**"。 在 Azure 门户中, 清除复选框。 在 PowerShell 中, 不要在**AzApplicationGatewayBackendHttpSettings**命令中使用 **-PickHostNameFromBackendAddress**开关。

- 将自定义探测关联回后端 HTTP 设置, 并验证后端是否正常。

- 应用程序网关现在应将同一主机名 www.contoso.com 转发到应用服务。 重定向在同一主机名上发生。 检查下面的示例请求和响应标头。

若要使用 PowerShell 实现前面的步骤, 请使用下面的示例 PowerShell 脚本。 请注意, 我们如何在探测和 HTTP 设置配置中使用 **-PickHostname**开关。

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>后续步骤

如果前面的步骤未解决此问题, 请打开[支持票证](https://azure.microsoft.com/support/options/)。
