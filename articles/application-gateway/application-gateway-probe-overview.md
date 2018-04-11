---
title: Azure 应用程序网关的运行状况监视概述
description: 了解 Azure 应用程序网关中的监视功能
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/30/2018
ms.author: victorh
ms.openlocfilehash: 2f62f01c1178f9529eb46051f088affccc5279a7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="application-gateway-health-monitoring-overview"></a>应用程序网关运行状况监视概述

默认情况下，Azure 应用程序网关会监视其后端池中所有资源的运行状况，并自动从池中删除任何被视为不正常的资源。 应用程序网关持续监视不正常的实例，一旦这些实例恢复可用状态并能响应运行状况探测，应用程序网关就会将它们添加回到正常的后端池中。 应用程序网关发送的运行状况探测所针对的端口与后端 HTTP 设置中定义的端口相同。 此配置可确保探测所测试的端口即是客户用来连接到后端的端口。

![应用程序网关探测示例][1]

除了使用默认的运行状况探测监视以外，还可以根据应用程序的要求自定义运行状况探测。 本文介绍默认的和自定义的运行状况探测。

> [!NOTE]
> 如果应用程序网关子网上存在 NSG，则应在入站流量的应用程序网关子网上打开端口范围 65503-65534。 这些端口是确保后端运行状况 API 正常工作所必需的。

## <a name="default-health-probe"></a>默认的运行状况探测

如果未设置任何自定义探测配置，应用程序网关会自动配置默认运行状况探测。 监视行为是向针对后端池配置的 IP 地址发出 HTTP 请求。 对于默认探测，如果后端 http 设置是针对 HTTPS 配置的，则探测也会使用 HTTPS 测试后端的运行状况。

例如：将应用程序网关配置为使用 A、B 和 C 后端服务器来接收端口 80 上的 HTTP 网络流量。 默认运行状况监视每隔 30 秒对三台服务器进行测试，以获取正常的 HTTP 响应。 正常的 HTTP 响应具有 200 到 399 的[状态代码](https://msdn.microsoft.com/library/aa287675.aspx)。

如果服务器 A 的默认探测检查失败，应用程序网关会从后端池删除该服务器，并且网络流量不再流向此服务器。 默认探测仍继续每隔 30 秒检查服务器 A。 当服务器 A 成功响应默认运行状况探测发出的请求时，将变为正常状态并重新加回到后端池，而流量也开始再次流向该服务器。

### <a name="probe-matching"></a>探测匹配

默认情况下，包含状态代码 200 的 HTTP(S) 响应被视为正常。 自定义运行状况探测额外支持两个匹配条件。 可根据需要使用条件匹配来修改构成正常响应的因素的默认解释。

下面是匹配条件： 

- **HTTP 响应状态代码匹配** - 接受用户指定的 http 响应代码或响应代码范围的探测匹配条件。 支持逗号分隔的单个响应状态代码，或一系列状态代码。
- **HTTP 响应正文匹配** - 查找 HTTP 响应正文并匹配用户指定字符串的探测匹配条件。 请注意，该匹配操作只会在响应正文中确定是否存在用户指定的字符串，而不执行完整正则表达式匹配。

可以使用 `New-AzureRmApplicationGatewayProbeHealthResponseMatch` cmdlet 指定匹配条件。

例如：

```
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzureRmApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
指定匹配条件后，可在 PowerShell 中使用 `-Match` 参数将其附加到探测配置。

### <a name="default-health-probe-settings"></a>默认的运行状况探测设置

| 探测属性 | 值 | 说明 |
| --- | --- | --- |
| 探测 URL |http://127.0.0.1:\<port\>/ |URL 路径 |
| 时间间隔 |30 |探测间隔（秒） |
| 超时 |30 |探测超时（秒） |
| 不正常阈值 |3 |探测重试计数。 连续探测失败计数达到不正常阈值后，将后端服务器标记为故障。 |

> [!NOTE]
> 该端口与后端 HTTP 设置的端口相同。

默认探测只查看 http://127.0.0.1:\<port\> 来判断运行状况。 如果需要配置运行状况探测以使其转到自定义 URL 或修改任何其他设置，必须使用以下步骤中所述的自定义探测：

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

## <a name="next-steps"></a>后续步骤
了解应用程序网关的运行状况监视后，可以在 Azure 门户中配置[自定义运行状况探测](application-gateway-create-probe-portal.md)，或使用 PowerShell 和 Azure 资源管理器部署模型配置[自定义运行状况探测](application-gateway-create-probe-ps.md)。

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
