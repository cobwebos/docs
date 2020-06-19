---
title: 在 Azure 应用程序网关中排查后端运行状况问题
description: 介绍如何排查 Azure 应用程序网关的后端运行状况问题
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: c51d79d55f77468030100fa10973e2a31148ceae
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648443"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>排查应用程序网关中的后端运行状况问题
==================================================

<a name="overview"></a>概述
--------

默认情况下，Azure 应用程序网关会探测后端服务器以检查其运行状况并检查其是否可以处理请求。 用户还可以创建自定义探测以涉及主机名、要探测的路径以及要作为“运行正常”接受的状态代码。 在每种情况下，如果后端服务器未成功响应，则应用程序网关会将服务器标记为“运行不正常”，并停止向服务器转发请求。 服务器开始成功响应后，应用程序网关将继续转发请求。

### <a name="how-to-check-backend-health"></a>如何检查后端运行状况

若要检查后端池的运行状况，可以使用 Azure 门户中的“后端运行状况”页。 也可以使用 [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0)、[CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health) 或 [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth)。

按照以上任一方法可以检索出以下状态之一：

- 正常

- 不正常

- 未知

如果服务器的后端运行状况为“运行正常”，则表示应用程序网关将向该服务器转发请求。 但如果后端池中所有服务器的后端运行状况不正常或未知，则尝试访问应用程序时可能会遇到问题。 本文介绍所显示的每个错误的症状、原因和解决方案。

<a name="backend-health-status-unhealthy"></a>后端运行状况：不正常
-------------------------------

如果后端运行状况不正常，门户视图将类似于以下屏幕截图：

