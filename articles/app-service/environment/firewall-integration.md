---
title: 锁定出站流量
description: 了解如何与 Azure 防火墙集成，以保护应用服务环境中的出站流量。
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 03/31/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4c25c64268b38e5929c73891f7c48e79b9b8593e
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856045"
---
# <a name="locking-down-an-app-service-environment"></a>锁定应用服务环境

应用服务环境 (ASE) 需要访问许多的外部依赖项才能正常运行。 ASE 驻留在客户的 Azure 虚拟网络 (VNet) 中。 客户必须允许 ASE 依赖项流量，对于想要锁定从 VNet 传出的所有流量的客户而言，这是一个问题。

有许多用于管理 ASE 的入站终结点。 无法通过防火墙设备发送入站管理流量。 此流量的源地址是已知的，并已在[应用服务环境管理地址](https://docs.microsoft.com/azure/app-service/environment/management-addresses)文档中发布。 还有一个名为 AppServiceManagement 的服务标记，可以与网络安全组 (NSG) 一起使用来保护入站流量。

ASE 出站依赖项几乎完全是使用 FQDN 定义的，不附带任何静态地址。 缺少静态地址意味着无法使用网络安全组锁定来自 ASE 的出站流量。 地址会频率更改，用户无法基于当前解析设置规则，然后使用这些规则来创建 NSG。 

保护出站地址的解决方案在于使用可基于域名控制出站流量的防火墙设备。 Azure 防火墙可以根据目标的 FQDN 限制出站 HTTP 和 HTTPS 流量。  

## <a name="system-architecture"></a>系统体系结构

部署出站流量通过防火墙设备的 ASE 需要更改 ASE 子网中的路由。 路由在 IP 级别运行。 如果在定义路由时出了差错，可以强制将 TCP 回复流量从另一个地址发送到源。 如果回复地址不同于流量发送到的地址，则会出现所谓“非对称路由”的问题，这会中断 TCP。

必须定义路由，以便发往 ASE 的入站流量能够以传入流量的相同方式做出回复。 必须为入站管理请求和入站应用程序请求定义路由。

传入和传出 ASE 的流量必须遵守以下约定

* 发往 Azure SQL、存储和事件中心的流量不是使用防火墙设备支持的。 此流量必须直接发送到这些服务。 实现此目的的方法是为这三个服务配置服务终结点。 
* 必须定义路由表规则，用于从入站管理流量的来源位置发回这些流量。
* 必须定义路由表规则，用于从入站应用程序流量的来源位置发回这些流量。 
* 可以使用路由表规则将离开 ASE 的所有其他流量发送到防火墙设备。

![使用 Azure 防火墙的 ASE 连接流][5]

## <a name="locking-down-inbound-management-traffic"></a>锁定入站管理流量

如果尚未为 ASE 子网分配 NSG，请创建一个。 在 NSG 中，将第一个规则设为允许来自端口 454、455 上名为 AppServiceManagement 的服务标记的流量。 若要从公共 IP 管理 ASE，只需设置允许从 AppServiceManagement 标记进行访问的规则。 位于该服务标记后面的地址仅用来管理 Azure 应用服务。 流经这些连接的管理流量由身份验证证书提供加密和保护。 此通道上的典型流量包括客户发起的命令和运行状况探测等等。 

通过门户创建的带有新子网的 ASE 在创建时带有一个 NSG，其中包含 AppServiceManagement 标记的允许规则。  

ASE 还必须允许端口 16001 上来自负载均衡器标记的入站请求。 端口 16001 上来自负载均衡器的请求在负载均衡器和 ASE 前端之间进行保持连接检查。 如果端口 16001 被阻止，则 ASE 将不正常。

## <a name="configuring-azure-firewall-with-your-ase"></a>在 ASE 中配置 Azure 防火墙 

使用 Azure 防火墙锁定现有 ASE 的传出流量的步骤如下：

1. 为 ASE 子网中的 SQL、存储和事件中心启用服务终结点。 若要启用服务终结点，请转到网络门户并选择子网，然后从服务终结点下拉列表中选择“Microsoft.EventHub”、“Microsoft.SQL”和“Microsoft.Storage”。 为 Azure SQL 启用服务终结点后，还必须为应用的所有 Azure SQL 依赖项配置服务终结点。 

   ![选择服务终结点][2]
  
1. 在 ASE 所在的 VNet 中创建名为 AzureFirewallSubnet 的子网。 遵循 [Azure 防火墙文档](https://docs.microsoft.com/azure/firewall/)中的指导创建 Azure 防火墙。

1. 在 Azure 防火墙 UI >“规则”>“应用程序规则集合”中，选择“添加应用程序规则集合”。 提供名称、优先级，并设置“允许”。 在“FQDN 标记”部分提供名称，将源地址设置为 *，然后选择“应用服务环境 FQDN 标记”和“Windows 更新”。 
   
   ![添加应用程序规则][1]
   
1. 在 Azure 防火墙 UI >“规则”>“网络规则集合”中，选择“添加网络规则集合”。 提供名称、优先级，并设置“允许”。 在“规则”部分中，在 IP 地址下，提供一个名称，选择“任何”作为协议，将源和目标地址设置为 *，将端口设置为 123。  此规则允许系统使用 NTP 执行时钟同步。 以相同的方式针对端口 12000 创建另一个规则，以帮助诊断任何系统问题。 

   ![添加 NTP 网络规则][3]
   
1. 在 Azure 防火墙 UI >“规则”>“网络规则集合”中，选择“添加网络规则集合”。 提供名称、优先级，并设置“允许”。 在“规则”部分中，在“服务标记”下，提供一个名称，选择“任何”  作为协议，将 * 设置为源地址，选择服务标记 AzureMonitor，然后将端口设置为 80、443。 此规则允许系统向 Azure Monitor 提供运行状况和指标信息。

   ![添加 NTP 服务标记网络规则][6]
   
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

使用应用程序网关只是系统配置方法的一个例子。 如果遵循此路径，则需要将路由添加到 ASE 子网路由表，以便发送到应用程序网关的回复流量直接通过该路由传送。 

## <a name="logging"></a>日志记录 

Azure 防火墙可将日志发送到 Azure 存储、事件中心或 Azure Monitor 日志。 若要将应用与支持的任何目标相集成，请转到 Azure 防火墙门户 >“诊断日志”，并为所需目标启用日志。 与 Azure Monitor 日志集成后，可以查看已发送到 Azure 防火墙的任何流量的日志记录。 若要查看被拒绝的流量，请打开 Log Analytics 工作区门户 >“日志”，并输入如下所示的查询 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
首次运行应用程序时，如果不知道所有的应用程序依赖项，则将 Azure 防火墙与 Azure Monitor 日志集成会很有用。 可以通过[在 Azure Monitor 中分析日志数据](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)详细了解 Azure Monitor 日志。
 
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
| 40.77.24.27:80 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 40.77.24.27:443 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 13.90.249.229:80 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 13.90.249.229:443 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 104.45.230.69:80 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 104.45.230.69:443 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 13.82.184.151:80 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 13.82.184.151:443 | 监视 ASE 问题和发出相关警报时需要此端口 |

使用 Azure 防火墙时，将使用 FQDN 标记自动配置以下所有设置。 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 依赖项 

| 终结点 |
|----------|
|graph.microsoft.com:443 |
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
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|prod.microsoftmetrics.com:443 |
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
|prod.warmpath.msftcloudes.com:443 |
|prod.warmpath.msftcloudes.com:80 |
|gcs.prod.monitoring.core.windows.net:80|
|gcs.prod.monitoring.core.windows.net:443|
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 | 
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|settings-win.data.microsoft.com:443 |
|maupdateaccount2.blob.core.windows.net:443 |
|maupdateaccount3.blob.core.windows.net:443 |
|dc.services.visualstudio.com:443 |
|gmstorageprodsn1.blob.core.windows.net:443 |
|gmstorageprodsn1.file.core.windows.net:443 |
|gmstorageprodsn1.queue.core.windows.net:443 |
|gmstorageprodsn1.table.core.windows.net:443 |
|rteventservice.trafficmanager.net:443 |
|ctldl.windowsupdate.com:80 |
|ctldl.windowsupdate.com:443 |

#### <a name="wildcard-httphttps-dependencies"></a>通配符 HTTP/HTTPS 依赖项 

| 终结点 |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |
| \*。 identity.azure.net:443 |
| \*.ctldl.windowsupdate.com:80 |
| \*。 ctldl.windowsupdate.com:443 |

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
| \*。 cdn.mscr.io:443 |
|mcr.microsoft.com:443 |
|packages.fluentbit.io:80 |
|packages.fluentbit.io:443 |
|apt-mo.trafficmanager.net:80 |
|apt-mo.trafficmanager.net:443 |
|azure.archive.ubuntu.com:80 |
|azure.archive.ubuntu.com:443 |
|changelogs.ubuntu.com:80 |
|13.74.252.37:11371 |
|13.75.127.55:11371 |
|13.76.190.189:11371 |
|13.80.10.205:11371 |
|13.91.48.226:11371 |
|40.76.35.62:11371 |
|104.215.95.108:11371 |

## <a name="us-gov-dependencies"></a>US Gov 依赖项

对于 US Gov 区域中的 Ase，请按照本文档中的[使用 Ase 配置 Azure 防火墙](https://docs.microsoft.com/azure/app-service/environment/firewall-integration#configuring-azure-firewall-with-your-ase)部分中的说明，使用 Ase 配置 azure 防火墙。

如果要在 US Gov 中使用 Azure 防火墙以外的设备 

* 应在支持服务终结点的服务中配置服务终结点。
* 可将 FQDN HTTP/HTTPS 终结点放在防火墙设备中。
* 通配符 HTTP/HTTPS 终结点是可以根据许多限定符随 ASE 一起变化的依赖项。

Linux 在 US Gov 区域中不可用，因此不作为可选配置列出。

#### <a name="service-endpoint-capable-dependencies"></a>支持服务终结点的依赖项 ####

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
| 40.77.24.27:80 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 40.77.24.27:443 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 13.90.249.229:80 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 13.90.249.229:443 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 104.45.230.69:80 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 104.45.230.69:443 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 13.82.184.151:80 | 监视 ASE 问题和发出相关警报时需要此端口 |
| 13.82.184.151:443 | 监视 ASE 问题和发出相关警报时需要此端口 |

#### <a name="dependencies"></a>依赖关系 ####

| 终结点 |
|----------|
| \*.ctldl.windowsupdate.com:80 |
| \*.management.usgovcloudapi.net:80 |
| \*.update.microsoft.com:80 |
|admin.core.usgovcloudapi.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azperfmerges.blob.core.windows.net:80 |
|azprofileruploads1.blob.core.windows.net:80 |
|azprofileruploads10.blob.core.windows.net:80 |
|azprofileruploads2.blob.core.windows.net:80 |
|azprofileruploads3.blob.core.windows.net:80 |
|azprofileruploads4.blob.core.windows.net:80 |
|azprofileruploads5.blob.core.windows.net:80 |
|azprofileruploads6.blob.core.windows.net:80 |
|azprofileruploads7.blob.core.windows.net:80 |
|azprofileruploads8.blob.core.windows.net:80 |
|azprofileruploads9.blob.core.windows.net:80 |
|azureprofilerfrontdoor.cloudapp.net:80 |
|azurewatsonanalysis.usgovcloudapp.net:80 |
|cacerts.digicert.com:80 |
|client.wns.windows.com:80 |
|crl.microsoft.com:80 |
|crl.verisign.com:80 |
|crl3.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|ctldl.windowsupdate.com:80 |
|definitionupdates.microsoft.com:80 |
|download.windowsupdate.com:80 |
|fairfax.warmpath.usgovcloudapi.net:80 |
|flighting.cp.wd.microsoft.com:80 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:80 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:80 |
|global.metrics.nsatc.net:80 |
|go.microsoft.com:80 |
|gr-gcws-prod-bd3.usgovcloudapp.net:80 |
|gr-gcws-prod-bn1.usgovcloudapp.net:80 |
|gr-gcws-prod-dd3.usgovcloudapp.net:80 |
|gr-gcws-prod-dm2.usgovcloudapp.net:80 |
|gr-gcws-prod-phx20.usgovcloudapp.net:80 |
|gr-gcws-prod-sn5.usgovcloudapp.net:80 |
|login.live.com:80 |
|login.microsoftonline.us:80 |
|management.core.usgovcloudapi.net:80 |
|management.usgovcloudapi.net:80 |
|maupdateaccountff.blob.core.usgovcloudapi.net:80 |
|mscrl.microsoft.com
|ocsp.digicert.0 |
|ocsp.msocsp.co|
|ocsp.verisign.0 |
|rteventse.trafficmanager.net:80 |
|settings-n.data.microsoft.com:80 |
|shavamafestcdnprod1.azureedge.net:80 |
|shavanifestcdnprod1.azureedge.net:80 |
|v10ortex-win.data.microsoft.com:80 |
|wp.microsoft.com:80 |
|dcpalt.microsoft.com:80 |
|www.microsoft.com:80 |
|www.msftconnecttest.com:80 |
|www.thawte.com:80 |
|\*ctldl.windowsupdate.com:443 |
|\*.management.usgovcloudapi.net:443 |
|\*.update.microsoft.com:443 |
|admin.core.usgovcloudapi.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azperfmerges.blob.core.windows.net:443 |
|azprofileruploads1.blob.core.windows.net:443 |
|azprofileruploads10.blob.core.windows.net:443 |
|azprofileruploads2.blob.core.windows.net:443 |
|azprofileruploads3.blob.core.windows.net:443 |
|azprofileruploads4.blob.core.windows.net:443 |
|azprofileruploads5.blob.core.windows.net:443 |
|azprofileruploads6.blob.core.windows.net:443 |
|azprofileruploads7.blob.core.windows.net:443 |
|azprofileruploads8.blob.core.windows.net:443 |
|azprofileruploads9.blob.core.windows.net:443 |
|azureprofilerfrontdoor.cloudapp.net:443 |
|azurewatsonanalysis.usgovcloudapp.net:443 |
|cacerts.digicert.com:443 |
|client.wns.windows.com:443 |
|crl.microsoft.com:443 |
|crl.verisign.com:443 |
|crl3.digicert.com:443 |
|csc3-2009-2.crl.verisign.com:443 |
|ctldl.windowsupdate.com:443 |
|definitionupdates.microsoft.com:443 |
|download.windowsupdate.com:443 |
|fairfax.warmpath.usgovcloudapi.net:443 |
|flighting.cp.wd.microsoft.com:443 |
|gcwsprodgmdm2billing.queue.core.usgovcloudapi.net:443 |
|gcwsprodgmdm2billing.table.core.usgovcloudapi.net:443 |
|global.metrics.nsatc.net:443 |
|go.microsoft.com:443 |
|gr-gcws-prod-bd3.usgovcloudapp.net:443 |
|gr-gcws-prod-bn1.usgovcloudapp.net:443 |
|gr-gcws-prod-dd3.usgovcloudapp.net:443 |
|gr-gcws-prod-dm2.usgovcloudapp.net:443 |
|gr-gcws-prod-phx20.usgovcloudapp.net:443 |
|gr-gcws-prod-sn5.usgovcloudapp.net:443 |
|login.live.com:443 |
|login.microsoftonline.us:443 |
|management.core.usgovcloudapi.net:443 |
|management.usgovcloudapi.net:443 |
|maupdateaccountff.blob.core.usgovcloudapi.net:443 |
|mscrl.microsoft.com:443 |
|ocsp.digicert.com:443 |
|ocsp.msocsp.com:443 |
|ocsp.verisign.com:443 |
|rteventservice.trafficmanager.net:443 |
|settings-win.data.microsoft.com:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|shavamanifestcdnprod1.azureedge.net:443 |
|v10.vortex-win.data.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|www.microsoft.com:443 |
|www.msftconnecttest.com:443 |
|www.thawte.com:443 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
[6]: ./media/firewall-integration/firewall-ntprule-monitor.png
