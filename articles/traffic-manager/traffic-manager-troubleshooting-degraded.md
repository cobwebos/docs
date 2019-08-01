---
title: Azure 流量管理器上的降级状态故障排除
description: 如何在流量管理器显示为降级状态时对流量管理器配置文件进行故障排除。
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: rohink
ms.openlocfilehash: f8f457623dff7840ca839ef57580b744a4d916c7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565876"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Azure 流量管理器上的降级状态故障排除

本文介绍如何对显示降级状态的 Azure 流量管理器配置文件进行故障排除。 作为对 Azure 流量管理器降级状态进行故障排除的第一步是启用诊断日志记录。  有关详细信息, 请参阅[启用诊断日志](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs)。 在此方案中，假设已配置了一个指向某些 cloudapp.net 托管服务的流量管理器配置文件。 如果流量管理器的运行状况显示“已降级”状态，则一个或多个终结点的状态可能为“已降级”：

![已降级终结点状态](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

如果流量管理器的运行状况显示“非活动”状态，则这两个终结点可能**已禁用**：

![非活动流量管理器状态](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>了解流量管理器探测

* 仅当探测从探测路径收到 HTTP 200 响应时，流量管理器才将终结点视为“联机”。 如果应用程序返回任何其他 HTTP 响应代码, 则应将该响应代码添加到流量管理器配置文件的[预期状态代码范围](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring)。
* 如果在流量管理器配置文件的[预期状态代码范围](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring)内将此响应指定为有效的响应代码, 则会将30重定向响应视为失败。 流量管理器不探测重定向目标。
* 对于 HTTPS 探测器，证书错误会被忽略。
* 只要返回 200，就无需在意探测器路径的实际内容。 常用的技巧是探测某些静态内容的 URL，例如“/favicon.ico”。 即使应用程序处于正常状态，ASP 页等动态内容也不一定会返回 200。
* 最佳做法是将探测路径设置为具有足够逻辑的内容, 以确定站点的状态为 "已启动" 或 "已关闭"。 在上面的示例中，如果将路径设置为“/favicon.ico”，只会测试 w3wp.exe 是否响应。 这种探测可能不会指示 Web 应用程序是否正常。 更好的选择是，将路径设置为诸如“/Probe.aspx”之类的值，可通过逻辑确定站点运行状况。 例如，可以使用性能计数器来查看 CPU 利用率，或者测量失败请求的数目。 或者，可以尝试访问数据库资源或会话状态，确保 Web 应用程序正常工作。
* 如果配置文件中的所有终结点都已降级，流量管理器会将所有终结点视为处于正常状态，并将流量路由到所有终结点。 此行为可确保探测机制中的问题不会导致服务完全中断。

## <a name="troubleshooting"></a>疑难解答

若要排查探测失败，需要使用一个工具显示探测 URL 中返回的 HTTP 状态代码。 有许多工具可以显示原始 HTTP 响应。

* [Fiddler](https://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

也可以在 Internet Explorer 中使用“F12 调试工具”的“网络”标签页查看 HTTP 响应。

在此示例中, 我们想要查看探测器 URL 中的响应: http\/:/watestsdp2008r2.cloudapp.net:80/Probe。 以下 PowerShell 示例演示了该问题。

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

示例输出：

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

请注意我们收到了重定向响应。 如前所述，任何非 200 状态代码都被视为失败。 流量管理器将终结点状态更改为“脱机”。 若要解决该问题，请检查网站配置，确保可以从探测路径返回正确的状态代码。 将流量管理器探测重新配置为指向返回 200 的路径。

如果探测使用 HTTPS 协议，可能需要禁用证书检查，避免测试期间出现 SSL/TLS 错误。 以下 PowerShell 语句禁用当前 PowerShell 会话的证书验证：

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

[云服务](https://go.microsoft.com/fwlink/?LinkId=314074)

[Azure 应用服务](https://azure.microsoft.com/documentation/services/app-service/web/)

[流量管理器上的操作（REST API 参考）](https://go.microsoft.com/fwlink/?LinkId=313584)

[Azure 流量管理器 cmdlet][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
