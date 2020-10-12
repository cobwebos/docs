---
title: 在 Azure 应用程序网关上托管多个站点
description: 本文概述了 Azure 应用程序网关的多站点支持。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: surmb
ms.topic: conceptual
ms.openlocfilehash: 53f6f37454de886934a483b40daad24204958baf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87474319"
---
# <a name="application-gateway-multiple-site-hosting"></a>应用程序网关的多站点托管功能

使用多站点托管功能可以在应用程序网关的同一端口上配置多个 Web 应用程序。 它可以将多达 100 多个网站添加到一个应用程序网关中，从而为部署配置更有效的拓扑。 每个网站都可以定向到自己的后端池。 例如，contoso.com、fabrikam.com 和 adatum.com，这三个域指向应用程序网关的 IP 地址。 你将创建三个多站点侦听器，并为每个侦听器配置相应的端口和协议设置。 

此外，你还可以在多站点侦听器中定义通配符主机名，每个侦听器最多可以定义 5 个主机名。 若要了解详细信息，请参阅[侦听器中的通配符主机名](#wildcard-host-names-in-listener-preview)。

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="多站点应用程序网关":::

> [!IMPORTANT]
> 对于 v1 SKU，规则按照它们在门户中列出的顺序进行处理。 对于 v2 SKU，完全匹配具有更高的优先级。 我们强烈建议先配置多站点侦听器，然后再配置基本侦听器。  这可以确保将流量路由到适当的后端。 如果基本侦听器先列出并且与传入请求匹配，则该侦听器将处理该请求。

对 `http://contoso.com` 的请求路由到 ContosoServerPool，对 `http://fabrikam.com` 的请求路由到 FabrikamServerPool。

同样，可以在同一应用程序网关部署中托管同一父域的多个子域。 例如，可以在单个应用程序网关部署中托管 `http://blog.contoso.com` 和 `http://app.contoso.com`。

## <a name="wildcard-host-names-in-listener-preview"></a>侦听器中的通配符主机名（预览）

应用程序网关允许使用多站点 HTTP(S) 侦听器进行基于主机的路由。 现在，你可以在主机名中使用通配符（如星号 (*) 和问号 (?)），并且可以在每个多站点 HTTP(S) 侦听器上使用最多 5 个主机名。 例如，`*.contoso.com`。

在主机名中使用通配符，你可以在单个侦听器中匹配多个主机名。 例如，`*.contoso.com` 可以与 `ecom.contoso.com`、`b2b.contoso.com` 和 `customer1.b2b.contoso.com` 等匹配。 使用主机名数组，你可以为侦听器配置多个主机名，以将请求路由到后端池。 例如，侦听器可以包含接受两个主机名的请求的 `contoso.com, fabrikam.com`。

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="多站点应用程序网关":::

>[!NOTE]
> 此功能处于预览状态，仅适用于应用程序网关 Standard_v2 和 WAF_v2 SKU。 若要了解关于预览的详细信息，请参阅[此处的使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

>[!NOTE]
>此功能目前仅通过 [Azure PowerShell](tutorial-multiple-sites-powershell.md) 和 [Azure CLI](tutorial-multiple-sites-cli.md) 提供。 门户支持即将推出。
> 请注意，由于门户支持并非完全可用，因此，如果仅使用 HostNames 参数，侦听器将在门户中显示为基本侦听器，并且侦听器列表视图的“主机名”列将不会显示已配置的主机名。 对于通配符侦听器的任何更改，请确保使用 Azure PowerShell 或 CLI，直到门户中支持这些更改为止。

在 [Azure PowerShell](tutorial-multiple-sites-powershell.md) 中，必须使用 `-HostNames` 而不是 `-HostName`。 使用 HostNames 时，你可以通过逗号分隔值的形式提及最多 5 个主机名并使用通配符。 例如： `-HostNames "*.contoso.com,*.fabrikam.com"`

在 [Azure CLI](tutorial-multiple-sites-cli.md) 中，必须使用 `--host-names` 而不是 `--host-name`。 使用 host-names 时，你可以通过逗号分隔值的形式提及最多 5 个主机名并使用通配符。 例如： `--host-names "*.contoso.com,*.fabrikam.com"`

### <a name="allowed-characters-in-the-host-names-field"></a>“主机名”字段中允许使用的字符：

* `(A-Z,a-z,0-9)` - 字母数字字符
* `-` - 连字符或减号
* `.` - 句点作为分隔符
*   `*` - 可以在允许的范围内匹配多个字符
*   `?` - 可以在允许的范围内匹配单个字符

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>在侦听器中使用通配符和多个主机名的条件：

*   单个侦听器中最多只能提及 5 个主机名
*   星号 `*` 只能在域样式名或主机名的组件中提到一次。 例如，component1 *.component2*.component3。 `(*.contoso-*.com)` 是有效的。
*   在一个主机名中，最多只能有两个星号 `*`。 例如，`*.contoso.*` 有效，但 `*.contoso.*.*.com` 无效。
*   主机名中最多只能有 4 个通配符。 例如，`????.contoso.com`、`w??.contoso*.edu.*` 有效，但是 `????.contoso.*` 无效。
*   在主机名的组件中一同使用星号 `*` 和问号 `?`（`*?``?*` 或 `**`）是无效的。 例如，`*?.contoso.com` 和 `**.contoso.com` 均无效。

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>在侦听器中使用通配符或多个主机名的注意事项和限制：

*   [SSL 终止和端到端 SSL](ssl-overview.md) 要求你将协议配置为 HTTPS，并上传要在侦听器配置中使用的证书。 如果它是多站点侦听器，你还可以输入主机名（通常为 SSL 证书的 CN）。 在侦听器中指定多个主机名或使用通配符时，必须考虑以下事项：
    *   如果它是通配符主机名，如 *.contoso.com，则必须上传带有 CN（如 *.contoso.com）的通配符证书
    *   如果同一侦听器中提及了多个主机名，则必须上传一个 CN 与所提及的主机名匹配的 SAN 证书（使用者替代名称）。
*   不能使用正则表达式来提及主机名。 只能使用星号 (*) 和问号 (?) 等通配符来构成主机名模式。
*   对于后端运行状况检查，无法对每个 HTTP 设置关联多项[自定义探测](application-gateway-probe-overview.md)。 相反，你可以在后端探测其中一个网站，或使用“127.0.0.1”探测后端服务器的 localhost。 但是，在侦听器中使用通配符或多个主机名时，系统将根据规则类型（基本或基于路径），将针对所有指定域模式的请求路由到后端池。
*   属性“hostname”采用一个字符串作为输入，你只能在其中提及一个非通配符域名，而“hostnames”采用字符串数组作为输入，你最多可以在其中提及 5 个通配符域名。 但不能同时使用这两个属性。
*   不能通过使用通配符或多个主机名的目标侦听器创建[重定向](redirect-overview.md)规则。

有关如何在多站点侦听器中配置通配符主机名的分步指南，请参阅[使用 Azure PowerShell](tutorial-multiple-sites-powershell.md) 或[使用 Azure CLI](tutorial-multiple-sites-cli.md) 创建多站点。

## <a name="host-headers-and-server-name-indication-sni"></a>主机标头和服务器名称指示 (SNI)

可以通过三种常用机制在同一基础结构上启用多站点托管功能。

1. 托管多个 Web 应用程序时，每个唯一的 IP 地址可以托管一个应用程序。
2. 使用主机名在同一 IP 地址上托管多个 Web 应用程序。
3. 使用不同的端口在同一 IP 地址上托管多个 Web 应用程序。

当前，应用程序网关支持一个公共 IP 地址，用于侦听流量。 因此，目前不支持多个有自己的 IP 地址的应用程序。 

应用程序网关支持多个应用程序，每个应用程序侦听不同的端口，但此方案要求应用程序接受非标准端口上的流量。 这通常不是所需的配置。

应用程序网关需要使用 HTTP 1.1 主机标头才能在相同的公共 IP 地址和端口上托管多个网站。 在应用程序网关上托管的站点也可以通过服务器名称指示 (SNI) TLS 扩展来支持 TLS 卸载。 这种情况意味着，客户端浏览器和后端 Web 场必须支持 RFC 6066 中定义的 HTTP/1.1 和 TLS 扩展。

## <a name="next-steps"></a>后续步骤

了解如何在应用程序网关中配置多站点托管
* [使用 Azure 门户](create-multiple-sites-portal.md)
* [使用 Azure PowerShell](tutorial-multiple-sites-powershell.md) 
* [使用 Azure CLI](tutorial-multiple-sites-cli.md)

可以访问 [Resource Manager template using multiple site hosting](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting)（使用多站点托管的 Resource Manager 模板），了解如何进行基于模板的端到端部署。
