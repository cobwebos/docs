---
title: "本地数据网关 | Microsoft Docs"
description: "如果 Azure 中的 Analysis Services 服务器要连接到本地数据源，则本地网关是必需的。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 90584f60864589744888817ea71d0eb0d4d170ff


---
# <a name="on-premises-data-gateway"></a>本地数据网关
本地数据网关的作用好似一架桥，提供本地数据源与云中的 Azure Analysis Services 服务器之间的安全数据传输。

网关可以安装在连网的计算机上。 对于 Azure 订阅中有的每个 Azure Analysis Services 服务器，必须安装一个网关。 例如，如果在连接到本地数据源的 Azure 订阅中有两个服务器，则必须在两台连网的独立计算机上安装一个网关。

## <a name="requirements"></a>要求
**最低要求：**

* .NET 4.5 Framework
* 64 位版本的 Windows 7/Windows Server 2008 R2（或更高版本）

**推荐：**

* 8 核 CPU
* 8 GB 内存
* 64 位版本的 Windows 2012 R2（或更高版本）

**重要注意事项：**

* 不能在域控制器上安装网关。
* 一台计算机上只能安装一个网关。
* 在计算机处于开启但未处于休眠状态下安装网关。 如果计算机未处于开启状态，则 Azure Analysis Services 服务器无法连接到本地数据源来刷新数据。
* 不要在使用无线网络连接的计算机上安装网关。 否则，可能会降低性能。
* 若要更改已配置网关的服务器名称，需要重新安装并配置新网关。
* 在某些情况下，使用本机提供程序（如 SQL Server Native Client (SQLNCLI11)）连接到数据源的表格模型可能返回错误。 若要了解详细信息，请参阅[数据源连接](analysis-services-datasource.md)。

## <a name="supported-on-premises-data-sources"></a>支持的本地数据源
对于预览版，网关支持 Azure Analysis Services 服务器与以下本地数据源之间的连接：

* SQL Server
* SQL 数据仓库
* APS
* Oracle
* Teradata

## <a name="download"></a>下载
 [下载网关](https://aka.ms/azureasgateway)

## <a name="install-and-configure"></a>安装和配置
1. 运行安装程序。
2. 选择安装位置，并接受许可条款。
3. 登录 Azure。
4. 指定 Azure Analysis Server 名称。 每个网关只能指定一台服务器。 单击“配置”便可继续进行后续步骤。

    ![登录到 Azure](./media/analysis-services-gateway/aas-gateway-configure-server.png)

## <a name="how-it-works"></a>工作原理
网关在组织网络中的计算机上作为 Windows 服务**本地数据网关**运行。 为与 Azure Analysis Services 配合使用而安装的网关基于用于其他服务（如 Power BI）的同一个网关，但配置方式有所差异。

![工作原理](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

查询和数据流工作原理类似如下所示：

1. 查询是通过使用本地数据源的加密凭据进行创建的。 然后，它将发送到网关队列进行处理。
2. 网关云服务分析该查询，并将请求推送到 [Azure 服务总线](https://azure.microsoft.com/documentation/services/service-bus/)。
3. 本地数据网关会针对挂起的请求轮询 Azure 服务总线。
4. 网关获取查询，对凭据进行解密，然后使用这些凭据连接到数据源。
5. 网关将查询发送到数据源以便执行。
6. 结果会从数据源返回到网关，然后返回到云服务。

## <a name="windows-service-account"></a>Windows 服务帐户
本地数据网关配置为将 NT SERVICE\PBIEgwService 用于 Windows 服务登录凭据。 默认情况下，它有权作为服务登录；这位于正在安装网关的计算机的上下文中。 此凭据与用于连接到本地数据源或 Azure 帐户的帐户不相同。  

如果由于身份验证，代理服务器遇到问题，建议将 Windows 服务帐户更改为域用户或托管服务帐户。

## <a name="ports"></a>端口
网关会创建与 Azure 服务总线之间的出站连接。 它在以下出站端口上进行通信：TCP 443（默认值）、5671、5672、9350 到 9354。  网关不需要入站端口。

建议在防火墙中针对数据区域列出 IP 地址允许列表。 可以下载 [Microsoft Azure 数据中心 IP 列表](https://www.microsoft.com/download/details.aspx?id=41653)。 该列表每周都会进行更新。

> [!NOTE]
> Azure 数据中心 IP 列表中列出的 IP 地址使用的是 CIDR 表示法。 例如，10.0.0.0/24 并不是指 10.0.0.0 到 10.0.0.24。 深入了解 [CIDR 表示法](http://whatismyipaddress.com/cidr)。
>
>

以下是该网关所用的完全限定域名。

| 域名 | 出站端口 | 说明 |
| --- | --- | --- |
| *.powerbi.com |80 |用于下载该安装程序的 HTTP。 |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |高级消息队列协议 (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |通过 TCP 的服务总线中继上的侦听器（需要 443 来获取访问控制令牌） |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |在 Power BI 服务无法访问网关时用于测试 Internet 连接。 |
| *.microsoftonline-p.com |443 |用于根据配置进行身份验证。 |

### <a name="forcing-https-communication-with-azure-service-bus"></a>强制与 Azure 服务总线进行 HTTPS 通信
可以强制网关使用 HTTPS 而非直接 TCP 与 Azure 服务总线进行通信，但这可能显著降低性能。 需要修改 Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config 文件。 将值从 `AutoDetect` 更改为 `Https`。 默认情况下，此文件位于 C:\Program Files\On-premises data gateway。

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>故障排除
实质上，用于将 Azure Analysis Services 连接到本地数据源的本地数据网关与 Power BI 使用的是同一个网关。

如果在安装和配置网关时遇到问题，请参阅 [Power BI 网关疑难解答](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/)。 如果认为防火墙有问题，请参阅“防火墙或代理”部分。

如果认为遇到的是网关代理问题，请参阅 [配置 Power BI 网关的代理设置](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md)。

## <a name="next-steps"></a>后续步骤
* [管理 Analysis Services](analysis-services-manage.md)
* [从 Azure Analysis Services 获取数据](analysis-services-connect.md)



<!--HONumber=Nov16_HO3-->


