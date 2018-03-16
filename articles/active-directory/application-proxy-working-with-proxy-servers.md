---
title: "使用现有的本地代理服务器和 Azure AD | Microsoft 文档"
description: "介绍如何使用现有的本地代理服务器。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 85b45a828dbb06325b6a1c9453117849f48a27c8
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2018
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>使用现有的本地代理服务器

本文介绍如何将 Azure Active Directory (Azure AD) 应用程序代理连接器配置为使用出站代理服务器。 本文的目标读者为在网络环境中包含现有代理的客户。

首先，我们探讨以下主要部署方案：
* 将连接器配置为绕过本地出站代理。
* 将连接器配置为使用出站代理来访问 Azure AD 应用程序代理。

有关连接器工作原理的详细信息，请参阅[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)。

## <a name="bypass-outbound-proxies"></a>绕过出站代理

连接器具有可发出出站请求的基础 OS 组件。 这些组件会使用 Web 代理自动发现 (WPAD) 自动尝试查找网络上的代理服务器。

OS 组件尝试通过针对 wpad.domainsuffix 执行 DNS 查找来查找代理服务器。 如果查找解析了 DNS，将向 wpad.dat 的 IP 地址发出 HTTP 请求。 此请求将成为环境中的代理配置脚本。 连接器使用此脚本选择出站代理服务器。 但是，连接器流量可能仍然不会通过，因为需要在代理服务器上完成其他配置设置。

可以将连接器配置为绕过本地代理，以确保它使用与 Azure 服务的直接连接。 我们建议使用此方法（只要网络策略允许这样做），因为这样可以少维护一项配置。

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
为了确保连接器更新程序服务也绕过代理，请对 ApplicationProxyConnectorUpdaterService.exe.config 文件进行类似的更改。 此文件位于 C:\Program Files\Microsoft AAD App Proxy Connector Updater。

请务必备份原始文件，以防到时需要还原到默认的 .config 文件。

## <a name="use-the-outbound-proxy-server"></a>使用出站代理服务器

某些环境要求所有出站流量通过出站代理而不发生异常。 因此，绕过代理是不可行的。

