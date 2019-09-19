---
title: Azure 应用程序网关上的后端运行状况问题疑难解答
description: 本文将指导你解决 Azure 应用程序网关的后端运行状况问题
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 1fd4e9156e29133b1db4fe9ab9a0825eb1aa3b55
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097578"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>排查应用程序网关上的后端运行状况问题
==================================================

<a name="overview"></a>概述
--------

默认情况下，应用程序网关会探查后端服务器检查其运行状况状态以及是否已准备好为请求提供服务。 用户可以创建自定义探测，并提及主机名、要探测的路径以及要接受为正常状态的状态代码。 在这两种情况下，如果后端服务器未成功响应，应用程序网关会将服务器标记为不正常，并停止将请求转发到服务器。 服务器成功启动响应后，它将继续为请求提供服务。

### <a name="how-to-check-backend-health"></a>如何检查后端运行状况

若要检查后端池的运行状况，可以使用 Azure 门户中的 "后端运行状况" 页。 或者，可以使用[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0)、 [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)或[REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth)来获取运行状况状态。 有关每种方法的详细信息，请查看链接的文章。

以上所述的任何方法检索到的状态可以是以下三种类型：

1.  状态良好

2.  不正常

3.  未知

如果服务器的后端运行状况状态为 "正常"，则表示应用程序网关会将请求转发到该服务器。 但如果后端池中所有服务器的后端运行状况不正常或未知，则在应用程序访问期间可能会遇到一些问题。 本文档介绍当后端服务器可能不正常或未知时显示的每个错误的症状、原因和解决方案。

<a name="backend-health-status-unhealthy"></a>后端运行状况状态不正常
-------------------------------

如果后端运行状况状态显示为 "不正常"，将在门户中看到它，如以下屏幕截图所示：

