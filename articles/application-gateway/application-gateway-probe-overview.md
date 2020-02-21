---
title: Azure 应用程序网关的运行状况监视概述
description: Azure 应用程序网关监视其后端池中所有资源的运行状况，并自动从池中删除任何被视为不正常的资源。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2020
ms.author: victorh
ms.openlocfilehash: a4427c05d16a42879d37fdbd2e8b8be9095fcc9b
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505890"
---
# <a name="application-gateway-health-monitoring-overview"></a>应用程序网关运行状况监视概述

默认情况下，Azure 应用程序网关会监视其后端池中所有资源的运行状况，并自动从池中删除任何被视为不正常的资源。 应用程序网关持续监视不正常的实例，一旦这些实例恢复可用状态并能响应运行状况探测，应用程序网关就会将它们添加回到正常的后端池中。 应用程序网关发送的运行状况探测所针对的端口与后端 HTTP 设置中定义的端口相同。 此配置可确保探测所测试的端口即是客户用来连接到后端的端口。 

源 IP 地址应用程序网关用于运行状况探测取决于后端池：
 
- 如果后端池是公共终结点，则源地址是应用程序网关前端公共 IP 地址。
- 如果后端池是专用终结点，则源 IP 地址来自应用程序网关子网专用 IP 地址空间。


![应用程序网关探测示例][1]

除了使用默认的运行状况探测监视以外，还可以根据应用程序的要求自定义运行状况探测。 本文介绍默认的和自定义的运行状况探测。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>默认的运行状况探测

如果未设置任何自定义探测配置，应用程序网关会自动配置默认运行状况探测。 监视行为是向针对后端池配置的 IP 地址发出 HTTP 请求。 对于默认探测，如果后端 http 设置是针对 HTTPS 配置的，则探测也会使用 HTTPS 测试后端的运行状况。

