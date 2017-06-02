---
title: "使用现有的本地代理服务器和 Azure AD | Microsoft 文档"
description: "介绍如何使用现有的本地代理服务器。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: ea928ba4d13970a32123a8ada8575658cecde5d8
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---

# <a name="work-with-existing-on-premises-proxy-servers"></a>使用现有的本地代理服务器

本文介绍如何将 Azure Active Directory (Azure AD) 应用程序代理连接器配置为使用出站代理服务器。 本文的目标读者为在网络环境中包含现有代理的客户。

首先，我们探讨以下主要部署方案：
* 将连接器配置为绕过本地出站代理。
* 将连接器配置为使用出站代理来访问 Azure AD 应用程序代理。

有关连接器工作原理的详细信息，请参阅[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)。

## <a name="configure-the-outbound-proxy"></a>配置出站代理

如果环境中有出站代理，请使用具有适当权限的帐户配置出站代理。 由于安装程序在执行安装的用户的上下文中运行，因此可以使用 Microsoft Edge 或其他 Internet 浏览器来检查配置。

在 Microsoft Edge 中配置代理设置：

1. 转到“设置” > “查看高级设置” > “打开代理设置” > “手动代理设置”。
2. 将“使用代理服务器”设置为“打开”，选中“请勿将代理服务器用于本地(Intranet)地址”复选框，然后更改地址和端口以反映本地代理服务器。
3. 填写所需的代理设置。

   ![代理设置对话框](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)

## <a name="bypass-outbound-proxies"></a>绕过出站代理

连接器具有可发出出站请求的基础 OS 组件。 这些组件会自动尝试查找网络上的代理服务器。 这些组件使用 Web 代理自动发现 (WPAD)（如果已在环境中启用）。

OS 组件尝试通过针对 wpad.domainsuffix 执行 DNS 查找来查找代理服务器。 如果解析了 DNS，将向 wpad.dat 的 IP 地址发出 HTTP 请求。 此请求将成为环境中的代理配置脚本。 连接器使用此脚本选择出站代理服务器。 但是，连接器流量可能仍然不会通过，因为需要在代理服务器上完成其他配置设置。

可以将连接器配置为绕过本地代理，以确保它使用与 Azure 服务的直接连接。 我们建议使用此方法（如果网络策略允许这样做），因为这样可以少维护一项配置。