![应用程序网关后端运行状况-不正常](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

或者，如果你使用 Azure PowerShell、CLI 或 Azure REST API 查询，你将看到如下所示的响应：
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
一旦后端池中的所有服务器的后端服务器状态都为 "不正常"，请求就不会转发到这些请求，应用程序网关会将错误的网关错误502返回给发出请求的客户端。 若要解决此问题，请查看 "后端运行状况" 选项卡的 "详细信息" 列。

"后端运行状况" 选项卡的 "详细信息" 列中显示的消息可提供有关该问题的详细信息，并根据这些信息开始解决问题。

> [!NOTE]
> 以\<\>协议\>：//127.0.0.1：\<端口/的格式发送默认探测请求，例如， <http://127.0.0.1/>对于端口80上的 http 探测，只考虑 http 状态代码200-399 的响应正常响应。 协议和目标端口从 HTTP 设置继承而来。 如果你希望应用程序网关在不同的协议、主机名或路径上探测，并接受其他状态代码为 "正常"，请配置自定义探测，并将其与 HTTP 设置关联。

<a name="error-messages"></a>错误消息
------------------------
#### <a name="backend-server-timeout"></a>后端服务器超时

**消息：** 后端响应应用程序网关\'运行状况探测所花的时间超过了探测设置中的超时阈值。

原因：应用程序网关将 HTTP （S）探测请求发送到后端服务器后，它将等待后端服务器的响应已配置特定的时间。 如果后端服务器在超时值内没有响应，则会将其标记为不正常，直到它再次在超时之前开始响应。

**解决方法：** 检查后端服务器或应用程序是否在配置的超时时间内未响应，并检查应用程序依赖关系，例如，如果数据库有任何问题，这可能会导致响应延迟。 如果你知道应用程序的行为，并且它应该只在超时值后响应，则会增加自定义探测设置的超时值。
需要使用自定义探测来更改超时值。如果要了解如何配置自定义探测，请[查看文档页](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)。

若要增加超时值，请按照以下步骤操作：

1.  直接访问后端服务器，并在该页面上检查服务器响应所用的时间。 您可以使用任何工具来访问，包括使用开发人员工具的浏览器。

2.  确定应用程序响应所需的时间之后，单击 "运行状况探测" 选项卡，然后选择与 HTTP 设置关联的探测。

3.  输入超出应用程序响应时间的超时值（以秒为单位）。

4.  保存自定义探测设置，如果后端运行状况显示为 "正常"，请检查后端运行状况。

#### <a name="dns-resolution-error"></a>DNS 解析错误

**消息：** 应用程序网关无法为此后端创建探测。 如果未正确输入后端的 FQDN，通常会发生这种情况。 

原因：如果后端池的类型为 "IP 地址/FQDN" 或 "应用服务"，则应用程序网关解析为使用 DNS 服务器（自定义或 Azure 默认）输入的 FQDN 的 IP 地址，并尝试连接到 HTTP 设置中提到的 TCP 端口上的服务器。 但如果显示此消息，则表示应用程序网关不能成功解析输入的 FQDN 的 IP 地址。

**解决方法：**

1.  验证在后端池中输入的 FQDN 是否正确，以及它是否是公共域，尝试从本地计算机解析它。

2.  如果可以解析 IP 地址，VNet 中的 DNS 配置可能会出现问题。

3.  检查是否为 VNet 配置了自定义 DNS 服务器。 如果是这样，请在 DNS 服务器上检查为什么无法将其解析为给定 FQDN 的 IP 地址。

4.  如果是 Azure 默认 DNS，请使用域名注册机构检查是否已完成记录或 CNAME 记录映射。

5.  如果域是专用/内部的，请尝试从同一 VNet 中的 VM 解析它，如果能够解决该问题，请重新启动应用程序网关，然后再次检查。 若要重新启动应用程序网关，需要[停止](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0)并[开始](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0)使用各自文档链接中提供的 PowerShell 命令。

#### <a name="tcp-connect-error"></a>TCP 连接错误

**消息：** 应用程序网关无法连接到后端。
请检查后端是否响应用于探测的端口。
同时检查是否有任何 NSG/UDR/防火墙阻止访问此后端的 Ip 和端口

原因：在 DNS 解析阶段之后，应用程序网关会尝试连接到在 HTTP 设置中配置的 TCP 端口上的后端服务器。 如果应用程序网关无法在指定的端口上建立 TCP 会话，则会将此消息标记为不正常。

**解决方案：** 如果看到此错误，请检查以下各项：

1.  请检查是否可以使用浏览器或 PowerShell 在 HTTP 设置中提到的端口连接到后端服务器，例如，可以使用以下命令：Test-netconnection-ComputerName www.bing.com-端口443

2.  如果提到的端口不是所需的端口，请输入用于连接到后端服务器的应用程序网关的正确端口号

3.  如果无法从本地计算机连接到端口，请执行以下操作：

    a.  检查后端服务器的 NIC 和子网的 NSG 设置，并检查是否允许到已配置端口的入站连接。 如果不允许，请创建新规则以允许连接。 若要了解如何创建 NSG 规则，请[查看文档页](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)。

    b.  如果允许出站公共和专用流量，请检查应用程序网关的子网的 NSG 设置，以便可以建立连接。 检查（a）中链接的文档，以了解有关如何创建 NSG 规则的详细信息。
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  检查应用程序网关和后端服务器子网的 UDR 设置，以了解任何路由异常情况。 确保 UDR 不会将流量定向到后端子网。 例如，通过 ExpressRoute/VPN 检查到网络虚拟设备的路由或要播发到应用程序网关子网的默认路由。

    d.  若要检查 NIC 的有效路由和规则，可以使用以下 PowerShell 命令。
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
4.  如果找不到 NSG 或 UDR 的任何问题，请检查后端服务器上是否存在应用程序相关的问题，因为哪些客户端无法在配置的端口上建立 TCP 会话。 要检查的事项如下：

    e.  打开 "命令提示符" （Win +\> R-cmd）并输入 netstat 并按 enter

    f.  检查服务器是否正在侦听配置的端口。
        例如：
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    g.  如果不是，请检查你的 web 服务器设置，例如 IIS 中的网站绑定、NGINX 中的服务器块和 Apache 中的虚拟主机

    h.  检查 OS 防火墙设置，以确保允许到端口的传入流量

#### <a name="http-status-code-mismatch"></a>HTTP 状态代码不匹配

**消息：** 后端\'的 HTTP 响应的状态代码与探测设置不匹配。 应为： {HTTPStatusCode0} 收到： {HTTPStatusCode1}。

原因：建立 TCP 连接并完成 SSL 握手后（如果启用了 SSL），应用程序网关会将探测作为 HTTP GET 请求发送到后端服务器。 如上所述\<，默认探测为协议\>：//127.0.0.1：\<port\>/，并将风行一时200-399 中的响应状态代码视为正常。 如果服务器返回任何其他状态代码，则会将此消息标记为不正常。

**解决方案：** 根据后端服务器的响应代码，你可以执行以下步骤。 下面列出了几个常见状态代码：

| **错误** | **操作** |
| --- | --- |
| 探测状态代码不匹配：收到401 | 检查后端服务器是否要求身份验证。 应用程序网关探测此时无法传递用于身份验证的凭据。 允许\"探测状态代码\"中的 HTTP 401 匹配或探测到服务器不需要身份验证的路径。 | |
| 探测状态代码不匹配：收到403 | 禁止访问。 检查后端服务器上是否允许访问路径。 | |
| 探测状态代码不匹配：收到404 | 找不到页面。 检查主机名路径（如果可在后端服务器上访问）。 将 hostname 或 path 参数更改为可访问的值。 | |
| 探测状态代码不匹配：收到405 | 应用程序网关的探测请求使用 HTTP GET 方法。 检查服务器是否允许它。 | |
| 探测状态代码不匹配：收到500 | 内部服务器错误。 检查后端服务器的运行状况，以及服务是否正在运行。 | |
| 探测状态代码不匹配：收到503 | 服务不可用。 检查后端服务器的运行状况，以及服务是否正在运行。 | |

或者，如果你认为响应是 legit 的，并且你希望应用程序网关接受其他状态代码，则可以创建自定义探测。 修改这一点将非常有用，在这种情况下，后端网站需要身份验证，并且由于探测请求不携带任何用户凭据，它们将失败，并且后端服务器将返回 HTTP 401 状态代码。

若要创建自定义探测，请按照[此处](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)列出的步骤进行操作。

#### <a name="http-response-body-mismatch"></a>HTTP 响应正文不匹配

**消息：** 后端\'s HTTP 响应的正文与探测设置不匹配。 收到的响应正文不包含 {string}。

原因：创建自定义探测时，可以通过匹配响应正文中的字符串来将后端服务器标记为正常。 例如，可以将应用程序网关配置为接受 "未经授权" 作为要匹配的字符串。 如果探测请求的后端服务器响应包含字符串 "未经授权"，则会将其标记为 "正常"。
否则，会将此消息标记为不正常。

**解决方案：** 可以通过执行以下步骤来解决此问题：

1.  在本地或从探测路径上的客户端计算机访问后端服务器，并检查响应正文。

2.  检查应用程序网关自定义探测配置，验证响应正文是否与配置的内容相匹配。

3.  如果二者不匹配，则将探测配置更改为 "接受"。

可在[此处](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)详细了解应用程序网关探测匹配。

#### <a name="backend-server-certificate-invalid-ca"></a>后端服务器证书无效的 CA

**消息：** 后端使用的服务器证书不是由知名证书颁发机构（CA）签名的。 通过上传后端使用的服务器证书的根证书，将应用程序网关上的后端列入白名单。

原因：应用程序网关 v2 的端到端 SSL 要求验证后端服务器的证书，使服务器视为正常。
对于要信任的 SSL 证书，必须由应用程序网关的受信任存储区中包含的 CA 颁发后端服务器证书。 如果证书不是由受信任的 CA 颁发的（例如自签名证书），则用户应将颁发者的证书上传到应用程序网关。

**解决方案：** 按照以下步骤导出受信任的根证书并将其上传到应用程序网关（下面提供的步骤适用于 Windows 客户端）

1.  登录到托管应用程序的计算机

2.  按 Win + R 或右键单击 "开始" 按钮并选择 "运行" 打开运行

3.  输入 certmgr.msc，然后按 Enter。 你还可以在 "开始" 菜单中搜索证书管理器。

4.  找到证书（通常位于 " \'证书-当前用户\\"\\"\'个人证书"），然后打开证书

5.  在 "证书属性" 中，选择 "证书路径" 选项卡

6.  选择根证书，然后选择 "查看证书" 选项

7.  在 "证书属性" 中，切换到 "详细信息" 选项卡

8.  在 "详细信息" 选项卡中，选择 "复制到文件" 选项，并将文件保存在64编码的 x.509 （。CER）格式

9.  保存后，在 Azure 门户中打开 "应用程序网关 HTTP 设置" 页

10. 打开 HTTP 设置并单击 "添加证书"，并找到最近保存的证书文件

11. 单击 "保存" 以保存 HTTP 设置

或者，通过使用浏览器并从浏览器导出根证书，可以从客户端计算机中导出根证书。

有关在应用程序网关上提取和上传受信任的根证书的详细步骤，请参阅[此处](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)。

#### <a name="trusted-root-certificate-mismatch"></a>受信任的根证书不匹配

**消息：** 后端使用的服务器证书的根证书与添加到应用程序网关的受信任根证书不匹配。 确保将正确的根证书添加到后端允许列表

原因：应用程序网关 v2 的端到端 SSL 要求验证后端服务器的证书，使服务器视为正常。
对于要信任的 SSL 证书，必须由应用程序网关的受信任存储区中包含的 CA 颁发后端服务器证书。 如果证书不是由受信任的 CA 颁发的（例如自签名证书），则用户应将颁发者的证书上传到应用程序网关。

已上传到应用程序网关 HTTP 设置的证书必须与后端服务器证书的根证书匹配。

**解决方案：** 如果看到上述错误消息，则表示已上传到应用程序网关的证书与上传到后端服务器的证书不匹配。

按照上面提到的步骤1-11 将正确的受信任的根证书上传到应用程序网关。

有关在应用程序网关上提取和上传受信任的根证书的详细步骤，请参阅[此处](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)。
> [!NOTE]
> 如果后端服务器未与 TLS 握手中的根 > 中间（如果适用） > 叶，则也可能会出现上述错误。 若要验证，可以从任何客户端使用 OpenSSL 命令，并使用应用程序网关探测中配置的设置连接到后端服务器。

例如，应用于对象的
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
如果输出未显示要返回的证书的完整链，请再次导出包含根证书的完整链中的证书，并在后端服务器中配置该证书。 

已连接（00000188） \
深度 = 0 OU = 已验证域控件，CN \*=. example.com \
验证错误： num = 20：无法获取本地颁发者证书 \
验证返回： 1 \
深度 = 0 OU = 已验证域控件，CN \*=. example.com \
验证错误： num = 21：无法验证第一个证书 \
验证返回： 1 \
\-\-\-\
证书链 \
 0 s：/OU = 域控件验证的/CN = * .com \
   i：/C = US/ST = 亚利桑那/L = Scottsdale/O = GoDaddy，inc./ou =http://certs.godaddy.com/repository//CN=Go Daddy Secure 证书颁发机构-G2 \
\-----开始证书-----\
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
\-----结束证书-----

#### <a name="backend-certificate-invalid-common-name-cn"></a>后端证书无效的公用名（CN）

**消息：** 后端证书的公用名（CN）与探测的主机标头不匹配。

原因：应用程序网关验证后端 http 设置中指定的主机名是否与后端服务器 SSL 证书提供的公用名（CN）的名称相匹配。 这只是 Standard_v2 和 WAF_v2 SKU 的行为。 标准 SKU 和 WAF SKU 的 SNI 在后端池地址中设置为 FQDN。

在 v2 SKU 中，如果存在默认探测（未配置自定义探测），则将从 "HTTP 设置" 中提到的主机名设置 SNI，或者如果 "HTTP 设置" 中提到了 "从后端地址中选取主机名"，则将从后端地址池包含有效的 FQDN。

如果有与 HTTP 设置关联的自定义探测，将从自定义探测配置中提到的主机名设置 SNI，或者，如果在自定义探测中选中了 "从后端 HTTP 设置中选取主机名"，则将从 HTTP 中提到的主机名设置它设置。

如果在 HTTP 设置中设置了 "从后端地址中选取主机名"，则后端地址池必须包含有效的 FQDN。

如果看到上述错误消息，则表示后端证书的公用名（CN）与自定义探测或 HTTP 设置中配置的主机名（如果选择 "从后端 HTTP 设置中选取主机名"）不匹配。 如果使用默认探测，主机名将设置为 "127.0.0.1"。 如果这不是所需的值，则应创建自定义探测，并将其关联到 HTTP 设置。

**解决方案：**

若要解决此问题，请执行以下步骤：

对于 Windows：

1.  登录到托管应用程序的计算机

2.  按 Win + R 或右键单击 "开始" 按钮并选择 "运行" 打开运行

3.  输入 certmgr.msc，然后按 Enter。 你还可以在 "开始" 菜单中搜索证书管理器。

4.  找到证书（通常位于 " \'证书-当前用户\\"\\"\'个人证书"），然后打开证书

5.  在 "详细信息" 选项卡中，检查证书的使用者

6.  验证证书的 CN，并在自定义探测或 HTTP 设置的 "主机名" 字段中输入相同的内容（如果选择了 "从后端 HTTP 设置选取主机名"。 如果这不是你的网站所需的主机名，则必须为该域获取证书，或者在自定义探测/http 设置配置中输入正确的主机名。

对于使用 OpenSSL 的 Linux

1.  在 OpenSSL 中运行以下命令。 
```
openssl x509 -in certificate.crt -text -noout
```

2.  从显示的属性中，查找证书的 CN，并在 http 设置的 "主机名" 字段中输入相同的名称。 如果这不是你的网站所需的主机名，则必须为该域获取证书，或者在自定义探测/http 设置配置中输入正确的主机名。

#### <a name="backend-certificate-is-invalid"></a>后端证书无效

**消息：** 后端证书无效。 当前日期不在证书的\""有效\"起始\"\"日期" 和 "有效截止日期" 范围之内。

原因：每个证书都带有有效性，并且 HTTPS 连接不会安全，除非服务器的 SSL 证书有效。
当前数据必须在 "有效起始于" 范围内。 如果不是，则该证书将被视为无效，这将是一个安全问题。 此时，应用程序网关会将后端服务器标记为不正常。

**解决方案：** 如果 SSL 证书已过期，请向供应商续订证书，并将服务器设置更新为新证书。 如果它是自签名证书，则需要生成一个有效证书，并将根证书上传到应用程序网关的 HTTP 设置。 要执行此操作，请执行以下步骤：

1.  在门户中打开应用程序网关 HTTP 设置

2.  选择包含过期证书的 HTTP 设置，选择 "添加证书" 并打开新的证书文件

3.  使用证书旁边的 "删除" 图标删除旧证书，并单击 "保存"

#### <a name="certificate-verification-failed"></a>证书验证失败

**消息：** 无法验证后端证书的有效性。 若要找出原因，请检查打开 SSL 诊断，查找与错误代码 {errorCode} 关联的消息

原因：当应用程序网关无法验证证书的有效性时，会发生此错误。

**解决方案：** 若要解决此问题，请检查服务器上的证书（如果该证书已正确创建）。 例如，你可以使用[OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html)来验证证书及其属性，并尝试将证书 Reuploading 为应用程序网关的 HTTP 设置。

<a name="backend-health-status-unknown"></a>后端运行状况状态未知
-------------------------------
如果后端运行状况显示为 "未知"，则它将显示在门户中，如以下屏幕截图所示：

![应用程序网关后端运行状况-未知](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

如果后端运行状况显示为 "未知"，则可能是由于以下一个或多个原因：

1.  应用程序网关子网中的 NSG 阻止从 "Internet" 对端口65503-65534 （v1 SKU）或65200-65535 （v2 SKU）进行入站访问
2.  具有默认路由（0.0.0.0/0）的应用程序网关子网中的 UDR，下一跃点不是 Internet
3.  ExpressRoute/VPN 通过 BGP 连接到 VNet 的默认路由
4.  在 VNet 中配置的自定义 DNS 服务器无法解析公共域名
5.  应用程序网关处于不正常状态

**解决方案：**

1.  检查 NSG 是否正在阻止从 "Internet" 访问端口65503-65534 （v1 SKU）或65200-65535 （v2 SKU）

    a.  在应用程序网关 "概述" 选项卡中，选择 "虚拟网络/子网" 链接

    b.  在虚拟网络的 "子网" 选项卡中，选择已在其中部署应用程序网关的子网

    c.  检查是否已配置任何 NSG

    d.  如果有一个，请在 "搜索" 选项卡中或在 "所有资源" 下搜索该 NSG 资源

    e.  在 "入站规则" 部分中，添加一个入站规则，以允许 v1 SKU 的目标端口范围65503-65534 或包含源 as Any 或 Internet 的 65200-65535 v2 SKU

    f.  单击 "保存" 并验证是否可以成功查看后端

    g.  也可以通过[PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)来执行此操作

2.  检查 UDR 是否具有默认路由（0.0.0.0/0）和下一跃点是否为 Internet
    
    a.  执行步骤 1. a 和 1. b 确定子网

    b.  检查是否配置了任何 UDR。 如果有一个，请在搜索栏中或在 "所有资源" 下搜索资源

    c.  检查是否有任何默认路由（0.0.0.0/0）和下一跃点不是 Internet。 如果是虚拟设备或虚拟网络网关，则需要确保虚拟设备或本地设备能够正确地将数据包路由回 internet 目标，而无需修改数据包

    d.  否则，将下一跃点更改为 Internet，单击保存，并验证后端运行状况

3.  ExpressRoute/VPN 通过 BGP 连接到 VNet 的默认路由

    a.  如果通过 BGP 连接到 VNet 的 ExpressRoute/VPN 连接，并且要播发默认路由，则需要确保将数据包路由回 internet 目标而不进行修改

    b.  你可以使用应用程序网关门户中的 "连接故障排除" 选项进行验证

    c.  手动选择目标为任意 internet 可路由 IP 地址（如 "1.1.1.1"）和目标端口作为任何内容并检查连接性。

    d.  如果下一个跃点是虚拟网络网关，则可能存在通过 ExpressRoute 或 VPN 播发的默认路由

4.  如果在 VNet 中配置了自定义 DNS 服务器，请验证服务器是否可以解析公共域。 在应用程序网关必须连接到外部域（如 OCSP 服务器）或检查证书的吊销状态等情况下，可能需要公共域名解析。

5.  若要验证应用程序网关是否正常运行，请在门户中转到资源运行状况选项，并验证其是否为 "正常"。 如果你看到的状态不正常或降级，请[联系支持人员](https://azure.microsoft.com/support/options/)。

<a name="next-steps"></a>后续步骤
----------

若要了解有关应用程序网关诊断和日志记录的详细信息，请参阅[此处](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。