例如：将应用程序网关配置为使用 A、B 和 C 后端服务器来接收端口 80 上的 HTTP 网络流量。 默认运行状况监视每隔 30 秒对三台服务器进行测试，以获取正常的 HTTP 响应。 正常的 HTTP 响应具有 200 到 399 的[状态代码](https://msdn.microsoft.com/library/aa287675.aspx)。

如果服务器 A 的默认探测检查失败，应用程序网关会从后端池删除该服务器，并且网络流量不再流向此服务器。 默认探测仍继续每隔 30 秒检查服务器 A。 当服务器 A 成功响应默认运行状况探测发出的请求时，会将其作为正常添加回后端池，并且流量开始再次流向服务器。

### <a name="probe-matching"></a>探测匹配

默认情况下，状态代码介于200和399之间的 HTTP （S）响应被视为正常。 自定义运行状况探测额外支持两个匹配条件。 可以使用匹配条件来随意修改做出正常响应的的默认解释。

下面是匹配条件： 

- **HTTP 响应状态代码匹配** - 接受用户指定的 http 响应代码或响应代码范围的探测匹配条件。 支持逗号分隔的单个响应状态代码，或一系列状态代码。
- **HTTP 响应正文匹配** - 查找 HTTP 响应正文并匹配用户指定字符串的探测匹配条件。 匹配仅在响应正文中查找用户指定字符串的状态，并且不是完整的正则表达式匹配。

可以使用 `New-AzApplicationGatewayProbeHealthResponseMatch` cmdlet 指定匹配条件。

例如：

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
指定匹配条件后，可在 PowerShell 中使用 `-Match` 参数将其附加到探测配置。

### <a name="default-health-probe-settings"></a>默认的运行状况探测设置

| 探测属性 | 值 | 说明 |
| --- | --- | --- |
| 探测 URL |http://127.0.0.1:\<port\>/ |URL 路径 |
| 时间间隔 |30 |发送下一个运行状况探测前需要等待的时间（以秒为单位）。|
| 超时 |30 |将探测标记为不正常前，应用程序网关等待探测响应的时间（以秒为单位）。 如果探测返回为正常，则相应的后端立即被标记为正常。|
| 不正常阈值 |3 |控制在正常运行状况探测失败时要发送的探测数。 这些附加的运行状况探测会迅速发送，以确定后端的运行状况，并且不等待探测间隔。 连续探测失败计数达到不正常阈值后，将后端服务器标记为故障。 |

> [!NOTE]
> 该端口与后端 HTTP 设置的端口相同。

默认探测仅查找 http：\//127.0.0.1：\<端口\>，以确定运行状况状态。 如果需要配置运行状况探测以使其转到自定义 URL 或修改任何其他设置，必须使用自定义探测。

### <a name="probe-intervals"></a>探测间隔

应用程序网关的所有实例探测相互独立的后端。 相同的探测配置适用于每个应用程序网关实例。 例如，如果探测配置为每 30 秒发送运行状况探测，并且应用程序网关包含两个实例，则这两个实例均每隔 30 秒发送运行状况探测。

此外，如果存在多个侦听器，则每个侦听器探测相互独立的后端。 例如，如果有两个侦听器指向两个不同端口上的同一后端池（由两个后端 http 设置配置），则每个侦听器独立探测同一后端。 在这种情况下，两个侦听器的每个应用程序网关实例都有两个探测。 如果此方案中的应用程序网关包含两个实例，则在每个配置的探测间隔中，可看到四个探测。

## <a name="custom-health-probe"></a>自定义的运行状况探测

自定义探测可让你更精细地控制运行状况监视。 使用自定义探测时，可以配置探测间隔、要测试的 URL 和路径，以及在将后端池实例标记为不正常之前可接受的失败响应次数。

### <a name="custom-health-probe-settings"></a>自定义的运行状况探测设置

下表提供自定义运行状况探测的属性的定义。

| 探测属性 | 说明 |
| --- | --- |
| 名称 |探测的名称。 此名称用于在后端 HTTP 设置中引用探测。 |
| 协议 |用于发送探测的协议。 探测使用后端 HTTP 设置中定义的协议 |
| 主机 |用于发送探测的主机名。 仅在应用程序网关上配置了多站点的情况下适用，否则使用“127.0.0.1”。 此值与 VM 主机名不同。 |
| 路径 |探测的相对路径。 有效路径以“/”开头。 |
| 时间间隔 |探测间隔（秒）。 此值是每两次连续探测之间的时间间隔。 |
| 超时 |探测超时（秒）。 如果在此超时期间内未收到有效响应，则将探测标记为失败。  |
| 不正常阈值 |探测重试计数。 连续探测失败计数达到不正常阈值后，将后端服务器标记为故障。 |

> [!IMPORTANT]
> 如果在应用程序网关中设置了单站点，则默认情况下，除非已在自定义探测中进行配置，否则应将主机名指定为“127.0.0.1”。
> 例如，自定义探测发送到 \<协议\>://\<主机\>:\<端口\>\<路径\>。 所使用的端口与后端 HTTP 设置中定义的端口相同。

## <a name="nsg-considerations"></a>NSG 注意事项

对于应用程序网关 v1 SKU，你必须允许 TCP 端口65503-65534 上的传入 Internet 流量，并允许 v2 SKU 的 TCP 端口65200-65535 （目标子网为**Any** and Source as **GatewayManager** service 标记）。 此端口范围是进行 Azure 基础结构通信所必需的。

此外，不能阻止出站 Internet 连接，并且必须允许来自**AzureLoadBalancer**标记的入站流量。

有关详细信息，请参阅[应用程序网关配置概述](configuration-overview.md#network-security-groups-on-the-application-gateway-subnet)。

## <a name="next-steps"></a>后续步骤
了解应用程序网关的运行状况监视后，可以在 Azure 门户中配置[自定义运行状况探测](application-gateway-create-probe-portal.md)，或使用 PowerShell 和 Azure 资源管理器部署模型配置[自定义运行状况探测](application-gateway-create-probe-ps.md)。

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