![应用程序网关后端运行状况 - 不正常](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

或者，如果你使用的是 Azure PowerShell、CLI 或 Azure REST API 查询，你将收到类似于如下所示的响应：
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
收到后端池中所有服务器的不正常后端服务器状态之后，请求将不会转发到服务器，并且应用程序网关会向请求客户端返回“502 错误网关”错误。 若要排查此问题，请勾选“后端运行状况”选项卡上的“详细信息”列 。

“详细信息”列中所示的消息提供了关于问题的更详细见解，你可以基于这些见解排查问题。

> [!NOTE]
> 默认探测请求以 \<protocol\>://127.0.0.1:\<port\>/ 格式发送。 例如， http://127.0.0.1:80 表示端口 80 上的 http 探测。 仅 200 到 399 的 HTTP 状态代码被视为正常。 协议和目标端口继承自 HTTP 设置。 如果希望应用程序网关探测其他协议、主机名或路径，并将其他状态代码识别为“正常”，请配置自定义探测并将其与 HTTP 设置相关联。

<a name="error-messages"></a>Error messages
------------------------
#### <a name="backend-server-timeout"></a>后端服务器超时

**消息：** 后端响应应用程序网关运行状况探测所需时间超过探侧设置中的超时阈值。

原因：应用程序网关向后端服务器发送 HTTP(S) 探测请求后，它会在配置的时间段内等待来自后端服务器的响应。 如果后端服务器未在配置的时间段（超时值）内响应，则将其标记为不正常，直到它在配置的超时时间段内开始响应为止。

**解决方法：** 检查后端服务器或应用程序未在配置的超时时间段内响应的原因，并检查应用程序依赖项。 例如，检查数据库是否存在可能导致响应延迟的任何问题。 如果知道应用程序的行为，并且应用程序应仅在达到超时值后作出响应，则增加自定义探测设置中的超时值。 必须具有自定义探测才能更改超时值。 有关如何配置自定义探测的信息，请[参阅文档页](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)。

若要增大时间值，请执行以下步骤：

1.  直接访问后端服务器，并在该页面上查看服务器响应所需时间。 可以使用任何工具访问后端服务器，包括使用开发人员工具的浏览器。

1.  确定应用程序响应所需时间后，选择“运行状况探测”选项卡，然后选择与 HTTP 设置关联的探测。

1.  输入超过应用程序响应时间的任何超时值（以秒为单位）。

1.  保存自定义探测设置，并检查后端运行状况现在是否显示为“正常”。

#### <a name="dns-resolution-error"></a>DNS 解析错误

**消息：** 应用程序网关无法为此后端创建探测。 未正确输入后端的 FQDN 时，通常会发生这种情况。 

原因：如果后端池的类型为 IP 地址/FQDN 或应用服务，则应用程序网关将解析为通过域名系统 (DNS) 输入的 FQDN 的 IP 地址（自定义或 Azure 默认），并尝试连接到 HTTP 设置中所述的 TCP 端口上的服务器。 但如果显示此消息，则表示应用程序网关无法成功解析输入的 FQDN 的 IP 地址。

**解决方法：**

1.  验证后端池中输入的 FQDN 是否正确及其是否是公共域，然后尝试从本地计算机解析它。

1.  如果可以解析 IP 地址，则虚拟网络中的 DNS 配置可能存在问题。

1.  检查虚拟网络是否配置了自定义 DNS 服务器。 如果是，请检查 DNS 服务器为何无法解析为指定 FQDN 的 IP 地址。

1.  如果使用的是 Azure 默认 DNS，请检查域名注册器是否完成了正确的 A 记录或 CNAME 记录映射。

1.  如果域是专用域或内部域，请尝试从同一虚拟网络中的 VM 解析它。 如果可以解析它，请重启应用程序网关并重新检查。 若要重启应用程序网关，需要使用这些链接资源中所述的 PowerShell 命令进行[停止](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0)和[启动](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0)操作。

#### <a name="tcp-connect-error"></a>TCP 连接错误

**消息：** 应用程序网关无法连接到后端。
请检查后端是否在用于探测的端口上作出响应。
此外，还需要检查是否存在任何 NSG/UDR/防火墙阻止访问此后端的 IP 和端口

原因：DNS 解析阶段结束后，应用程序网关会尝试连接到 HTTP 设置中配置的 TCP 端口上的后端服务器。 如果应用程序网关无法在指定端口上建立 TCP 会话，则在此消息中探测将被标记为“不正常”。

**解决方案：** 如果接收到此错误，请执行以下步骤：

1.  检查是否可以使用浏览器或 PowerShell 连接到 HTTP 设置中所述端口上的后端服务器。 例如，运行以下命令：`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  如果所述端口不是所需端口，请输入正确端口号，以便应用程序网关连接到后端服务器

1.  如果也无法从本地计算机上的端口进行连接，请执行以下操作：

    a.  检查后端服务器的网络适配器和子网的网络安全组 (NSG) 设置，并检查是否允许到配置的端口的入站连接。 如果不可以，请新建规则以允许连接。 若要了解如何创建 NSG 规则，请[参阅文档页](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)。

    b.  检查应用程序网关子网的 NSG 设置是否允许公共和专用出站流量，以便建立连接。 查看步骤 3a 中提供的文档页，详细了解如何创建 NSG 规则。
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  检查应用程序网关的用户定义的路由 (UDR) 设置，并检查后端服务器的子网是否存在任何路由异常。 确保 UDR 不会将流量导出后端子网。 例如，检查到网络虚拟设备的路由或通过 Azure ExpressRoute 和/或 VPN 播发到应用程序网关子网的默认路由。

    d.  若要检查网络适配器的有效路由和规则，可以使用以下 PowerShell 命令：
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  如果没有发现 NSG 或 UDR 存在任何问题，请检查后端服务器是否存在与应用程序相关的问题，这些问题会阻止客户端在配置的端口上建立 TCP 会话。 需要检查的若干事项：

    a.  打开命令提示符 (Win+R -\> cmd)，输入 `netstat`，然后选择 Enter。

    b.  检查服务器是否正在侦听配置的端口。 例如：
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  如果未侦听配置的端口，请检查 Web 服务器设置。 例如：IIS 中的站点绑定、NGINX 中的服务器块和 Apache 中的虚拟主机。

    d.  检查 OS 防火墙设置，确保可以将流量传入端口。

#### <a name="http-status-code-mismatch"></a>HTTP 状态代码不匹配

**消息：** 后端的 HTTP 响应状态代码与探测设置不匹配。 预期：{HTTPStatusCode0} 收到：{HTTPStatusCode1}。

原因：建立 TCP 连接并完成 TLS 握手（如果启用了 TLS）后，应用程序网关会将探测作为 HTTP GET 请求发送到后端服务器。 如前文所述，默认探测将发送到 \<protocol\>://127.0.0.1:\<port\>/，并且它会将 200 至 399 范围内的响应状态代码视为“正常”。 如果服务器返回其他任何状态代码，则在此消息中其将被标记为“不正常”。

**解决方案：** 根据后端服务器的响应代码，可以执行以下步骤。 以下列出了一些常见状态代码：

| **错误** | **操作** |
| --- | --- |
| 探测状态代码不匹配：收到 401 | 检查后端服务器是否要求进行身份验证。 应用程序网关探测此时无法传递用于身份验证的凭据。 允许探测状态代码中的 HTTP 401 匹配或探测到服务器不需要身份验证的路径。 | |
| 探测状态代码不匹配：收到 403 | 访问被禁止。 检查后端服务器中是否允许访问路径。 | |
| 探测状态代码不匹配：收到 404 | 找不到页面。 检查是否可以在后端服务器上访问主机名路径。 将主机名或路径参数更改为可访问的值。 | |
| 探测状态代码不匹配：收到 405 | 应用程序网关的探测请求可以使用 HTTP GET 方法。 检查服务器是否允许此方法。 | |
| 探测状态代码不匹配：收到 500 | 内部服务器错误。 检查后端服务器的运行状况，以及服务是否正在运行。 | |
| 探测状态代码不匹配：收到 503 | 服务不可用。 检查后端服务器的运行状况，以及服务是否正在运行。 | |

或者，如果认为响应合理，并且希望应用程序网关将其他状态代码视为“正常”，则可以创建自定义探测。 在后端网站需要进行身份验证的情况下，此方法很有用。 探测请求将因不包含任何用户凭据而失败，并且后端服务器将返回 HTTP 401 状态代码。

若要创建自定义探测，请遵循[这些步骤](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)。

#### <a name="http-response-body-mismatch"></a>HTTP 响应正文不匹配

**消息：** 后端的 HTTP 响应正文与探测设置不匹配。 收到的响应正文不包含 {string}。

原因：创建自定义探测时，可以选择通过匹配响应正文中的字符串将后端服务器标记为“正常”。 例如，可以将应用程序网关配置为接受“unauthorized”作为要匹配的字符串。 如果探测请求的后端服务器响应包含字符串 unauthorized，则它将被标记为“正常”。 否则，此消息中它会被标记为“不正常”。

**解决方案：** 若要解决此问题，请执行以下步骤：

1.  在本地或从探测路径上的客户端计算机访问后端服务器，然后检查响应正文。

1.  验证应用程序网关自定义探测配置中的响应正文是否与配置内容相匹配。

1.  如果两者不匹配，请更改探测配置，以便接收正确的字符串值。

详细了解[应用程序网关探测匹配](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)。

>[!NOTE]
> 对于与 TLS 相关的所有错误消息，若要详细了解 SNI 行为以及 v1 与 v2 SKU 之间的差异，请参阅 [TLS 概述](ssl-overview.md)页。


#### <a name="backend-server-certificate-invalid-ca"></a>后端服务器证书无效的 CA

**消息：** 后端使用的服务器证书不是由已知证书颁发机构 (CA) 签署的。 通过上传后端所使用的服务器证书的根证书，将应用程序网关上的后端列入白名单。

原因：带应用程序网关 v2 的端到端 SSL 必须验证后端服务器的证书，才能断定服务器是否正常运行。
为了使 TLS/SSL 证书受信任，后端服务器的证书必须由应用程序网关的受信任存储区中包含的 CA 颁发。 如果证书不是由受信任的 CA 颁发的（例如，使用自签名证书），则用户应将证书颁发者的证书上传到应用程序网关。

**解决方案：** 按照以下步骤将受信任的根证书导出并上传到应用程序网关。 （这些步骤适用于 Windows 客户端。）

1.  登录到托管应用程序的计算机。

1.  选择 Win+R 或右键单击“开始”按钮，然后选择“运行” 。

1.  输入 `certmgr.msc`，然后选择 Enter。 还可以在“开始”菜单上搜索证书管理器。

1.  找到证书（通常位于 `\Certificates - Current User\\Personal\\Certificates\`），然后将其打开。

1.  选择根证书，然后选择“查看证书”。

1.  在“证书”属性中，选择“详细信息”选项卡。

1.  在“详细信息”选项卡上，选择“复制到文件”选项，然后将文件另存为 Base-64 编码 X.509 (.CER) 格式 。

1.  在 Azure 门户中打开应用程序网关 HTTP“设置”页。

1. 打开 HTTP 设置，选择“添加证书”，然后找到刚保存的证书文件。

1. 选择“保存”，保存这些 HTTP 设置。

或者，可以从客户端计算机导出根证书，方法是通过浏览器直接访问服务器（绕过应用程序网关），然后从浏览器导出根证书。

若要详细了解如何在应用程序网关中提取和上传受信任的根证书，请参阅[导出受信任的根证书（适用于 v2 SKU）](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)。

#### <a name="trusted-root-certificate-mismatch"></a>受信任的根证书不匹配

**消息：** 后端使用的服务器证书的根证书与添加到应用程序网关的受信任根证书不匹配。 确保添加正确的根证书以将后端列入白名单

原因：带应用程序网关 v2 的端到端 SSL 必须验证后端服务器的证书，才能断定服务器是否正常运行。
为了使 TLS/SSL 证书受信任，后端服务器证书必须由应用程序网关的受信任存储区中包含的 CA 颁发。 如果证书不是由受信任的 CA 颁发的（例如，使用自签名证书），则用户应将证书颁发者的证书上传到应用程序网关。

已上传到应用程序网关 HTTP 设置的证书必须与后端服务器证书的根证书匹配。

**解决方案：** 如果收到此错误消息，则表示已上传到应用程序网关的证书与上传到后端服务器的证书不匹配。

按照上述方法中的步骤 1-11 将正确的受信任根证书上传到应用程序网关。

若要详细了解如何在应用程序网关中提取和上传受信任的根证书，请参阅[导出受信任的根证书（适用于 v2 SKU）](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)。
> [!NOTE]
> 如果后端服务器未交换证书的完整链，包括 TLS 握手期间的根 > 中间环节（如果适用）> 叶，则同样会发生此错误。 若要进行验证，可以通过使用应用程序网关探测中的已配置设置，使用任意客户端的 OpenSSL 命令并连接到后端服务器。

例如：
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
如果输出未显示要返回的证书的完整链，请再次导出具有完整链的证书，包括根证书。 在后端服务器上配置该证书。 

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

#### <a name="backend-certificate-invalid-common-name-cn"></a>后端证书的无效的公用名称 (CN)

**消息：** 后端证书的公用名称 (CN) 与探测的主机标头不匹配。

原因：应用程序网关检查后端 HTTP 设置中指定的主机名是否与后端服务器的 TLS/SSL 证书提供的 CN 相匹配。 这是 Standard_v2 和 WAF_v2 SKU (V2) 行为。 Standard 和 WAF SKU 的 (v1) 服务器名称指示 (SNI) 在后端池地址中设置为 FQDN。 若要详细了解 SNI 行为以及 v1 和 v2 SKU 之间的差异，请参阅[应用程序网关中的 TLS 终止以及端到端 TLS 的概述](ssl-overview.md)。

在 v2 SKU 中，如果存在默认探测（未配置和关联自定义探测），则将根据 HTTP 设置中所述的主机名设置 SNI。 或者，如果 HTTP 设置中涉及“从后端地址选择主机名”，且后端地址池包含有效的 FQDN，则将应用此设置。

如果存在与 HTTP 设置关联的自定义探测，则将根据自定义探测配置中涉及的主机名设置 SNI。 或者，如果在自定义探测中选择了“从后端 HTTP 设置选择主机名”，则将根据 HTTP 设置中涉及的主机名设置 SNI。

如果在 HTTP 设置中设置了“从后端地址选择主机名”，则后端地址池必须包含有效的 FQDN。

如果收到此错误消息，则表示后端证书的 CN 与自定义探测或 HTTP 设置中配置的主机名（如果选择了“从后端 HTTP 设置中选取主机名”）不匹配。 如果使用的是默认探测，则主机名应设置为 127.0.0.1。 如果不是所需的值，则应创建自定义探测，并将其与 HTTP 设置关联。

**解决方案：**

若要解决该问题，请执行以下步骤。

对于 Windows：

1.  登录到托管应用程序的计算机。

1.  选择 Win+R 或右键单击“开始”按钮，然后选择“运行” 。

1.  输入 certmgr.msc，然后选择 Enter。 还可以在“开始”菜单上搜索证书管理器。

1.  找到证书（通常位于 `\Certificates - Current User\\Personal\\Certificates`），然后打开证书。

1.  在“详细信息”选项卡上，勾选证书“主题” 。

1.  验证详细信息中的证书的 CN，然后在自定义探测的主机名字段或 HTTP 设置中将其输入（如果选择了“从后端 HTTP 设置中选择主机名”）。 如果这不是网站所需的主机名，则必须获取该域的证书，或者在自定义探测或 HTTP 设置配置中输入正确的主机名。

对于使用 OpenSSL 的 Linux：

1.  在 OpenSSL 中运行以下命令：
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  在显示的属性中，找到证书的 CN，然后在 http 设置的主机名字段中将其输入。 如果这不是网站所需的主机名，则必须获取该域的证书，或者在自定义探测或 HTTP 设置配置中输入正确的主机名。

#### <a name="backend-certificate-is-invalid"></a>后端证书无效

**消息：** 后端证书无效。 当前日期不在证书上的“生效”和“失效”日期范围内。

原因：每个证书都有有效范围，并且仅在服务器的 TLS/SSL 证书有效时 HTTPS 连接才安全。 当前数据必须在“有效期开始时间”和“有效期结束时间”范围内 。 如果不在范围内，则证书将被视为无效，这将导致一个安全问题，即应用程序网关将后端服务器标记为不正常。

**解决方案：** 如果 TLS/SSL 证书已过期，请通过供应商续订该证书，然后使用新证书更新服务器设置。 如果是自签名证书，则必须生成有效证书，并将根证书上传到应用程序网关 HTTP 设置。 为此，请执行以下步骤：

1.  在门户中打开应用程序网关 HTTP 设置。

1.  选择包含过期证书的设置，选择“添加证书”，然后打开新证书文件。

1.  使用证书旁边的“删除”图标删除旧证书，然后选择“保存” 。

#### <a name="certificate-verification-failed"></a>证书验证失败

**消息：** 无法验证后端证书的有效性。 若要找出原因，请检查 OpenSSL 诊断中与错误代码 {errorCode} 相关的消息

原因：应用程序网关无法验证证书的有效性时，会发生此错误。

**解决方案：** 若要解决该问题，请验证服务器上的证书是否创建正确。 例如，可以使用 [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) 验证证书及其属性，然后尝试将证书重新上传到应用程序网关的 HTTP 设置。

<a name="backend-health-status-unknown"></a>后端运行状况：未知
-------------------------------
如果后端运行状况显示为未知，门户视图将类似于以下屏幕截图：

![应用程序网关后端运行状况 - 未知](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

下列一个或多个原因可能导致产生此行为：

1.  应用程序网关子网上的 NSG 正在阻止从“Internet”对端口 65503-65534 (v1 SKU) 或 65200-65535 (v2 SKU) 的入站访问。
1.  应用程序网关子网上的 UDR 设置为默认路由 (0.0.0.0/0)，且下一跃点未指定为“Internet”。
1.  默认路由由通过 BGP 连接到虚拟网络的 ExpressRoute/VPN 播发。
1.  自定义 DNS 服务器在无法解析公共域名的虚拟网络上配置。
1.  应用程序网关处于不正常状态。

**解决方案：**

1.  检查 NSG 是否正在阻止从 Internet 对端口 65503-65534 (v1 SKU) 或 65200-65535 (v2 SKU) 的访问：

    a.  在应用程序网关的“概述”选项卡上，选择“虚拟网络/子网”链接 。

    b.  在虚拟网络的“子网”选项卡上，选择已部署应用程序网关的子网。

    c.  检查是否配置了任何 NSG。

    d.  如果配置了 NSG，请在“搜索”选项卡上或在“所有资源”下搜索该 NSG 资源 。

    e.  在“入站规则”部分中，添加一个入站规则，以允许 v1 SKU 的目标端口范围为 65503-65534，v2 SKU 的目标端口范围为 65200-65535，同时将“源”设置为“任意”或 Internet   。

    f.  选择“保存”并验证是否可以将后端视为正常。 此外，可以通过 [PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) 实现这一点。

1.  检查 UDR 是否具有默认路由 (0.0.0.0/0)，且下一跃点是否未设置为 Internet：
    
    a.  按照步骤 1a 和 1b 进行操作以确定子网。

    b.  检查是否配置了任何 UDR。 如果已配置，请在搜索栏中或“所有资源”下搜索资源。

    c.  检查是否存在任何默认路由 (0.0.0.0/0)，且下一跃点是否未设置为 Internet。 如果该设置为“虚拟设备”或“虚拟网络网关”，则必须确保在不修改数据包的情况下虚拟设备或本地设备可以正确地将数据包路由回 Internet 目标 。

    d.  否则，请将下一跃点更改为“Internet”，选择“保存”，然后验证后端运行状况 。

1.  由通过 BGP 连接到虚拟网络的 ExpressRoute/VPN 播发的默认路由：

    a.  如果拥有通过 BGP 连接到虚拟网络的 ExpressRoute/VPN，并且打算播发默认路由，则必须确保在不修改数据包的情况下可以将其路由回 Internet 目标。 可以使用应用程序网关门户中的“连接排查”选项进行验证。

    b.  手动将目标选择为任何可在 Internet 上路由的 IP 地址，如 1.1.1.1。 将目标端口设置为任意端口，然后验证连接性。

    c.  如果下一跃点是虚拟网络网关，则可能存在通过 ExpressRoute 或 VPN 播发的默认路由。

1.  如果虚拟网络上配置了自定义 DNS 服务器，请验证服务器是否可以解析公共域。 如果应用程序网关必须连接到 OCSP 服务器等外部域或需要检查证书的吊销状态，则可能需要使用公共域名解析。

1.  若要验证应用程序网关是否正常运行，请在门户中转到“资源运行状况”选项，然后验证状态是否为“正常” 。 如果显示“不正常”或“降级”状态，请[联系支持人员](https://azure.microsoft.com/support/options/) 。

<a name="next-steps"></a>后续步骤
----------

详细了解[应用程序网关诊断和日志记录](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。