若要对连接器禁用出站代理，请编辑 C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config 文件并添加 *system.net* 节，如以下代码示例中所示：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```
为了确保连接器更新程序服务也绕过代理，请对 C:\Program Files\Microsoft AAD App Proxy Connector Updater 路径下的 ApplicationProxyConnectorUpdaterService.exe.config 文件进行类似的更改。

请务必备份原始文件，以防到时需要还原到默认的 .config 文件。

## <a name="use-the-outbound-proxy-server"></a>使用出站代理服务器

某些环境要求所有出站流量通过出站代理而不发生异常。 因此，绕过代理是不可行的。

可按下图所示将连接器流量配置为通过出站代理。

 ![将连接器流量配置为通过出站代理发送到 Azure AD 应用程序代理。](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

由于只会发生出站流量，因此不需要配置通过防火墙进行入站访问。

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>步骤 1：将连接器和相关服务配置为通过出站代理

如前所述，如果在环境中启用并正确配置了 WPAD，连接器将自动发现出站代理服务器并尝试使用它。 但是，可以显式将连接器配置为通过出站代理。

为此，请编辑 C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config 文件并添加 *system.net* 节，如以下代码示例中所示。 更改 *proxyserver:8080*，反映本地代理服务器的名称或 IP 地址及其侦听的端口。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

接下来，通过对 C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config 路径下的文件进行类似的更改，将连接器更新程序服务配置为使用代理。

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>步骤 2：将代理配置为允许来自连接器和相关服务的流量流过

对于出站代理，需要考虑到四个方面：
* 代理出站规则
* 代理身份验证
* 代理端口
* SSL 检查

#### <a name="proxy-outbound-rules"></a>代理出站规则
允许访问连接器服务的以下终结点：

* *.msappproxy.net
* *.servicebus.windows.net

为了完成初始注册，需允许访问以下终结点：

* login.windows.net
* login.microsoftonline.com

连接器服务使用的底层服务总线控制通道还要求与特定的 IP 地址建立连接。 在服务总线改用 FQDN 之前，可以使用两个选项：

* 允许连接器对所有目标进行出站访问。
* 允许连接器对 [Azure 数据中心 IP 范围](https://www.microsoft.com/en-gb/download/details.aspx?id=41653)进行出站访问。

>[!NOTE]
>使用 Azure 数据中心 IP 范围列表的难点在于，该列表每周更新。 需要制定一个流程来确保相应地更新访问规则。
>

#### <a name="proxy-authentication"></a>代理身份验证

目前不支持代理身份验证。 目前的建议是允许连接器对 Internet 目标进行匿名访问。

#### <a name="proxy-ports"></a>代理端口

连接器使用 CONNECT 方法建立基于 SSL 的出站连接。 此方法实质上是通过出站代理建立一个隧道。 某些代理服务器默认只允许与标准 SSL 端口（例如 443）建立出站隧道连接。 在这种情况下，必须将代理服务器配置为允许与其他端口建立隧道连接。

将代理服务器配置为允许与非标准 SSL 端口 8080、9090、9091 和 10100-10120 建立隧道连接。

>[!NOTE]
>服务总线在通过 HTTPS 运行时，将使用端口 443。 但是，在默认情况下，服务总线会尝试建立直接 TCP 连接，仅当直接连接失败时，才回退到 HTTPS。
>

为了确保服务总线流量也通过出站代理服务器发送，请确保连接器无法直接连接到 Azure 服务的端口 9350、9352 和 5671。

#### <a name="ssl-inspection"></a>SSL 检查
不要对连接器流量使用 SSL 检查，因为这会导致连接器流量出现问题。

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>排查连接器代理问题和服务连接问题
现在，应会看到所有流量都会流过代理服务器。 如果遇到问题，以下故障排除信息应会有所帮助。

识别和排查连接器连接问题的最佳方法就是在启动连接器服务时，在连接器服务上创建“网络”捕获。 这可能是一个繁琐的任务。下面提供了有关捕获和筛选网络跟踪的简要提示。

可以使用自选的监视工具。 本文使用了 Microsoft 网络监视器 3.4。 可[从 Microsoft 下载](https://www.microsoft.com/download/details.aspx?id=4865)该工具。

以下部分使用的示例和筛选器特定于网络监视器，但可以向任何分析工具运用相同的原理。

### <a name="take-a-capture-by-using-network-monitor"></a>使用网络监视器创建捕获

开始捕获：

1. 打开网络监视器并单击“新建捕获”。
2. 单击“开始”按钮。

   ![网络监视器窗口](./media/application-proxy-working-with-proxy-servers/network-capture.png)

完成捕获后，单击“停止”按钮结束捕获。

### <a name="take-a-capture-of-connector-traffic"></a>创建连接器流量的捕获

在初始故障排除期间，请执行以下步骤：

1. 通过 services.msc 停止 Azure AD 应用程序代理连接器服务。
2. 启动网络捕获。
3. 启动 Azure AD 应用程序代理连接器服务。
4. 停止网络捕获。

   ![services.msc 中的 Azure AD 应用程序代理连接器服务](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-requests-from-the-connector-to-the-proxy-server"></a>查看连接器向代理服务器发出的请求

获取网络捕获后，可对其进行筛选。 分析跟踪的关键是了解如何筛选捕获。

下面显示了一个筛选器（其中 8080 是代理服务端口）：

**(http.Request or http.Response) and tcp.port==8080**

如果在“显示筛选器”窗口中输入此筛选器并选择“应用”，将会根据此筛选器筛选捕获的流量。

上面的筛选器只显示传入/传出代理端口的 HTTP 请求和响应。 如果连接器最初配置为使用代理服务器，筛选器将显示类似于下面的信息：

 ![筛选的 HTTP 请求和响应示例列表](./media/application-proxy-working-with-proxy-servers/http-requests.png)

假设你想要专门查找显示与代理服务器之间的通信的 CONNECT 请求。 成功后，将获得 HTTP OK (200) 响应。

如果看到其他响应代码（如 407 或 502），表示代理要求身份验证或者出于其他某种原因而不允许该流量。 在这种情况下，请咨询代理服务器支持团队。

### <a name="identify-failed-tcp-connection-attempts"></a>识别失败的 TCP 连接尝试

我们可能想要解决的另一种常见情况是，连接器尝试建立直接连接但失败。

可帮助轻松识别此问题的另一个网络监视器筛选器是：

**property.TCPSynRetransmit**

SYN 数据包是为了建立 TCP 连接而发送的第一个数据包。 如果此数据包未返回响应，则重新尝试 SYN。 可以使用上面的筛选器查看任何重新传输的 SYN。 然后，可以检查这些 SYN 是否对应于连接器相关的任何流量。

以下示例演示尝试与服务总线端口 9352 建立连接失败：

 ![失败的连接尝试的示例响应](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

如果看到类似于上面的响应，表示连接器正在尝试与 Azure 服务总线服务直接通信。 如果连接器预期应与 Azure 服务建立直接连接器，此响应明确表示你的网络或防火墙出现了问题。

>[!NOTE]
>如果配置为使用代理服务器，此响应可能表示服务总线正在尝试建立直接 TCP 连接，然后再改为尝试通过 HTTPS 建立连接。
>

并非所有用户都能执行网络跟踪分析。 但它可能是一个有用的工具，因为它能快速获取有关网络中发生的情况的信息。

如果连接器连接问题持续让你感到困扰，请创建支持票证向我们的支持团队反映问题。 我们将很乐意效劳，提供更多的故障排除方法。

有关解决应用程序代理连接器错误的信息，请参阅[应用程序代理故障排除](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-troubleshoot)。

## <a name="next-steps"></a>后续步骤

[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)<br>
[如何以无提示方式安装 Azure AD 应用程序代理连接器](active-directory-application-proxy-silent-installation.md)

