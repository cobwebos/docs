---
title: 后端运行状况和诊断日志
titleSuffix: Azure Application Gateway
description: 了解如何启用和管理 Azure 应用程序网关的访问日志和性能日志
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/22/2019
ms.author: victorh
ms.openlocfilehash: 6829efa007e9e67866bdc0efbca4d095155c35e2
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889704"
---
# <a name="back-end-health-and-diagnostic-logs-for-application-gateway"></a>应用程序网关的后端运行状况和诊断日志

可以通过以下方式监视 Azure 应用程序网关资源：

* [后端运行状况](#back-end-health)：应用程序网关提供通过 Azure 门户和 PowerShell 监视后端池中的服务器运行状况的功能。 还可通过性能诊断日志找到后端池的运行状况。

* [日志](#diagnostic-logging)：通过日志记录，可出于监视目的从资源保存或使用性能、访问及其他数据。

* [指标](application-gateway-metrics.md)：应用程序网关有几个指标可以帮助你验证系统是否按预期运行。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>后端运行状况

应用程序网关提供通过门户、PowerShell 和命令行界面 (CLI) 监视后端池各成员运行状况的功能。 还可通过性能诊断日志找到后端池的聚合运行状况摘要。

后端运行状况报告反映了应用程序网关对后端实例进行的运行状况探测的输出。 如果探测成功且后端能够接收流量，则可认为后端运行状况正常， 否则不正常。

> [!IMPORTANT]
> 如果应用程序网关子网上存在网络安全组 (NSG)，请在应用程序网关子网上打开端口范围 65503-65534（适用于 v1 SKU）和 65200-65535（适用于 v2 SKU），以便接收入站流量。 此端口范围是进行 Azure 基础结构通信所必需的。 它们受 Azure 证书的保护（处于锁定状态）。 如果没有适当的证书，外部实体（包括这些网关的客户）将无法对这些终结点做出任何更改。


### <a name="view-back-end-health-through-the-portal"></a>通过门户查看后端运行状况

在门户中，后端运行状况是自动提供的。 在现有的应用程序网关中，选择“监视”   > “后端运行状况”  。

后端池中的每个成员都列在此页上（不管其是 NIC、IP 还是 FQDN）。 会显示后端池名称、端口、后端 HTTP 设置名称以及运行状况。 运行状况的有效值为“正常”、“不正常”、“未知”。   

> [!NOTE]
> 如果后端运行状况显示为“未知”，请确保未通过虚拟网络中的 NSG 规则、用户定义路由 (UDR) 或自定义 DNS 阻止对后端的访问。 

![后端运行状况][10]

### <a name="view-back-end-health-through-powershell"></a>通过 PowerShell 查看后端运行状况

以下 PowerShell 代码显示了如何通过 `Get-AzApplicationGatewayBackendHealth` cmdlet 查看后端运行状况：

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>通过 Azure CLI 查看后端运行状况

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>结果

以下代码片段演示了一个响应示例：

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>诊断日志

可在 Azure 中使用不同类型的日志来对应用程序网关进行管理和故障排除。 可通过门户访问其中部分日志。 可从 Azure Blob 存储提取所有日志并在 [Azure Monitor 日志](../azure-monitor/insights/azure-networking-analytics.md)、Excel 和 Power BI 等各种工具中查看。 可从以下列表了解有关不同类型日志的详细信息：

* **活动日志**：可使用 [Azure 活动日志](../monitoring-and-diagnostics/insights-debugging-with-events.md)（以前称为操作日志和审核日志）查看提交到 Azure 订阅的所有操作及其状态。 默认情况下会收集活动日志条目，可在 Azure 门户中查看这些条目。
* **访问日志**：可以使用此日志查看应用程序网关访问模式并分析重要信息。 这包括调用方的 IP、请求的 URL、响应延迟、返回代码，以及传入和传出的字节数。每60秒收集一次访问日志。 此日志包含每个应用程序网关实例的一条记录。 应用程序网关实例由 instanceId 属性标识。
* **性能日志**：可使用此日志查看应用程序网关实例的执行情况。 此日志会捕获每个实例的性能信息，包括服务的总请求数、吞吐量（以字节为单位）、失败请求计数、正常和不正常的后端实例计数。 每隔 60 秒会收集一次性能日志。 性能日志仅适用于 v1 SKU。 对于 v2 SKU，请对性能数据使用[指标](application-gateway-metrics.md)。
* **防火墙日志**：可使用此日志查看通过应用程序网关（配置有 Web 应用程序防火墙）的检测模式或阻止模式记录的请求。 每60秒收集一次防火墙日志。 

> [!NOTE]
> 日志仅适用于在 Azure 资源管理器部署模型中部署的 Azure 资源。 不能将日志用于经典部署模型中的资源。 若要深入了解这两个模型，请参阅[了解 Resource Manager 部署和经典部署](../azure-resource-manager/management/deployment-models.md)一文。

可通过三种方式存储日志：

* 存储帐户****：如果日志存储时间较长并且希望能根据需要随时查看，则最好使用存储帐户。
* **事件中心**：事件中心是一种很好的选择，用于与其他安全信息和事件管理（SIEM）工具集成，以获取对资源的警报。
* **Azure Monitor 日志**： Azure Monitor 日志最好用于实时监视应用程序或查看趋势。

### <a name="enable-logging-through-powershell"></a>通过 PowerShell 启用日志记录

每个 Resource Manager 资源都会自动启用活动日志记录。 必须启用访问和性能日志记录才能开始收集通过这些日志提供的数据。 若要启用日志记录，请执行以下步骤：

1. 记下存储日志数据的存储帐户资源 ID。 此值的形式如下：/subscriptions/\<subscriptionId\>/resourceGroups/\<资源组名称\>/providers/Microsoft.Storage/storageAccounts/\<存储帐户名称\>。 订阅中的所有存储帐户均可使用。 可使用 Azure 门户查找此信息。

    ![门户：存储帐户的资源 ID](./media/application-gateway-diagnostics/diagnostics1.png)

2. 记下为其启用日志记录的应用程序网关的资源 ID。 此值的形式如下：/subscriptions/\<subscriptionId\>/resourceGroups/\<资源组名称\>/providers/Microsoft.Network/applicationGateways/\<应用程序网关名称\>。 可使用门户查找此信息。

    ![门户： 应用程序网关的资源 ID](./media/application-gateway-diagnostics/diagnostics2.png)

3. 使用以下 PowerShell cmdlet 启用诊断日志记录：

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>活动日志不需要单独的存储帐户。 使用存储来记录访问和性能需支付服务费用。

### <a name="enable-logging-through-the-azure-portal"></a>通过 Azure 门户启用日志记录

1. 在 Azure 门户中找到资源，然后选择“诊断设置”****。

   对于应用程序网关，提供 3 种日志：

   * 访问日志
   * 性能日志
   * 防火墙日志

2. 若要开始收集数据，请选择“启用诊断”****。

   ![启用诊断][1]

3. “诊断设置”**** 页提供用于诊断日志的设置。 本示例使用 Log Analytics 存储日志。 也可使用事件中心和存储帐户保存诊断日志。

   ![启动配置过程][2]

5. 键入设置的名称，确认设置，然后选择“保存”。****

### <a name="activity-log"></a>活动日志

默认情况下，Azure 会生成活动日志。 日志可在 Azure 事件日志存储中保留 90 天。 通过阅读[查看事件和活动日志](../monitoring-and-diagnostics/insights-debugging-with-events.md)一文，了解有关这些日志的详细信息。

### <a name="access-log"></a>访问日志

只有在每个应用程序网关实例上启用了访问日志，才会生成此日志，如上述步骤所示。 数据存储在启用日志记录时指定的存储帐户中。 应用程序网关的每次访问均以 JSON 格式记录下来，如下面 v1 示例所示：

|值  |说明  |
|---------|---------|
|instanceId     | 为请求服务的应用程序网关实例。        |
|clientIP     | 请求的初始 IP。        |
|clientPort     | 请求的初始端口。       |
|httpMethod     | 请求使用的 HTTP 方法。       |
|requestUri     | 已收到请求的 URI。        |
|RequestQuery     | 服务器路由****：请求已发送至后端池实例。</br>X-AzureApplicationGateway-LOG-ID****：用于请求的相关 ID。 它可用于排查后端服务器上的流量问题。 </br>服务器状态****： 应用程序网关接收从后端的 HTTP 响应代码。       |
|UserAgent     | HTTP 请求标头中的用户代理。        |
|httpStatus     | 从应用程序网关返回到客户端的 HTTP 状态代码。       |
|httpVersion     | 请求的 HTTP 版本。        |
|receivedBytes     | 接收的数据包大小（以字节为单位）。        |
|sentBytes| 发送的数据包大小（以字节为单位）。|
|timeTaken| 处理请求并发送其响应所需的时长（以毫秒为单位）。 这是计算从应用程序网关接收到 HTTP 请求的第一个字节到响应发送操作完成这两个时间点之间的时间间隔。 请务必注意，所用时间字段通常包含请求和响应数据包通过网络传输的时间。 |
|sslEnabled| 与后端池的通信是否使用 TLS/SSL。 有效值为打开和关闭。|
|host| 向后端服务器发送请求时所用的主机名。 如果正在重写后端主机名，则此名称将反映该主机名。|
|originalHost| 应用程序网关从客户端接收请求时所用的主机名。|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
对于应用程序网关和 WAF v2，日志显示了一些详细信息：

|值  |说明  |
|---------|---------|
|instanceId     | 为请求服务的应用程序网关实例。        |
|clientIP     | 请求的初始 IP。        |
|clientPort     | 请求的初始端口。       |
|httpMethod     | 请求使用的 HTTP 方法。       |
|requestUri     | 已收到请求的 URI。        |
|UserAgent     | HTTP 请求标头中的用户代理。        |
|httpStatus     | 从应用程序网关返回到客户端的 HTTP 状态代码。       |
|httpVersion     | 请求的 HTTP 版本。        |
|receivedBytes     | 接收的数据包大小（以字节为单位）。        |
|sentBytes| 发送的数据包大小（以字节为单位）。|
|timeTaken| 处理请求并发送其响应所需的时长（以**秒**为单位）。 这是计算从应用程序网关接收到 HTTP 请求的第一个字节到响应发送操作完成这两个时间点之间的时间间隔。 请务必注意，所用时间字段通常包含请求和响应数据包通过网络传输的时间。 |
|sslEnabled| 与后端池的通信是否使用 TLS。 有效值为打开和关闭。|
|sslCipher| 用于 TLS 通信的密码套件（如果启用了 TLS）。|
|sslProtocol| 使用的 SSL/TLS 协议（如果已启用 TLS）。|
|serverRouted| 应用程序网关将请求路由到的后端服务器。|
|serverStatus| 后端服务器的 HTTP 状态代码。|
|serverResponseLatency| 后端服务器的响应延迟。|
|host| 请求的主机标头中列出的地址。|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>性能日志

只有在每个应用程序网关实例上启用了性能日志，才会生成此日志，如上述步骤所示。 数据存储在启用日志记录时指定的存储帐户中。 每隔 1 分钟生成性能日志数据。 性能日志数据仅适用于 v1 SKU。 对于 v2 SKU，请对性能数据使用[指标](application-gateway-metrics.md)。 将记录以下数据：


|值  |说明  |
|---------|---------|
|instanceId     |  正在为其生成性能数据的应用程序网关实例。 对于多实例应用程序网关，每个实例有一行性能数据。        |
|healthyHostCount     | 后端池中运行正常的主机数。        |
|unHealthyHostCount     | 后端池中运行不正常的主机数。        |
|requestCount     | 服务的请求数。        |
|latency | 从实例到请求服务后端的请求的平均延迟（以毫秒为单位）。 |
|failedRequestCount| 失败的请求数。|
|throughput| 自最后一个日志后的平均吞吐量，以每秒字节数为单位。|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> 延迟时间的计算是从接收到 HTTP 请求的第一个字节开始，到发出 HTTP 响应的最后一个字节为止。 它是应用程序网关处理时间加上后端的网络耗时，再加上后端处理请求所花费的时间之和。

### <a name="firewall-log"></a>防火墙日志

只有为每个应用程序网关启用了防火墙日志，才会生成此日志，如上述步骤所示。 此日志还需在应用程序网关上配置 Web 应用程序防火墙。 数据存储在启用日志记录时指定的存储帐户中。 将记录以下数据：


|值  |说明  |
|---------|---------|
|instanceId     | 正在为其生成防火墙数据的应用程序网关实例。 对于多实例应用程序网关，每个实例有一行性能数据。         |
|clientIp     |   请求的初始 IP。      |
|clientPort     |  请求的初始端口。       |
|requestUri     | 已收到请求的 URL。       |
|ruleSetType     | 规则集类型。 可用值为 OWASP。        |
|ruleSetVersion     | 使用的规则集版本。 可用值为 2.2.9 和 3.0。     |
|ruleId     | 触发事件的规则 ID。        |
|message     | 触发事件的用户友好信息。 详细信息部分提供了更多详细信息。        |
|action     |  针对请求执行的操作。 可用值为 Matched 和 Blocked。      |
|site     | 为其生成日志的站点。 目前，由于规则为全局性规则，所以仅列出了“全局”站点。|
|详细信息     | 触发事件的详细信息。        |
|details.message     | 规则说明。        |
|details.data     | 在请求中找到的匹配规则的特定数据。         |
|details.file     | 包含此规则的配置文件。        |
|details.line     | 配置文件中触发事件的行号。       |
|hostname   | 应用程序网关的主机名或 IP 地址。    |
|transactionId  | 给定事务的唯一 ID，它有助于对同一请求中发生的多个违反规则的情况进行分组。   |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
    "hostname": "40.90.218.100",
    "transactionId": "AYAcUqAcAcAcAcAcASAcAcAc"
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>查看和分析活动日志

可使用以下任一方法查看和分析活动日志数据：

* Azure 工具****：通过 Azure PowerShell、Azure CLI、Azure REST API 或 Azure 门户检索活动日志中的信息。 [使用 Resource Manager 活动操作](../azure-resource-manager/management/view-activity-logs.md)一文中详细介绍了每种方法的分步说明。
* **Power BI**：如果还没有[Power BI](https://powerbi.microsoft.com/pricing)帐户，可免费试用。 使用 [Power BI 模板应用](https://docs.microsoft.com/power-bi/service-template-apps-overview)，可以分析数据。

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>查看并分析访问、性能和防火墙日志

[Azure Monitor 日志](../azure-monitor/insights/azure-networking-analytics.md)可从 Blob 存储帐户收集计数器和事件日志文件。 它含有可视化和强大的搜索功能，可用于分析日志。

还可以连接到存储帐户并检索访问和性能日志的 JSON 日志条目。 下载 JSON 文件后，可以将它们转换为 CSV 并在 Excel、Power BI 或任何其他数据可视化工具中查看。

> [!TIP]
> 如果熟悉 Visual Studio 和更改 C# 中的常量和变量值的基本概念，则可以使用 GitHub 提供的[日志转换器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)。
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>通过 GoAccess 分析访问日志

我们发布了一个资源管理器模板，用于安装和运行应用程序网关访问日志的常用 [GoAccess](https://goaccess.io/) 日志分析器。 GoAccess 提供了宝贵的 HTTP 流量统计信息，例如唯一访问者、请求的文件、主机、操作系统、浏览器和 HTTP 状态代码等。 有关更多详细信息，请参阅 [GitHub 的资源管理器模板文件夹中的自述文件](https://aka.ms/appgwgoaccessreadme)。

## <a name="next-steps"></a>后续步骤

* 使用 [Azure Monitor 日志](../azure-monitor/insights/azure-networking-analytics.md)可视化计数器和事件日志。
* [通过 Power BI 的博客文章直观显示 Azure 活动日志](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/)。
* [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)（在 Power BI 和其他组件中查看和分析 Azure 活动日志）博客文章。

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
