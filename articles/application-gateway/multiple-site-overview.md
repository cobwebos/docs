---
title: 在 Azure 应用程序网关上托管多个站点
description: 本文概述了 Azure 应用程序网关的多站点支持。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: b3e6bc6d2dd5568dcc11a37c6ab44bd3b4089c66
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87067936"
---
# <a name="application-gateway-multiple-site-hosting"></a>应用程序网关的多站点托管功能

使用多站点托管功能可以在应用程序网关的同一端口上配置多个 Web 应用程序。 它允许您为部署配置更有效的拓扑，方法是将最多100个网站添加到一个应用程序网关。 每个网站都可以定向到自己的后端池。 例如，三个域、contoso.com、fabrikam.com 和 adatum.com，指向应用程序网关的 IP 地址。 可以创建三个多站点侦听器，并为每个侦听器配置相应的端口和协议设置。 

你还可以在多站点侦听器中定义通配符主机名，每个侦听器最多可定义5个主机名。 若要了解详细信息，请参阅[侦听器中的通配符主机名](#wildcard-host-names-in-listener-preview)。

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="多站点应用程序网关":::

> [!IMPORTANT]
> 规则按其在 v1 SKU 的门户中列出的顺序进行处理。 对于 v2 SKU，精确匹配具有更高的优先级。 我们强烈建议先配置多站点侦听器，然后再配置基本侦听器。  这可以确保将流量路由到适当的后端。 如果基本侦听器先列出并且与传入的请求匹配，则该侦听器将处理该请求。

对 `http://contoso.com` 的请求路由到 ContosoServerPool，对 `http://fabrikam.com` 的请求路由到 FabrikamServerPool。

同样，可以在同一应用程序网关部署上托管同一个父域的多个子域。 例如，你可以 `http://blog.contoso.com` `http://app.contoso.com` 在单个应用程序网关部署上承载和。

## <a name="wildcard-host-names-in-listener-preview"></a>侦听程序（预览版）中的通配符主机名

应用程序网关允许使用多站点 HTTP （S）侦听器进行基于主机的路由。 现在，你可以在主机名中使用通配符（如星号（*）和问号（？））和每个多站点 HTTP （S）侦听器最多5个主机名。 例如，`*.contoso.com`。

使用主机名中的通配符，可以在单个侦听器中匹配多个主机名。 例如， `*.contoso.com` 可以匹配，也可以与等 `ecom.contoso.com` `b2b.contoso.com` `customer1.b2b.contoso.com` 。 使用主机名数组，可以为侦听器配置多个主机名，以将请求路由到后端池。 例如，侦听器可以包含 `contoso.com, fabrikam.com` 接受两个主机名的请求的侦听器。

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="通配符侦听器":::

>[!NOTE]
> 此功能处于预览阶段，仅适用于应用程序网关 Standard_v2 和 WAF_v2 SKU。 若要了解有关预览的详细信息，请参阅[此处的使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在[Azure 门户](create-multiple-sites-portal.md)中，可以在单独的文本框中进行定义，如下面的屏幕截图中所示。

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-example.png" alt-text="通配符侦听器示例配置":::

>[!NOTE]
>如果你要创建新的多站点侦听器，或将多个主机名添加到 Azure 门户中的现有多站点侦听器，则默认情况下会将其添加到 `HostNames` 侦听器配置的参数，该参数会将更多的功能添加到 `HostName` 配置中的现有参数。

在[Azure PowerShell](tutorial-multiple-sites-powershell.md)中，必须使用 `-HostNames` 而不是 `-HostName` 。 使用主机名时，可以将最多5个主机名提及为逗号分隔值并使用通配符。 例如： `-HostNames "*.contoso.com,*.fabrikam.com"`

在[Azure CLI](tutorial-multiple-sites-cli.md)中，必须使用 `--host-names` 而不是 `--host-name` 。 使用主机名时，最多可以提及5个主机名作为逗号分隔值并使用通配符。 例如： `--host-names "*.contoso.com,*.fabrikam.com"`

### <a name="allowed-characters-in-the-host-names-field"></a>"主机名" 字段中允许的字符：

* `(A-Z,a-z,0-9)`-字母数字字符
* `-`-连字符或减号
* `.`-period 作为分隔符
*   `*`-可以匹配允许范围内的多个字符
*   `?`-可以匹配允许范围中的单个字符

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>在侦听器中使用通配符和多个主机名的条件：

*   单个侦听器中最多只能提及5个主机名
*   `*`在域样式名或主机名的组件中，星号只能出现一次。 例如，component1 *. component2*. component3。 `(*.contoso-*.com)` 有效。
*   一个主机名最多只能有两个星号 `*` 。 例如， `*.contoso.*` 是有效的，并且 `*.contoso.*.*.com` 无效。
*   在一个主机名中，最多只能有4个通配符。 例如， `????.contoso.com` `w??.contoso*.edu.*` 是有效的，但 `????.contoso.*` 无效。
*   `*` `?` 在主机名（或或）的组件中一起使用星号和 `*?` 问号 `?*` `**` 是无效的。 例如， `*?.contoso.com` 和 `**.contoso.com` 无效。

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>在侦听器中使用通配符或多个主机名的注意事项和限制：

*   [Ssl 终止和端到端 ssl](ssl-overview.md)要求你将协议配置为 HTTPS，并上传要在侦听器配置中使用的证书。 如果它是多站点侦听器，则还可以输入主机名，通常是 SSL 证书的 CN。 如果在侦听器中指定多个主机名或使用通配符，则必须考虑以下事项：
    *   如果它是通配符，如 *. contoso.com，则必须使用类似于 *. contoso.com 的 CN 上传通配符证书
    *   如果同一侦听器中提到了多个主机名，则必须上传一个 SAN 证书（使用者备用名称），并与所述的主机名匹配。
*   不能使用正则表达式来提及主机名。 您只能使用星号（*）和问号（？）等通配符来形成主机名称模式。
*   对于后端运行状况检查，无法为每个 HTTP 设置关联多个[自定义探测](application-gateway-probe-overview.md)。 相反，你可以在后端探测其中一个网站，或使用 "127.0.0.1" 探测后端服务器的 localhost。 但是，当您在侦听器中使用通配符或多个主机名时，将根据规则类型（基本或基于路径），将针对所有指定域模式的请求路由到后端池。
*   属性 "hostname" 采用一个字符串作为输入，其中你可以只提及一个非通配符域名，而 "主机名" 采用字符串数组作为输入，其中最多可以提及5个通配符域名。 但不能同时使用这两个属性。
*   不能使用目标侦听器创建使用通配符或多个主机名的[重定向](redirect-overview.md)规则。

请参阅[使用 Azure 门户创建多站点](create-multiple-sites-portal.md)或使用[Azure PowerShell](tutorial-multiple-sites-powershell.md)或[使用 Azure CLI](tutorial-multiple-sites-cli.md)来了解如何在多站点侦听器中配置通配符主机名。

## <a name="host-headers-and-server-name-indication-sni"></a>主机标头和服务器名称指示 (SNI)

可以通过三种常用机制在同一基础结构上启用多站点托管功能。

1. 托管多个 Web 应用程序时，每个唯一的 IP 地址可以托管一个应用程序。
2. 使用主机名在同一 IP 地址上托管多个 Web 应用程序。
3. 使用不同的端口在同一 IP 地址上托管多个 Web 应用程序。

当前，应用程序网关支持单个公共 IP 地址，该地址用于侦听流量。 目前不支持多个应用程序，每个应用程序都有自己的 IP 地址。 

应用程序网关支持每个应用程序在不同的端口上侦听，但此方案要求应用程序接受非标准端口上的流量。 这通常不是所需的配置。

应用程序网关需要使用 HTTP 1.1 主机标头才能在相同的公共 IP 地址和端口上托管多个网站。 在应用程序网关上托管的站点也可以支持带有服务器名称指示（SNI） TLS 扩展的 TLS 卸载。 这种情况意味着，客户端浏览器和后端 Web 场必须支持 RFC 6066 中定义的 HTTP/1.1 和 TLS 扩展。

## <a name="next-steps"></a>后续步骤

了解多个站点托管后，请参阅[使用 Azure 门户创建多站点](create-multiple-sites-portal.md)或使用[Azure PowerShell](tutorial-multiple-sites-powershell.md)或[使用 Azure CLI](tutorial-multiple-sites-cli.md)获取有关创建应用程序网关以托管多个网站的分步指南。

可以查看 [Resource Manager template using multiple site hosting](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting)（使用多站点托管的 Resource Manager 模板），了解如何进行基于模板的端到端部署。