可按下图所示将连接器流量配置为通过出站代理：

 ![将连接器流量配置为通过出站代理发送到 Azure AD 应用程序代理。](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

由于只会发生出站流量，因此不需要配置通过防火墙进行入站访问。

>[!NOTE]
>应用程序代理不支持对其他代理进行身份验证。 连接器/更新程序网络服务帐户应能够在不进行身份验证的情况下连接到代理。

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>步骤 1：将连接器和相关服务配置为通过出站代理

如果在环境中启用并正确配置了 WPAD，连接器会自动发现出站代理服务器并尝试使用它。 但是，可以显式将连接器配置为通过出站代理。

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

接下来，通过对 C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config 下的文件进行类似的更改，将连接器更新程序服务配置为使用代理。

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>步骤 2：将代理配置为允许来自连接器和相关服务的流量流过

对于出站代理，需要考虑到四个方面：
* 代理出站规则
* 代理身份验证
* 代理端口
* SSL 检查

#### <a name="proxy-outbound-rules"></a>代理出站规则
允许访问连接器服务的以下终结点：

* * .msappproxy.net
* * .servicebus.windows.net

为了完成初始注册，需允许访问以下终结点：

* login.windows.net
* login.microsoftonline.com

如果不能通过 FQDN 允许连接，请使用以下选项改为指定 IP 范围：

* 允许连接器对所有目标进行出站访问。
* 允许连接器对所有 [Azure 数据中心 IP 范围](https://www.microsoft.com/en-gb/download/details.aspx?id=41653)进行出站访问。 使用 Azure 数据中心 IP 范围列表的难点在于，该列表每周更新。 需要制定一个流程来确保相应地更新访问规则。 只使用 IP 地址的子集可能导致配置中断。

#### <a name="proxy-authentication"></a>代理身份验证

目前不支持代理身份验证。 目前的建议是允许连接器对 Internet 目标进行匿名访问。

#### <a name="proxy-ports"></a>代理端口

连接器使用 CONNECT 方法建立基于 SSL 的出站连接。 此方法实质上是通过出站代理建立一个隧道。 将代理服务器配置为允许与端口 443 和 80 建立隧道连接。

>[!NOTE]
>服务总线在通过 HTTPS 运行时，将使用端口 443。 但是，在默认情况下，服务总线会尝试建立直接 TCP 连接，仅当直接连接失败时，才回退到 HTTPS。

#### <a name="ssl-inspection"></a>SSL 检查
请勿对连接器流量使用 SSL 检查，因为这会导致连接器流量出现问题。 连接器使用证书对应用程序代理服务进行身份验证，在 SSL 检查过程中，该证书可能会丢失。 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>排查连接器代理问题和服务连接问题
现在，应会看到所有流量都会流过代理服务器。 如果遇到问题，以下故障排除信息应会有所帮助。

识别和排查连接器连接问题的最佳方法就是在启动连接器服务时，创建“网络”捕获。 以下是一些关于捕获和筛选网络跟踪的小技巧。

可以使用自选的监视工具。 本文使用了 Microsoft 消息分析器。 可[从 Microsoft 下载](https://www.microsoft.com/download/details.aspx?id=44226)该工具。

以下示例特定于消息分析器，但其原理适用于任何分析工具。

### <a name="take-a-capture-of-connector-traffic"></a>创建连接器流量的捕获

在初始故障排除期间，请执行以下步骤：

1. 通过 services.msc 停止 Azure AD 应用程序代理连接器服务。

   ![services.msc 中的 Azure AD 应用程序代理连接器服务](./media/application-proxy-working-with-proxy-servers/services-local.png)

2. 以管理员身份运行消息分析器。
3. 选择“启动本地跟踪”。

   ![启动网络捕获](./media/application-proxy-working-with-proxy-servers/start-local-trace.png)

3. 启动 Azure AD 应用程序代理连接器服务。
4. 停止网络捕获。

   ![停止网络捕获](./media/application-proxy-working-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>检查连接器通信流是否绕过出站代理

如果将应用程序代理连接器配置为绕过代理服务器并直接连接到应用程序代理服务，可通过网络捕获查看失败的 TCP 连接尝试。 

使用“消息分析器”筛选器来标识这些尝试。 在筛选框中输入 `property.TCPSynRetransmit`，选择“应用”。 

SYN 数据包是为了建立 TCP 连接而发送的第一个数据包。 如果此数据包未返回响应，则重新尝试 SYN。 可以使用上面的筛选器查看任何重新传输的 SYN。 然后，可以检查这些 SYN 是否对应于连接器相关的任何流量。

如果连接器预期应与 Azure 服务建立直接连接器，端口 443 上的 SynRetransmit 响应明确表示网络或防火墙出现了问题。

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>检查连接器流量是否使用出站代理

如果将应用程序代理连接器通信流配置为通过代理服务器，可查看指向代理的失败 http 连接。 

若要筛选这些连接尝试的网络捕获，请在“消息分析器”筛选器中输入 `(https.Request or https.Response) and tcp.port==8080`，将 8080 替换为自己的代理服务端口。 选择“应用”可查看筛选结果。 

上面的筛选器只显示传入/传出代理端口的 HTTP 请求和响应。 假设想要查找显示与代理服务器之间的通信的 CONNECT 请求。 成功后，会获得 HTTP OK (200) 响应。

如果看到其他响应代码（如 407 或 502），表示代理要求身份验证或者出于其他某种原因而不允许该流量。 在这种情况下，请咨询代理服务器支持团队。

## <a name="next-steps"></a>后续步骤

- [了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)

- 如果有任何关于连接器连接的问题，请在 [Azure Active Directory 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD)提问，或创建一个支持团队票证。
