---
title: 排查 Azure 应用程序网关中的后端运行状况问题
description: 描述如何排查 Azure 应用程序网关的后端运行状况问题
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 71e1f8be2af5556d86996175e8a1ddbccc9c7de1
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001670"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>排查应用程序网关上的后端运行状况问题
==================================================

<a name="overview"></a>概述
--------

默认情况下，Azure 应用程序网关会探测后端服务器检查其运行状况状态，并检查它们是否已准备好为请求提供服务。 用户还可以创建自定义探测来提及主机名、要探测的路径，以及要接受为正常状态的状态代码。 在每种情况下，如果后端服务器未成功响应，应用程序网关会将服务器标记为不正常，并停止向服务器转发请求。 服务器成功启动响应后，应用程序网关会恢复请求的转发。

### <a name="how-to-check-backend-health"></a>如何检查后端运行状况

若要检查后端池的运行状况，可以使用 Azure 门户上的 "**后端运行状况**" 页。 或者，可以使用[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0)、 [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)或[REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth)。

这些方法中的任何一种检索到的状态可以是以下任一方法：

- 正常

- 不正常

- 未知

如果服务器的后端运行状况状态为 "正常"，则表示应用程序网关会将请求转发到该服务器。 但如果后端池中所有服务器的后端运行状况不正常或未知，则在尝试访问应用程序时可能会遇到问题。 本文介绍每个显示的错误的症状、原因和解决方法。

<a name="backend-health-status-unhealthy"></a>后端运行状态：不正常
-------------------------------

如果后端运行状况状态不正常，门户视图将类似于以下屏幕截图：

