---
title: Azure 应用程序通过应用服务的网关进行故障排除–重定向到应用服务 URL
description: 本文介绍如何排查将 Azure 应用程序网关与 Azure 应用服务配合使用时出现的重定向问题
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: ef2bbf8804e96a3e25f053d189c6d85bfa845b0b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833179"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>排查应用程序网关中的应用服务问题

了解如何诊断并解决将 Azure 应用服务用作后端目标时 Azure 应用程序网关可能出现的问题。

## <a name="overview"></a>概述

本文介绍如何排查以下问题：

> [!div class="checklist"]
> * 发生重定向时，应用服务 URL 在浏览器中公开。
> * 应用服务 ARRAffinity cookie 域设置为应用服务主机名 example.azurewebsites.net，而不是原始主机。

当后端应用程序发送重定向响应时，你可能希望将客户端重定向到不同的 URL，而不是后端应用程序指定的 URL。 当应用服务托管在应用程序网关后面，并要求客户端重定向到其相对路径时，你可能希望这样做。 例如，从 contoso.azurewebsites.net/path1 到 contoso.azurewebsites.net/path2 的重定向。 

当应用服务发送重定向响应时，它会在其响应的位置标头中，使用它从应用程序网关收到的请求中的相同主机名。 例如，客户端直接向 contoso.azurewebsites.net/path2 发出请求，而不是通过应用程序网关 contoso.com/path2。 你不希望绕过应用程序网关。

此问题可能是以下主要原因造成的：

- 在应用服务中配置了重定向。 只需在请求中添加一个尾随的斜杠即可配置重定向。
- Azure Active Directory 身份验证导致重定向。

此外，在应用程序网关后使用应用服务时，与应用程序网关（example.com）关联的域名不同于应用服务的域名（例如，example.azurewebsites.net）。 应用服务设置的 ARRAffinity cookie 的域值携带 example.azurewebsites.net 域名，这并不是必需的。 原始主机名 example.com 应是 Cookie 中的域名值。

## <a name="sample-configuration"></a>示例配置

- HTTP 侦听器：基本或多站点
- 后端地址池：应用服务
- HTTP 设置：**从后端地址中选取主机名**
- 探测：**从启用的 HTTP 设置中选取主机名**

## <a name="cause"></a>原因

应用服务是多租户服务，因此它会使用请求中的主机标头将请求路由到正确的终结点。 应用服务的默认域名 azurewebsites.net （例如，contoso.azurewebsites.net）不同于应用程序网关的域名（例如，contoso.com）。 

来自客户端的原始请求包含应用程序网关的域名 contoso.com 作为主机名。 需要配置应用程序网关，以便在将请求路由到应用服务后端时，将原始请求中的主机名更改为应用服务的主机名。 在应用程序网关的 HTTP 设置配置中使用开关“从后端地址中选取主机名”。 在运行状况探测配置中使用开关“从后端 HTTP 设置中选取主机名”。



![应用程序网关更改主机名](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

当应用服务执行重定向时，它将在 location 标头中使用重写的主机名 contoso.azurewebsites.net，而不是 contoso.com 的原始主机名，除非另外配置了。 检查以下示例请求和响应标头。
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
在以上示例中，可以看到响应标头包含 301 重定向状态代码。 Location 标头具有应用服务的主机名，而不是原始主机名 `www.contoso.com`。

## <a name="solution-rewrite-the-location-header"></a>解决方案：重写位置标头

将 location 标头中的主机名设置为应用程序网关的域名。 为此，请创建一个包含条件的[重写规则](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)，该规则评估响应中的 location 标头是否包含 azurewebsites.net。 该规则还必须执行相应的操作来重写 location 标头，使其包含应用程序网关的主机名。 有关详细信息，请参阅有关[如何重写 location 标头](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)的说明。

> [!NOTE]
> HTTP 标头重写支持仅适用于应用程序网关的 [Standard_v2 和 WAF_v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)。 如果使用 v1 SKU，我们建议[从 v1 迁移到 v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2)。 需要使用 v2 SKU 中提供的重写和其他[高级功能](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku)。

## <a name="alternate-solution-use-a-custom-domain-name"></a>备用解决方案：使用自定义域名

如果使用 v1 SKU，则无法重写 location 标头。 此功能仅适用于 v2 SKU。 若要解决重定向问题，请将应用程序网关接收的同一主机名传递给应用服务，而不要执行主机替代。

现在，应用服务会在指向应用程序网关而不是指向自身的同一原始主机标头中执行重定向（如果有）。

必须拥有一个自定义域并执行以下过程：

- 将该域注册到应用服务的自定义域列表。 必须在自定义域中创建一个指向应用服务 FQDN 的 CNAME。 有关详细信息，请参阅[将现有的自定义 DNS 名称映射到 Azure 应用服务](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)。

    ![应用服务自定义域列表](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 应用服务已准备好接受主机名 `www.contoso.com`。 更改 DNS 中的 CNAME 条目，将其指向应用程序网关的 FQDN，例如 `appgw.eastus.cloudapp.azure.com`。

- 执行 DNS 查询时，请确保域 `www.contoso.com` 解析为应用程序网关的 FQDN。

- 设置自定义探测以禁用“从后端 HTTP 设置中选取主机名”。 在 Azure 门户中，清除探测设置中的复选框。 在 PowerShell 中，请不要在 **Set-AzApplicationGatewayProbeConfig** 命令中使用 **-PickHostNameFromBackendHttpSettings** 开关。 在探测的 "主机名" 字段中，输入应用服务的 FQDN example.azurewebsites.net。 从应用程序网关发送的探测请求会在 host 标头中携带此 FQDN。

  > [!NOTE]
  > 对于下一步骤，请确保自定义探测未关联到后端 HTTP 设置。 此时，HTTP 设置中仍已启用“从后端地址中选取主机名”开关。

- 设置应用程序网关的 HTTP 设置以禁用“从后端地址中选取主机名”。 在 Azure 门户中清除相应的复选框。 在 PowerShell 中，请不要在 **Set-AzApplicationGatewayBackendHttpSettings** 命令中使用 **-PickHostNameFromBackendAddress** 开关。

- 将自定义探测重新关联到后端 HTTP 设置，并验证后端是否正常。

- 应用程序网关现在应将同一主机名 `www.contoso.com`转发到应用服务。 重定向在同一主机名中发生。 检查以下示例请求和响应标头。

若要使用 PowerShell 对现有设置执行上述步骤，请使用以下示例 PowerShell 脚本。 请注意，我们没有在探测和 HTTP 设置配置中使用 **-PickHostname** 开关。

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

如果上述步骤无法解决问题，请开具[支持票证](https://azure.microsoft.com/support/options/)。
