---
title: 使用应用服务-重定向到应用服务的 URL 的 Azure 应用程序网关故障排除
description: 本文提供有关如何与 Azure 应用服务一起使用 Azure 应用程序网关时，重定向问题进行故障排除信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 359d75f10f95b0e41ccd9a869d49247355f0d5d0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123175"
---
# <a name="troubleshoot-application-gateway-with-app-service--redirection-to-app-services-url"></a>使用应用服务排查应用程序网关问题 – 重定向到应用服务的 URL

 了解如何诊断和解决应用程序网关获取其中公开应用程序服务的 URL 重定向问题。

## <a name="overview"></a>概述

当配置面向公众的应用程序网关后端池中的应用服务，而如果必须在应用程序代码中配置的重定向，你可能会看到，当访问应用程序网关时，你将重定向浏览器可以直接向应用程序服务 URL。

由于是以下主要原因可能会发生此问题：

- 必须在应用服务上配置的重定向。 可以重定向为向请求添加尾部反斜杠一样简单。
- 具有 Azure AD 身份验证，这将导致重定向。
- 已启用应用程序网关的 HTTP 设置中的"选择主机名从后端地址"交换机。
- 你没有你的应用服务中注册的自定义域。

## <a name="sample-configuration"></a>示例配置

- HTTP 侦听器：基本或多站点
- 后端地址池：应用服务
- HTTP 设置："选择主机名后端地址从"已启用
- 探测："选择主机名从 HTTP 设置"已启用

## <a name="cause"></a>原因

应用服务仅使用配置的主机名在自定义域设置中，默认情况下访问，它是"example.azurewebsites.net"并且如果你想要访问应用服务应用程序网关使用未注册或使用应用服务中的主机名应用程序网关的 FQDN，您必须重写中对应用服务的主机名的原始请求的主机名。

若要使用应用程序网关实现此目的，我们使用的交换机"选取主机名从后端地址"HTTP 设置中和探测来工作，我们在探测配置中使用"选择主机名从后端 HTTP 设置"。

![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

由于此操作，请在应用服务执行的重定向，它使用主机名"example.azurewebsites.net"中的 Location 标头，而不是原始主机名除非已另外进行配置。 您可以检查下面的示例请求和响应头。
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://example.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.azurewebsites.net

X-Powered-By: ASP.NET
```
在上述示例中，可以注意到响应标头的 301 重定向状态代码和 location 标头具有应用服务的主机名而非原始主机名"www.contoso.com"。

## <a name="solution"></a>解决方案

如果这是不可能，我们必须将应用程序网关接收的相同主机标头传递到应用服务还执行主机替代，而是可以通过不将重定向对应用程序端，但是，解决此问题。

一旦我们这样做，应用服务将进行重定向 （如果有） 上指向应用程序网关的同一原始主机标头，而不自己。

若要实现此目的，必须拥有自定义域，并请遵循下面所述的过程。

- 注册到自定义域列表的应用服务域。 为此，必须具有一个 CNAME，指向应用服务的 FQDN 的自定义域中。 有关详细信息，请参阅[现有的自定义 DNS 名称映射到 Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)。

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- 完成后，你的应用服务已准备好接受的主机名"www.contoso.com"。 现在，更改你在 DNS 中为返回指向应用程序网关的 FQDN 的 CNAME 条目。 例如，"appgw.eastus.cloudapp.azure.com"。

- 请确保你的域"www.contoso.com"解析为应用程序网关的 FQDN 中，当执行 DNS 查询时。

- 设置你的自定义探测以禁用"选取主机名从后端 HTTP 设置"。 这可以在门户中完成通过取消选中中探测设置的复选框，并在 PowerShell 中不使用-PickHostNameFromBackendHttpSettings 切换集 AzApplicationGatewayProbeConfig 命令中。 在探测主机名字段中，输入从应用程序网关发送的探测请求将主机标头中携带此应用服务 FQDN"example.azurewebsites.net"。

  > [!NOTE]
  > 同时执行下一步，请确保的自定义探测都不关联到后端 HTTP 设置因为在 HTTP 设置仍具有在这种情况下启用的"选择主机名从后端地址"交换机。

- 设置应用程序网关的 HTTP 设置，以禁用"选取主机名从后端地址"。 这可以在门户中完成，通过取消选中该复选框，并在 PowerShell 中不使用-PickHostNameFromBackendAddress 切换集 AzApplicationGatewayBackendHttpSettings 命令中。

- 将返回到后端 HTTP 设置的自定义探测和验证后端运行状况，如果运行状况良好。

- 完成此操作后，应用程序网关现在应将相同的主机名"www.contoso.com"转发到应用服务，重定向会基于相同的主机名。 您可以检查下面的示例请求和响应头。
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