![应用程序网关后端运行状况-不正常](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

或者，如果你使用的是 Azure PowerShell、CLI 或 Azure REST API 查询，你将获得如下所示的响应：
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
在为后端池中的所有服务器接收到不正常的后端服务器状态之后，请求将不会转发到服务器，应用程序网关会将 "502 错误的网关" 错误返回给发出请求的客户端。 若要解决此问题，请查看 "**后端运行状况**" 选项卡上的**详细信息**列。

"**详细信息**" 列中显示的消息可提供有关该问题的详细信息，并根据这些信息开始对问题进行故障排除。

> [!NOTE]
> 默认探测请求的格式为 \<protocol @ no__t-1：//127.0.0.1： \<port @ no__t。 例如，在端口80上的 http 探测 http://127.0.0.1:80 。 仅200到399的 HTTP 状态代码被视为正常。 协议和目标端口从 HTTP 设置继承而来。 如果希望应用程序网关探测不同的协议、主机名或路径，并将不同的状态代码识别为健康，请配置自定义探测，并将其与 HTTP 设置关联。

<a name="error-messages"></a>错误消息
------------------------
#### <a name="backend-server-timeout"></a>后端服务器超时

**消息：** 后端响应应用程序网关 @ no__t-0 运行状况探测所花的时间超过了探测设置中的超时阈值。

原因：在应用程序网关将 HTTP （S）探测请求发送到后端服务器后，它将等待来自后端服务器的响应已配置时间段。 如果后端服务器未在配置的时间段（超时值）内响应，则会将其标记为不正常，直到它在配置的超时期限内再次开始响应。

**解决方法：** 检查后端服务器或应用程序在配置的超时期限内未响应的原因，同时检查应用程序的依赖关系。 例如，检查数据库是否存在可能触发响应延迟的任何问题。 如果你知道应用程序的行为，并且它应该只在超时值后响应，请增加自定义探测设置的超时值。 您必须具有自定义探测才能更改超时值。 有关如何配置自定义探测的信息，[请参阅文档页](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)。

若要增加超时值，请执行以下步骤：

1.  直接访问后端服务器，并在该页上检查服务器做出响应所花费的时间。 可以使用任何工具访问后端服务器，包括使用开发人员工具的浏览器。

1.  确定应用程序响应所需的时间后，选择 "**运行状况探测**" 选项卡，然后选择与 HTTP 设置关联的探测。

1.  输入大于应用程序响应时间的任何超时值（以秒为单位）。

1.  保存自定义探测设置，并检查后端运行状况是否显示为 "正常"。

#### <a name="dns-resolution-error"></a>DNS 解析错误

**消息：** 应用程序网关无法为此后端创建探测。 如果未正确输入后端的 FQDN，则往往会发生这种情况。 

原因：如果后端池的类型为 "IP 地址/FQDN" 或 "应用服务"，则应用程序网关解析为通过域名系统（DNS）（自定义或 Azure 默认值）输入的 FQDN 的 IP 地址，并尝试连接到 HTTP 设置中提到的 TCP 端口上的服务器。 但如果显示此消息，则表示应用程序网关无法成功解析输入的 FQDN 的 IP 地址。

**解决方法：**

1.  验证在后端池中输入的 FQDN 是否正确，以及它是否是公共域，然后尝试从本地计算机解析它。

1.  如果可以解析 IP 地址，则虚拟网络中的 DNS 配置可能存在问题。

1.  检查是否为虚拟网络配置了自定义 DNS 服务器。 如果是，请检查 DNS 服务器为什么无法解析为指定 FQDN 的 IP 地址。

1.  如果你使用的是 Azure 默认 DNS，请检查你的域名注册机构是否已完成正确的记录或 CNAME 记录映射。

1.  如果域是专用域或内部域，请尝试从同一虚拟网络中的 VM 进行解析。 如果可以解决此问题，请重新启动应用程序网关，然后重新检查。 若要重新启动应用程序网关，需要通过使用这些链接资源中所述的 PowerShell 命令来[停止](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0)和[启动](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0)。

#### <a name="tcp-connect-error"></a>TCP 连接错误

**消息：** 应用程序网关无法连接到后端。
请检查后端是否在用于探测的端口上做出响应。
同时，请检查是否有任何 NSG/UDR/防火墙正在阻止访问此后端的 IP 和端口

原因：在 DNS 解析阶段之后，应用程序网关会尝试连接到在 HTTP 设置中配置的 TCP 端口上的后端服务器。 如果应用程序网关无法在指定的端口上建立 TCP 会话，则会将此消息标记为不正常。

**解决方案：** 如果收到此错误，请执行以下步骤：

1.  使用浏览器或 PowerShell 检查是否可以使用 HTTP 设置中提到的端口连接到后端服务器。 例如，运行以下命令： `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  如果指定的端口不是所需的端口，请输入应用程序网关的正确端口号，以连接到后端服务器

1.  如果在本地计算机的端口上也无法进行连接，请进行以下检查：

    a.  检查后端服务器网络适配器和子网的网络安全组（NSG）设置，以及是否允许到已配置端口的入站连接。 如果没有，请创建新规则以允许连接。 若要了解如何创建 NSG 规则，[请参阅文档页](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)。

    b.  检查应用程序网关子网的 NSG 设置是否允许出站公共和专用流量，以便可以建立连接。 查看步骤3a 中提供的文档页，详细了解如何创建 NSG 规则。
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  检查应用程序网关和后端服务器子网的用户定义路由（UDR）设置以了解任何路由异常情况。 请确保 UDR 不会将流量定向到后端子网。 例如，通过 Azure ExpressRoute 和/或 VPN 检查到网络虚拟设备的路由或要播发到应用程序网关子网的默认路由。

    d.  若要检查网络适配器的有效路由和规则，可以使用以下 PowerShell 命令：
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  如果你找不到 NSG 或 UDR 的任何问题，请检查后端服务器，以了解与应用程序相关的问题，这些问题阻止客户端在配置的端口上建立 TCP 会话。 要检查的几个方面：

    a.  打开命令提示符（Win + R-\> cmd），输入 `netstat`，然后选择 Enter。

    b.  检查服务器是否正在侦听配置的端口。 例如：
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  如果未在配置的端口上侦听，请检查你的 web 服务器设置。 例如： IIS 中的网站绑定、NGINX 中的服务器块和 Apache 中的虚拟主机。

    d.  检查 OS 防火墙设置，以确保允许到端口的传入流量。

#### <a name="http-status-code-mismatch"></a>HTTP 状态代码不匹配

**消息：** 后端 HTTP 响应的状态代码与探测设置不匹配。 预期值: {HTTPStatusCode0}，收到的值: {HTTPStatusCode1}.

原因：建立 TCP 连接并完成 SSL 握手后（如果启用了 SSL），应用程序网关会将探测作为 HTTP GET 请求发送到后端服务器。 如前文所述，默认探测为 \<protocol @ no__t-1：//127.0.0.1： \<port @ no__t-3/，并将风行一时200到399中的响应状态代码视为正常。 如果服务器返回任何其他状态代码，则会将此消息标记为不正常。

**解决方案：** 根据后端服务器的响应代码，可以执行以下步骤。 下面列出了一些常见的状态代码：

| **错误** | **操作** |
| --- | --- |
| 探测状态代码不匹配：收到 401 | 检查后端服务器是否要求身份验证。 应用程序网关探测此时无法传递用于身份验证的凭据。 允许探测状态代码中的 \"HTTP 401 @ no__t 为匹配或探测到服务器不需要身份验证的路径。 | |
| 探测状态代码不匹配：收到 403 | 禁止访问。 检查后端服务器是否允许访问路径。 | |
| 探测状态代码不匹配：收到 404 | 找不到页面。 检查主机名路径是否可在后端服务器上访问。 将主机名或路径参数更改为可访问的值。 | |
| 探测状态代码不匹配：收到 405 | 应用程序网关的探测请求使用 HTTP GET 方法。 检查服务器是否允许此方法。 | |
| 探测状态代码不匹配：收到 500 | 内部服务器错误。 检查后端服务器的运行状况，以及服务是否正在运行。 | |
| 探测状态代码不匹配：收到 503 | 服务不可用。 检查后端服务器的运行状况，以及服务是否正在运行。 | |

或者，如果你认为响应是合法的，并且你希望应用程序网关接受其他状态代码，则可以创建自定义探测。 在后端网站需要身份验证的情况下，此方法非常有用。 由于探测请求不携带任何用户凭据，因此它们将失败，并且后端服务器将返回 HTTP 401 状态代码。

若要创建自定义探测，请遵循[以下步骤](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)。

#### <a name="http-response-body-mismatch"></a>HTTP 响应正文不匹配

**消息：** 后端 HTTP 响应的正文与探测设置不匹配。 收到的响应正文不包含 {string}。

原因：创建自定义探测时，可以通过匹配响应正文中的字符串，将后端服务器标记为正常。 例如，可将应用程序网关配置为接受“unauthorized”作为要匹配的字符串。 如果探测请求的后端服务器响应包含**未经授权**的字符串，则会将其标记为 "正常"。 否则，此消息将被标记为不正常。

**解决方案：** 若要解决此问题，请执行以下步骤：

1.  在本地或从探测路径上的客户端计算机访问后端服务器，并检查响应正文。

1.  验证应用程序网关自定义探测配置中的响应正文是否与配置的内容相匹配。

1.  如果二者不匹配，请更改探测配置，使其具有要接受的正确字符串值。

详细了解[应用程序网关探测匹配](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)。

#### <a name="backend-server-certificate-invalid-ca"></a>后端服务器证书无效的 CA

**消息：** 后端使用的服务器证书未由已知的证书颁发机构 (CA) 签名。 通过上传后端使用的服务器证书的根证书，将应用程序网关上的后端列入白名单。

原因：应用程序网关 v2 的端到端 SSL 要求验证后端服务器的证书，以便将服务器视为正常运行。
对于要信任的 SSL 证书，后端服务器的证书必须由应用程序网关的受信任存储区中包含的 CA 颁发。 如果证书不是由受信任的 CA 颁发（例如，如果使用的是自签名证书），则用户应将颁发者的证书上传到应用程序网关。

**解决方案：** 按照以下步骤将受信任的根证书导出并上传到应用程序网关。 （这些步骤适用于 Windows 客户端。）

1.  登录到托管应用程序的计算机。

1.  选择 Win + R 或右键单击 "**开始**" 按钮，然后选择 "**运行**"。

1.  输入 `certmgr.msc`，然后选择 Enter。 你还可以在 "**开始**" 菜单中搜索 "证书管理器"。

1.  找到该证书（通常在 `\Certificates - Current User\\Personal\\Certificates\`），然后将其打开。

1.  选择根证书，然后选择 "**查看证书**"。

1.  在 "证书属性" 中，选择 "**详细信息**" 选项卡。

1.  在 "**详细信息**" 选项卡上，选择 "**复制到文件**" 选项，并将文件保存在64编码的 x.509 （。CER）格式。

1.  在 Azure 门户中打开 "应用程序网关 HTTP**设置**" 页。

1. 打开 "HTTP 设置"，选择 "**添加证书**"，然后找到刚刚保存的证书文件。

1. 选择 "**保存**" 以保存 HTTP 设置。

或者，你可以从客户端计算机中导出根证书，方法是通过浏览器直接访问服务器（绕过应用程序网关），然后从浏览器导出根证书。

有关如何在应用程序网关上提取和上传受信任的根证书的详细信息，请参阅[导出受信任的根证书（适用于 V2 SKU）](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)。

#### <a name="trusted-root-certificate-mismatch"></a>受信任的根证书不匹配

**消息：** 后端使用的服务器证书的根证书与添加到应用程序网关的受信任根证书不匹配。 请确保添加正确的根证书，以将后端列入白名单

原因：应用程序网关 v2 的端到端 SSL 要求验证后端服务器的证书，以便将服务器视为正常运行。
对于要信任的 SSL 证书，必须由应用程序网关的受信任存储区中包含的 CA 颁发后端服务器证书。 如果证书不是由受信任的 CA 颁发（例如，使用的是自签名证书），则用户应将颁发者的证书上传到应用程序网关。

已上传到应用程序网关 HTTP 设置的证书必须与后端服务器证书的根证书匹配。

**解决方案：** 如果收到此错误消息，则表示已上传到应用程序网关的证书与上传到后端服务器的证书不匹配。

按照前面方法中的步骤1-11 将正确的受信任根证书上传到应用程序网关。

有关如何在应用程序网关上提取和上传受信任的根证书的详细信息，请参阅[导出受信任的根证书（适用于 V2 SKU）](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)。
> [!NOTE]
> 如果后端服务器没有交换证书的完整链（包括 Root > 中间（如果适用） > 叶，则会出现此错误。 若要验证，可以从任何客户端使用 OpenSSL 命令，并使用应用程序网关探测中配置的设置连接到后端服务器。

例如：
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
如果输出未显示要返回的证书的完整链，请再次导出证书，包括根证书。 在后端服务器上配置该证书。 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>后端证书无效的公用名（CN）

**消息：** 后端证书的公用名 (CN) 与探测的主机标头不匹配。

原因：应用程序网关检查后端 HTTP 设置中指定的主机名是否与后端服务器 SSL 证书所提供的 CN 的名称相匹配。 这是 Standard_v2 和 WAF_v2 SKU 的行为。 标准 SKU 和 WAF SKU 的服务器名称指示（SNI）在后端池地址中设置为 FQDN。

在 v2 SKU 中，如果存在默认探测（未配置和关联任何自定义探测），将从 HTTP 设置中提到的主机名设置 SNI。 或者，如果 HTTP 设置中提到了 "从后端地址中选取主机名"，而后端地址池包含有效的 FQDN，则会应用此设置。

如果存在与 HTTP 设置关联的自定义探测，将从自定义探测配置中提到的主机名设置 SNI。 或者，如果在自定义探测中选择了 "**从后端 HTTP 设置选取主机名**"，则将从 HTTP 设置中提到的主机名设置 SNI。

如果在 HTTP 设置中设置**后端地址的 "选择主机名**"，则后端地址池必须包含有效的 FQDN。

如果收到此错误消息，则后端证书的 CN 与在自定义探测或 HTTP 设置中配置的主机名不匹配（如果选择了 "**从后端 HTTP 设置选取主机名**"）。 如果使用的是默认探测，则主机名将设置为**127.0.0.1**。 如果这不是所需的值，则应创建自定义探测，并将其与 HTTP 设置关联。

**解决方案：**

若要解决该问题，请执行以下步骤。

对于 Windows：

1.  登录到托管应用程序的计算机。

1.  选择 Win + R 或右键单击 "**开始**" 按钮，然后选择 "**运行**"。

1.  输入**certmgr.msc** ，然后选择 Enter。 你还可以在 "**开始**" 菜单中搜索 "证书管理器"。

1.  找到证书（通常在 `\Certificates - Current User\\Personal\\Certificates`）中，然后打开证书。

1.  在 "**详细信息**" 选项卡上，检查证书**使用者**。

1.  验证证书的 CN，并在自定义探测的 "主机名" 字段中或在 HTTP 设置中输入相同的内容（如果选择了 "**从后端 HTTP 设置选取主机名**"。 如果该网站不是所需的主机名，则必须为该域获取证书，或者在自定义探测或 HTTP 设置配置中输入正确的主机名。

对于使用 OpenSSL 的 Linux：

1.  在 OpenSSL 中运行以下命令：
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  从显示的属性中，查找证书的 CN，并在 http 设置的 "主机名" 字段中输入相同的名称。 如果该网站不是所需的主机名，则必须为该域获取证书，或者在自定义探测或 HTTP 设置配置中输入正确的主机名。

#### <a name="backend-certificate-is-invalid"></a>后端证书无效

**消息：** 后端证书无效。 当前日期不在证书上的“生效”和“失效”日期范围内。\"\"\"\"

原因：每个证书都带有有效性范围，并且 HTTPS 连接不安全，除非服务器的 SSL 证书有效。 当前数据必须在 "**有效起始** **于**" 范围内。 如果不是，则会将该证书视为无效证书，并会产生一个安全问题，应用程序网关会将后端服务器标记为不正常。

**解决方案：** 如果 SSL 证书已过期，请向供应商续订证书，并将服务器设置更新为新证书。 如果它是自签名证书，则必须生成一个有效证书，并将根证书上传到应用程序网关 HTTP 设置。 为此，请执行以下步骤：

1.  在门户中打开应用程序网关 HTTP 设置。

1.  选择包含过期证书的设置，选择 "**添加证书**"，并打开新的证书文件。

1.  使用证书旁边的 "**删除**" 图标删除旧证书，然后选择 "**保存**"。

#### <a name="certificate-verification-failed"></a>证书验证失败

**消息：** 无法验证后端证书的有效性。 若要找出原因，请在 OpenSSL 诊断信息中检查与错误代码 {errorCode} 相关的消息

原因：当应用程序网关无法验证证书的有效性时，会发生此错误。

**解决方案：** 若要解决此问题，请验证是否正确创建了服务器上的证书。 例如，你可以使用[OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html)来验证证书及其属性，然后尝试将证书 Reuploading 为应用程序网关 HTTP 设置。

<a name="backend-health-status-unknown"></a>后端运行状况状态：未知
-------------------------------
如果后端运行状况显示为 "未知"，则门户视图将类似于以下屏幕截图：

![应用程序网关后端运行状况-未知](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

此行为可能由以下一个或多个原因导致：

1.  应用程序网关子网中的 NSG 阻止从 "Internet" 对端口65503-65534 （v1 SKU）或65200-65535 （v2 SKU）进行入站访问。
1.  应用程序网关子网中的 UDR 设置为默认路由（0.0.0.0/0），下一跃点未指定为 "Internet"。
1.  通过 BGP 对虚拟网络的 ExpressRoute/VPN 连接播发默认路由。
1.  在无法解析公共域名的虚拟网络上配置自定义 DNS 服务器。
1.  应用程序网关处于不正常状态。

**解决方案：**

1.  检查 NSG 是否正在阻止从**Internet**访问端口65503-65534 （v1 sku）或65200-65535 （v2 sku）：

    a.  在 "应用程序网关**概述**" 选项卡上，选择 "**虚拟网络/子网**" 链接。

    b.  在虚拟网络的 "**子网**" 选项卡上，选择已在其中部署应用程序网关的子网。

    c.  检查是否配置了任何 NSG。

    d.  如果配置了 NSG，请在 "**搜索**" 选项卡上或在 "**所有资源**" 下搜索该 NSG 资源。

    e.  在 "**入站规则**" 部分中，添加一个入站规则，以允许 v1 sku 的目标端口范围65503-65534 或 65200-65535 v2 sku，并将**源**设置为 "**任意**" 或 " **Internet**"。

    f.  选择 "**保存**"，并验证是否可以将后端查看为 "正常"。 也可以通过[PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)实现此目的。

1.  检查 UDR 是否具有默认路由（0.0.0.0/0），下一跃点未设置为**Internet**：
    
    a.  按照步骤1a 和1b 来确定你的子网。

    b.  检查是否已配置任何 UDR。 如果有，请在搜索栏或在 "**所有资源**" 下搜索资源。

    c.  检查是否有任何默认路由（0.0.0.0/0），下一跃点未设置为**Internet**。 如果设置为 "**虚拟设备**" 或 "**虚拟网络网关**"，则必须确保虚拟设备或本地设备可以正确地将数据包路由回 internet 目标，而无需修改数据包。

    d.  否则，将下一跃点更改为**Internet**，选择**保存**，并验证后端运行状况。

1.  通过 BGP 连接到虚拟网络的 ExpressRoute/VPN 连接播发的默认路由：

    a.  如果通过 BGP 连接到虚拟网络的 ExpressRoute/VPN，并且要播发默认路由，则必须确保将数据包路由回 internet 目标，而不进行修改。 你可以使用应用程序网关门户中的 "**连接故障排除**" 选项进行验证。

    b.  手动选择目标为任何可通过 internet 路由的 IP 地址（如1.1.1.1）。 将目标端口设置为任何内容，并验证连接性。

    c.  如果下一个跃点是虚拟网络网关，则可能有通过 ExpressRoute 或 VPN 播发的默认路由。

1.  如果在虚拟网络上配置了自定义 DNS 服务器，请验证服务器（或服务器）能否解析公共域。 在应用程序网关必须连接到外部域（如 OCSP 服务器）或检查证书的吊销状态的方案中，可能需要公共域名解析。

1.  若要验证应用程序网关是否正常运行，请在门户中选择 "**资源运行状况**" 选项，并验证状态是否为 "**正常**"。 如果状态为 "不**正常**" 或 "已**降级**"，请[与支持部门联系](https://azure.microsoft.com/support/options/)。

<a name="next-steps"></a>后续步骤
----------

了解有关[应用程序网关诊断和日志记录](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)的详细信息。
