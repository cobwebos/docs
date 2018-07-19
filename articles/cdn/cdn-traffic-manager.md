---
title: 使用 Azure 流量管理器跨多个 Azure CDN 终结点设置故障转移 | Microsoft Docs
description: 了解如何使用 Azure 流量管理器对 Azure CDN 终结点进行设置。
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: b52cad1f32cc3d16cf70bb81640dcb1d9f8614bf
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133262"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>使用 Azure 流量管理器跨多个 Azure CDN 终结点设置故障转移

配置 Azure 内容分发网络 (CDN) 时，可根据需要选择最佳的提供程序和定价层。 Azure CDN 采用全局分布式基础结构，默认会创建本地和地理冗余以及全局负载均衡，以提高服务可用性和性能。 如果某个位置不可用于提供内容，则会将请求自动路由到另一个位置，并使用最佳的 POP（基于请求位置和服务器负载等因素）为每个客户端请求提供服务。 
 
如果你有多个 CDN 配置文件，可以使用 Azure 流量管理器进一步提高可用性和性能。 可以结合 Azure CDN 使用 Azure 流量管理器在多个 CDN 终结点之间进行负载均衡，以实现故障转移、异地负载均衡和其他方案。 在典型的故障转移方案中，所有客户端请求首先会定向到主要 CDN 配置文件；如果该配置文件不可用，则请求会不断传递到辅助 CDN 配置文件，直到主要 CDN 配置文件重新联机为止。 以这种方式使用 Azure 流量管理器可确保 Web 应用程序始终可用。 

本文提供了有关使用 **Verizon 的 Azure CDN Standard** 和 **Akamai 的 Azure CDN Standard** 配置文件设置故障转移的指导和示例。

## <a name="set-up-azure-cdn"></a>设置 Azure CDN 
使用不同的提供程序创建两个或更多个 Azure CDN 配置文件与终结点。

1. 遵循[创建新 CDN 配置文件](cdn-create-new-endpoint.md#create-a-new-cdn-profile)中的步骤，创建 **Verizon 的 Azure CDN Standard** 和 **Akamai 的 Azure CDN Standard** 配置文件。
 
   ![CDN 的多个配置文件](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. 在每个新配置文件中，遵循[创建新 CDN 终结点](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)中的步骤至少创建一个终结点。

## <a name="set-up-azure-traffic-manager"></a>设置 Azure 流量管理器
创建 Azure 流量管理器配置文件，并跨 CDN 终结点设置负载均衡。 

1. 遵循[创建流量管理器配置文件](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#create-a-traffic-manager-profile-1)中的步骤创建 Azure 流量管理器配置文件。 

    对于“路由方法”，请选择“优先级”。

2. 遵循[添加流量管理器终结点](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)中的步骤，在流量管理器配置文件中添加 CDN 终结点

    对于“类型”，请选择“外部终结点”。 对于“优先级”，请输入一个数字。

    例如，创建优先级为 *1* 的 *cdndemo101akamai.azureedge.net*，以及优先级为 *2* 的 *cdndemo101verizon.azureedge.net*。

   ![CDN 的流量管理器终结点](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>在 Azure CDN 和 Azure 流量管理器中设置自定义域
设置 CDN 和流量管理器配置文件后，遵循以下步骤在 CDN 终结点中添加 DNS 映射并注册自定义域。 在此示例中，自定义域名为 *cdndemo101.dustydogpetcare.online*。

1. 转到自定义域提供商（例如 GoDaddy）的网站，并创建两个 DNS CNAME 条目。 

    a. 对于第一个 CNAME 条目，请将具有 cdnverify 子域的自定义域映射到 CDN 终结点。 将自定义域注册到在步骤 2 中添加到流量管理器的 CDN 终结点时，需要使用此条目。

      例如： 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. 对于第二个 CNAME 条目，请将没有 cdnverify 子域的自定义域映射到 CDN 终结点。 此条目将自定义域映射到流量管理器。 

      例如： 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > 如果你的域当前已上线且不能中断，请在最后执行此步骤。 在将自定义域 DNS 更新到流量管理器之前，请验证 CDN 终结点和流量管理器域是否已上线。
    >


2.  在 Azure CDN 配置文件中，选择第一个 CDN 终结点 (Akamai)。 选择“添加自定义域”并输入 *cdndemo101akamai.azureedge.net*。 确认表示验证自定义域的复选标记是否显示为绿色。 

    Azure CDN 使用 *cdnverify* 子域来验证 DNS 映射，以完成此注册过程。 有关详细信息，请参阅[创建 CNAME DNS 记录](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)。 此步骤使 Azure CDN 能够识别自定义域，以便对其请求做出响应。

3.  返回自定义域提供商的网站，并更新创建的第一个 DNS 映射，以便将自定义域映射到第二个 CDN 终结点。
                             
    例如： 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. 在 Azure CDN 配置文件中，选择第二个 CDN 终结点 (Verizon) 并重复步骤 2。 选择“添加自定义域”并输入 *cdndemo101akamai.azureedge.net*。
 
完成这些步骤后，即已使用 Azure 流量管理器设置了具有故障转移功能的多 CDN 服务。 你可以从自定义域访问测试 URL。 若要测试功能，请禁用主要 CDN 终结点，并验证是否能够正确地将请求转移到辅助 CDN 终结点。 

## <a name="next-steps"></a>后续步骤
还可以设置其他路由方法（例如地理路由），以便在不同的 CDN 终结点之间进行负载均衡。 有关详细信息，请参阅[使用流量管理器配置地理流量路由方法](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)。



