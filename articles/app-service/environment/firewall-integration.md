---
title: 锁定应用服务环境出站流量 - Azure
description: 介绍如何集成 Azure 防火墙以保护出站流量
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: af6a32d7e32f23561b207c729402eaea7925f520
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453845"
---
# <a name="locking-down-an-app-service-environment"></a>锁定应用服务环境

应用服务环境 (ASE) 需要访问许多的外部依赖项才能正常运行。 ASE 驻留在客户的 Azure 虚拟网络 (VNet) 中。 客户必须允许 ASE 依赖项流量，对于想要锁定从 VNet 传出的所有流量的客户而言，这是一个问题。

ASE 有许多入站依赖项。 无法通过防火墙设备发送入站管理流量。 此流量的源地址是已知的，并已在[应用服务环境管理地址](https://docs.microsoft.com/azure/app-service/environment/management-addresses)文档中发布。 可以使用该信息创建网络安全组规则来保护入站流量。

ASE 出站依赖项几乎完全是使用 FQDN 定义的，不附带任何静态地址。 缺少静态地址意味着无法使用网络安全组 (NSG) 锁定来自 ASE 的出站流量。 地址会频率更改，用户无法基于当前解析设置规则，然后使用这些规则来创建 NSG。 

保护出站地址的解决方案在于使用可基于域名控制出站流量的防火墙设备。 Azure 防火墙可以根据目标的 FQDN 限制出站 HTTP 和 HTTPS 流量。  

## <a name="configuring-azure-firewall-with-your-ase"></a>在 ASE 中配置 Azure 防火墙 

使用 Azure 防火墙锁定现有 ASE 的传出流量的步骤如下：

1. 为 ASE 子网中的 SQL、存储和事件中心启用服务终结点。 为此，请转到网络门户 >“子网”，然后从服务终结点下拉列表中选择“Microsoft.EventHub”、“Microsoft.SQL”和“Microsoft.Storage”。 为 Azure SQL 启用服务终结点后，还必须为应用的所有 Azure SQL 依赖项配置服务终结点。 

   ![选择服务终结点][2]
  
1. 在 ASE 所在的 VNet 中创建名为 AzureFirewallSubnet 的子网。 遵循 [Azure 防火墙文档](https://docs.microsoft.com/azure/firewall/)中的指导创建 Azure 防火墙。
1. 在 Azure 防火墙 UI >“规则”>“应用程序规则集合”中，选择“添加应用程序规则集合”。 提供名称、优先级，并设置“允许”。 在“FQDN 标记”部分提供名称，将源地址设置为 *，然后选择“应用服务环境 FQDN 标记”和“Windows 更新”。 
   
   ![添加应用程序规则][1]
   
1. 在 Azure 防火墙 UI >“规则”>“网络规则集合”中，选择“添加网络规则集合”。 提供名称、优先级，并设置“允许”。 在“规则”部分提供名称，选择“任何”，将源和目标地址设置为 *，将端口设置为 123。 此规则允许系统使用 NTP 执行时钟同步。 以相同的方式针对端口 12000 创建另一个规则，以帮助诊断任何系统问题。

   ![添加 NTP 网络规则][3]

1. 使用[应用服务环境管理地址]( https://docs.microsoft.com/azure/app-service/environment/management-addresses)中的管理地址创建一个路由表并添加 Internet 的下一跃点。 需要使用路由表条目来避免非对称路由问题。 在 IP 地址依赖项中为下面所示的 IP 地址依赖项添加路由，并添加 Internet 的下一跃点。 将虚拟设备路由添加到 0.0.0.0/0 的路由表，并将 Azure 防火墙专用 IP 地址用作下一跃点。 

   ![创建路由表][4]
   
1. 将创建的路由表分配到 ASE 子网。

#### <a name="deploying-your-ase-behind-a-firewall"></a>在防火墙后部署 ASE

在防火墙后部署 ASE 的步骤与使用 Azure 防火墙配置现有 ASE 的步骤相同，不过，需要创建 ASE 子网，然后遵循上述步骤。 若要在现有的子网中创建 ASE，需要根据[使用资源管理器模板创建 ASE](https://docs.microsoft.com/azure/app-service/environment/create-from-template) 文档中所述使用资源管理器模板。

## <a name="application-traffic"></a>应用程序流量 

完成上述步骤后，ASE 可以正常运行。 但仍需根据应用程序的需要配置一些设置。 ASE 中配置了 Azure 防火墙的应用程序存在两个问题。  

- 必须将应用程序依赖项添加到 Azure 防火墙或路由表。 
- 必须为应用程序流量创建路由，以避免非对称路由问题

如果应用程序有依赖项，则需要将这些依赖项添加到 Azure 防火墙。 创建允许 HTTP/HTTPS 流量的应用程序规则，并针对其他方面的控制创建网络规则。 

如果你知道应用程序请求流量将来自哪个地址范围，则可将该范围添加到要分配给 ASE 子网的路由表。 如果地址范围很大或未指定，则你可以使用应用程序网关等网络设备来提供一个要添加到路由表的地址。 有关在 ILB ASE 中配置应用程序网关的详细信息，请阅读[将 ILB ASE 与应用程序网关集成](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

![使用 Azure 防火墙的 ASE 连接流][5]

使用应用程序网关只是系统配置方法的一个例子。 如果遵循此路径，则需要将路由添加到 ASE 子网路由表，以便发送到应用程序网关的回复流量直接通过该路由传送。 

## <a name="logging"></a>日志记录 

Azure 防火墙可将日志发送到 Azure 存储、事件中心或 Azure Monitor 日志。 若要将应用与支持的任何目标相集成，请转到 Azure 防火墙门户 >“诊断日志”，并为所需目标启用日志。 与 Azure Monitor 日志集成后，可以查看已发送到 Azure 防火墙的任何流量的日志记录。 若要查看被拒绝的流量，请打开 Log Analytics 工作区门户 >“日志”，并输入如下所示的查询 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
首次运行应用程序时，如果不知道所有的应用程序依赖项，则将 Azure 防火墙与 Azure Monitor 日志集成会很有用。 可以通过[在 Azure Monitor 中分析日志数据](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)详细了解 Azure Monitor 日志
 
## <a name="dependencies"></a>依赖项

仅当所要配置的防火墙设备不是 Azure 防火墙时，才需要以下信息。 

- 应在支持服务终结点的服务中配置服务终结点。
- IP 地址依赖项适用于非 HTTP/S 流量（TCP 和 UDP 流量）
- 可将 FQDN HTTP/HTTPS 终结点放在防火墙设备中。
- 通配符 HTTP/HTTPS 终结点是可以根据许多限定符随 ASE 一起变化的依赖项。 
- 仅当要在 ASE 中部署 Linux 应用时，才需要考虑 Linux 依赖项。 如果不将 Linux 应用部署到 ASE，则不需要将这些地址添加到防火墙。 


#### <a name="service-endpoint-capable-dependencies"></a>支持服务终结点的依赖项 

| 终结点 |
|----------|
| Azure SQL |
| Azure 存储 |
| Azure 事件中心 |

#### <a name="ip-address-dependencies"></a>IP 地址依赖项

| 终结点 | 详细信息 |
|----------| ----- |
| \*:123 | NTP 时钟检查。 在端口 123 上的多个终结点中检查流量 |
| \*:12000 | 此端口用于某些系统监视活动。 如果阻止此端口，则有些问题将难以诊断，但 ASE 会继续运行 |

使用 Azure 防火墙时，将使用 FQDN 标记自动配置以下所有设置。 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 依赖项 

| 终结点 |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80   |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>通配符 HTTP/HTTPS 依赖项 

| 终结点 |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
|grmdsprod\*mini\*.servicebus.windows.net:443 |
|grmdsprod\*lini\*.servicebus.windows.net:443 |
|grsecprod\*mini\*.servicebus.windows.net:443 |
|grsecprod\*lini\*.servicebus.windows.net:443 |
|graudprod\*mini\*.servicebus.windows.net:443 |
|graudprod\*lini\*.servicebus.windows.net:443 |

#### <a name="linux-dependencies"></a>Linux 依赖项 

| 终结点 |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
