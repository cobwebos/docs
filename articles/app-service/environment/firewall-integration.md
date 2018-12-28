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
ms.date: 09/24/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 52051ea221a3d49d86cc6b95e020e1075ce8cba2
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275544"
---
# <a name="locking-down-an-app-service-environment"></a>锁定应用服务环境

应用服务环境 (ASE) 需要访问许多的外部依赖项才能正常运行。 ASE 驻留在客户的 Azure 虚拟网络 (VNet) 中。 客户必须允许 ASE 依赖项流量，对于想要锁定从 VNet 传出的所有流量的客户而言，这是一个问题。

ASE 有许多入站依赖项。 无法通过防火墙设备发送入站管理流量。 此流量的源地址是已知的，并已在[应用服务环境管理地址](https://docs.microsoft.com/azure/app-service/environment/management-addresses)文档中发布。 可以使用该信息创建网络安全组规则来保护入站流量。

ASE 出站依赖项几乎完全是使用 FQDN 定义的，不附带任何静态地址。 缺少静态地址意味着无法使用网络安全组 (NSG) 锁定来自 ASE 的出站流量。 地址会频率更改，用户无法基于当前解析设置规则，然后使用这些规则来创建 NSG。 

保护出站地址的解决方案在于使用可基于域名控制出站流量的防火墙设备。 Azure 防火墙可以根据目标的 FQDN 限制出站 HTTP 和 HTTPS 流量。  

## <a name="configuring-azure-firewall-with-your-ase"></a>在 ASE 中配置 Azure 防火墙 

使用 Azure 防火墙锁定 ASE 传出流量的步骤如下：

1. 在 ASE 所在的或将要放入到的 VNet 中创建 Azure 防火墙。 [Azure 防火墙文档](https://docs.microsoft.com/azure/firewall/)
2. 在 Azure 防火墙 UI 中，选择“应用服务环境 FQDN 标记”
3. 使用[应用服务环境管理地址]( https://docs.microsoft.com/azure/app-service/environment/management-addresses)中的管理地址创建一个路由表并添加 Internet 的下一跃点。 需要使用路由表条目来避免非对称路由问题。
4. 在 IP 地址依赖项中为下面所示的 IP 地址依赖项添加路由，并添加 Internet 的下一跃点。
5. 将路由添加到 0.0.0.0/0 的路由表并将 Azure 防火墙作为下一跃点。
6. 为 Azure SQL 和 Azure 存储的 ASE 子网创建服务终结点。
7. 将创建的路由表分配到 ASE 子网。

## <a name="application-traffic"></a>应用程序流量 

完成上述步骤后，ASE 可以正常运行。 但仍需根据应用程序的需要配置一些设置。 ASE 中配置了 Azure 防火墙的应用程序存在两个问题。  

- 必须将应用程序依赖项 FQDN 添加到 Azure 防火墙或路由表
- 必须为流量的来源地址创建路由，以避免非对称路由问题

如果应用程序有依赖项，则需要将这些依赖项添加到 Azure 防火墙。 创建允许 HTTP/HTTPS 流量的应用程序规则，并针对其他方面的控制创建网络规则。 

如果你知道应用程序请求流量将来自哪个地址范围，则可将该范围添加到要分配给 ASE 子网的路由表。 如果地址范围很大或未指定，则你可以使用应用程序网关等网络设备来提供一个要添加到路由表的地址。 有关在 ILB ASE 中配置应用程序网关的详细信息，请阅读[将 ILB ASE 与应用程序网关集成](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)


## <a name="dependencies"></a>依赖项

Azure 应用服务有许多的外部依赖项。 这些依赖项可按类别划分为多个主要方面：

- 若要锁定出站网络流量，应为支持服务终结点的服务设置服务终结点。
- IP 地址终结点不是使用域名寻址的。 对于预期所有 HTTPS 流量使用域名的防火墙设备而言，这可能是一个问题。 应将 IP 地址终结点添加到 ASE 子网中设置的路由表。
- 可将 FQDN HTTP/HTTPS 终结点放在防火墙设备中。
- 通配符 HTTP/HTTPS 终结点是可以根据许多限定符随 ASE 一起变化的依赖项。 
- 仅当要在 ASE 中部署 Linux 应用时，才需要考虑 Linux 依赖项。 如果不将 Linux 应用部署到 ASE，则不需要将这些地址添加到防火墙。 


#### <a name="service-endpoint-capable-dependencies"></a>支持服务终结点的依赖项 

| 终结点 |
|----------|
| Azure SQL |
| Azure 存储 |
| Azure KeyVault |


#### <a name="ip-address-dependencies"></a>IP 地址依赖项 

| 终结点 |
|----------|
| 40.77.24.27:443 |
| 13.82.184.151:443 |
| 13.68.109.212:443 |
| 13.90.249.229:443 |
| 13.91.102.27:443 |
| 104.45.230.69:443 |
| 168.62.226.198:12000 |


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

