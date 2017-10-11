---
title: "降级状态 Azure Traffic Manager 故障排除"
description: "如何解决 Traffic Manager 配置文件，如果它显示为降级状态。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
ms.assetid: 8af0433d-e61b-4761-adcc-7bc9b8142fc6
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: kumud
ms.openlocfilehash: b1d00fb84695d2289f37647f55a7c56cf28c8c96
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>状态 Azure 流量管理器上的降级故障排除

本文介绍如何进行故障排除显示降级的状态的 Azure 流量管理器配置文件。 对于此方案中，请考虑你已配置指向某些 cloudapp.net 托管服务的流量管理器配置文件。 如果运行状况的流量管理器显示**已降级**状态，则一个或多个终结点的状态可能**已降级**:

![降级的终结点状态](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

如果运行状况的流量管理器显示**非活动**状态，则这两个终结点可能**禁用**:

![非活动状态的流量管理器状态](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>了解流量管理器探测

* 流量管理器会将终结点视为处于联机状态，仅当探测器从探测器路径接收 HTTP 200 响应时。 任何其他非 200 响应是失败。
* 30x 重定向失败，即使重定向的 URL 返回 200。
* 对于 HTTPs 探测器，证书错误将被忽略。
* 探测路径的实际内容并不重要，只要返回 200。 探测之类的某些静态内容的 URL"/ favicon.ico"是一种常用技术。 动态内容，如 ASP 页中，可能不始终会返回 200，即使应用程序处于正常状态。
* 最佳做法是将探测器路径设置为拥有足够的逻辑来确定站点向上或向下的内容的。 在上一示例中，通过将路径设置为"/ favicon.ico"，您只是测试该 w3wp.exe 是否响应。 此探测不可能表示 web 应用程序处于正常状态。 更好的选择将设置成一个路径，如"/ Probe.aspx"已从逻辑上确定站点的运行状况。 例如，你无法使用为 CPU 使用率的性能计数器或度量值的失败请求数。 或者，你可以尝试访问数据库资源或会话状态，以确保的 web 应用程序正常工作。
* 如果已降级的配置文件中的所有终结点，然后流量管理器会将所有正常运行的终结点和路由流量的所有终结点。 此行为确保探测机制的问题不会导致你的服务完全中断。

## <a name="troubleshooting"></a>疑难解答

若要解决探测失败，你需要一种工具，显示的 HTTP 状态代码返回从探测 URL。 有许多工具可显示原始 HTTP 响应。

* [Fiddler](http://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

此外，可以在 Internet Explorer 中使用 F12 调试工具的网络选项卡以查看 HTTP 响应。

此示例中我们想要查看来自我们探测 URL 的响应： http://watestsdp2008r2.cloudapp.net:80/探测。 下面的 PowerShell 示例演示了该问题。

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

输出示例：

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

请注意，我们收到的重定向响应。 如前面所述，任何 StatusCode 以外 200 被认为是失败。 流量管理器的终结点状态更改为脱机。 若要解决此问题，请检查网站配置以确保可以从探测路径返回正确 StatusCode。 重新配置为指向返回 200 的路径的流量管理器探测。

如果你的探测器使用 HTTPS 协议，你可能需要禁用检查以避免在测试期间的 SSL/TLS 错误的证书。 以下 PowerShell 语句禁用证书验证当前 PowerShell 会话：

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>后续步骤

[关于流量管理器流量路由方法](traffic-manager-routing-methods.md)

[什么是流量管理器](traffic-manager-overview.md)

[云服务](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)

[Traffic Manager 的操作 （REST API 参考）](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure 流量管理器 Cmdlet][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
