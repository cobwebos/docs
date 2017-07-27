---
title: "Azure 流量管理器 - 常见问题解答 | Microsoft Docs"
description: "本文提供有关流量管理器的常见问题解答"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 44762864e0a5adf568fcd4928b48661196f05b9e
ms.contentlocale: zh-cn
ms.lasthandoff: 06/16/2017

---

# <a name="traffic-manager-frequently-asked-questions-faq"></a>流量管理器常见问题解答 (FAQ)

## <a name="traffic-manager-basics"></a>流量管理器基础知识

### <a name="what-ip-address-does-traffic-manager-use"></a>流量管理器使用什么 IP 地址？

如[流量管理器工作原理](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)中所述，流量管理器在 DNS 级别工作。 它会发送 DNS 响应，将客户端定向到相应的服务终结点。 然后，客户端直接连接到服务终结点，不通过流量管理器进行连接。

因此，流量管理器不提供供客户端连接的终结点或 IP 地址。 因此，如果想要为服务使用静态 IP 地址，则必须在服务而不是流量管理器中配置该地址。

### <a name="does-traffic-manager-support-sticky-sessions"></a>流量管理器是否支持“粘滞”会话？

如[流量管理器工作原理](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)中所述，流量管理器在 DNS 级别工作。 它使用 DNS 响应将客户端引导到相应的服务终结点。 客户端直接连接到服务终结点，而不是通过流量管理器连接。 因此，流量管理器看不到客户端与服务器之间的 HTTP 流量。

此外，流量管理器收到的 DNS 查询的源 IP 地址属于递归 DNS 服务而不是客户端。 因此，流量管理器无法跟踪单个客户端，也无法实现“粘滞”会话。 这种限制在所有基于 DNS 的流量管理系统中很常见，并不是流量管理器特有的限制。

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>使用流量管理器时为何出现 HTTP 错误？

如[流量管理器工作原理](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)中所述，流量管理器在 DNS 级别工作。 它使用 DNS 响应将客户端引导到相应的服务终结点。 然后，客户端直接连接到服务终结点，不通过流量管理器进行连接。 流量管理器看不到客户端与服务器之间的 HTTP 流量。 因此，出现的任何 HTTP 错误必定来自应用程序。 要使客户端连接到应用程序，必须完成所有 DNS 解析步骤。 这包括流量管理器对应用程序流量流所做的任何交互。

因此，进一步的调查应着重于应用程序。

问题的最常见原因源自客户端浏览器发送的 HTTP 主机标头。 请确保将应用程序配置为接受所要使用的域名的正确主机标头。 对于使用 Azure 应用服务的终结点，请参阅[使用流量管理器为 Azure 应用服务中的 Web 应用配置自定义域名](../app-service-web/web-sites-traffic-manager-custom-domain-name.md)。

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>使用流量管理器对性能有什么影响？

如[流量管理器工作原理](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)中所述，流量管理器在 DNS 级别工作。 由于客户端直接连接到你的服务终结点，因此在使用流量管理器时，一旦建立连接就没有性能影响。

由于流量管理器在 DNS 级别与应用程序集成，因此需要将额外的 DNS 查找插入 DNS 解析链中。 流量管理器对 DNS 解析时间的影响微乎其微。 流量管理器使用全局性的名称服务器网络，并使用[任播](https://en.wikipedia.org/wiki/Anycast)网络来确保始终将 DNS 查询路由到最靠近的可用名称服务器。 此外，对 DNS 响应进行缓存意味着，因使用流量管理器而导致的额外的 DNS 延迟仅出现在部分会话中。

“性能”方法将流量路由到最靠近的可用终结点。 最终结果是，与此方法相关的整体性能影响将会减到最小。 通过减小与终结点之间的网络延迟，应该可以抵消 DNS 延迟增大造成的影响。

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>流量管理器允许使用什么应用程序协议？

如[流量管理器工作原理](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)中所述，流量管理器在 DNS 级别工作。 完成 DNS 查找以后，客户端会直接连接到应用程序终结点，不通过流量管理器进行连接。 因此，连接可以使用任何应用程序协议。 如果选择 TCP 作为监视协议，则无需使用任何应用程序协议，就可以完成对流量管理器终结点运行状况的监视。 如果选择使用应用程序协议来验证运行状况，则要求终结点能够响应 HTTP 或 HTTPS GET 请求。

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>是否可以对“裸”域名使用流量管理器？

不会。 DNS 标准不允许 CNAME 与其他同名的 DNS 记录共存。 DNS 区域的顶点（或根）始终包含两条预先存在的 DNS 记录：SOA 和权威 NS 记录。 这意味着在不违反 DNS 标准的情况下，无法在区域顶点位置创建 CNAME 记录。

流量管理器需要使用一条 DNS CNAME 记录来映射虚构 DNS 名称。 例如，将 www.contoso.com 映射到流量管理器配置文件 DNS 名称 contoso.trafficmanager.net。 此外，流量管理器配置文件还会返回另一条 DNS CNAME 来指示客户端应连接到的终结点。

若要解决此问题，我们建议使用 HTTP 重定向将流量从裸域名定向到不同的 URL，然后即可使用流量管理器。 例如，裸域“contoso.com”可将用户重定向到指向流量管理器 DNS 名称的 CNAME“www.contoso.com”。

在流量管理器中实现对裸域的完全支持已列入我们的待开发功能中。 请[在我们的社区反馈站点上投票](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly)，表达对此项功能的支持。

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>处理 DNS 查询时流量管理器是否会考虑客户端子网地址？ 
不会；此时流量管理器仅考虑其接收的 DNS 查询的源 IP 地址，该地址通常是查找地理路由方法和性能路由方法时 DNS 解析器的 IP 地址。  
具体而言，[RFC 7871 – DNS 查询中的客户端子网](https://tools.ietf.org/html/rfc7871)可提供[ DNS (EDNS0) 的扩展机制](https://tools.ietf.org/html/rfc2671)，将客户端子网地址从支持该机制的解析器传递到 DNS 服务器，但流量管理器目前不支持该客户端子网。 通过[社区反馈站点](https://feedback.azure.com/forums/217313-networking)可以表达对此项功能的支持。


### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>什么是 DNS TTL，它如何影响我的用户？

当 DNS 查询到达流量管理器时，它将在响应中设置一个名为生存时间 (TTL) 的值。 这个值以秒为单位，向下游 DNS 解析器指示缓存此响应的时间。 尽管 DNS 解析器不保证缓存此结果，但通过缓存，DNS 解析器可对任何离开缓存的后续查询做出响应，而无需这些查询进入流量管理器 DNS 服务器。 这会对响应产生如下影响：
- TTL 越高，到达流量管理器 DNS 服务器的查询越少，而这又可降低客户成本，因为会按使用了服务的查询数进行计费。
- 较高的 TTL 还可能降低执行 DNS 查找所需的时间。
- 较高的 TTL 还意味着数据不会反应最新的运行状况信息，流量管理器已通过其探测代理获取了该信息。

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>可如何设置流量管理器响应的 TTL 范围？

在每个配置文件级别，可将 DNS TTL 设置为最低 0 秒，最高 2,147,483,647 秒（最大范围符合 [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )）。 TTL 为 0 表示下游 DNS 解析器不缓存查询响应，所有查询到应进入流量管理器 DNS 服务器进行解析。

## <a name="traffic-manager-geographic-traffic-routing-method"></a>流量管理器的“地理”流量路由方法

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>可以在哪些情况下使用地理路由？ 
只要 Azure 客户需要根据地理区域辨识其用户，即可使用地理路由类型。 例如，通过使用地理流量路由方法可为特定区域的用户提供不同于其他区域的用户体验。 又比如，根据本地数据自主性的规定，只能由同一区域的终结点为用户提供数据。

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>进行地理路由时，流量管理器支持哪些区域？ 
可在[此处](traffic-manager-geographic-regions.md)查找流量管理器使用的国家/地区层次结构。 更改会在此页进行更新，但也可以通过 [Azure 流量管理器 REST API](https://docs.microsoft.com/rest/api/trafficmanager/) 以编程方式检索相同的信息。 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>流量管理器如何确定用户从何处进行查询？ 
流量管理器会查看查询的源 IP（很可能是本地 DNS 解析器在代表用户执行查询），并使用内部 IP 通过区域映射的方式确定位置。 该映射会随时更新，以反映 Internet 中的变化。 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>是否可以保证流量管理器在每种情况下都可正确确定用户的确切地理位置？
不可以；流量管理器无法保证我们根据 DNS 查询的源 IP 地址推断出的地理区域始终对应用户的位置，原因如下： 

- 首先，如之前常见问题解答中所述，我们所见的源 IP 地址是代表用户执行查询的 DNS 解析器的 IP 地址。 虽然 DNS 解析器的地理位置很好地反映了用户的地理位置，但根据 DNS 解析器服务的足迹和用户选择使用的特定 DNS 解析器服务而有所不同。 举例来说，位于马来西亚的客户可能在其设备设置中指定使用一种 DNS 解析器服务，但可能会选取该服务在新加坡的 DNS 服务器来处理此用户/设备的查询解析。 这种情况下，流量管理器仅可显示对应于新加坡位置的解析器的 IP 地址。 另请参阅本页面上之前有关客户端子网地址支持的常见问题解答。

- 其次，流量管理器使用内部映射来执行 IP 地址到地理区域的转换。 尽管此映射会经过不断验证和更新来提高其准确性和阐释 Internet 的演变，但是我们的信息仍有可能不能确切反应所有 IP 地址的地理位置。


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>是否需将终结点与进行地理路由时用来进行配置的终结点置于同一区域？ 
否。终结点的位置不会限制可以向其映射哪些区域。 例如，可以将印度的所有用户定向到 US-Central Azure 区域的某个终结点。

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>是否可以将地理区域分配给某个配置文件中的终结点，而该配置文件尚未进行地理路由所需的配置？ 

可以；如果某个配置文件的路由方法不是地理路由，则可使用 [Azure 流量管理器 REST API](https://docs.microsoft.com/rest/api/trafficmanager/) 将地理区域分配给该配置文件中的终结点。 如果是非地理路由类型的配置文件，则会忽略该配置。 如果在以后将此类配置文件更改为地理路由类型，流量管理器会使用相应的映射。


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>为什么在尝试将现有配置文件的路由方法更改为地理路由时会出现错误？

使用地理路由时，配置文件中的所有终结点都至少需要将一个区域映射到其中。 若要将现有配置文件转换为地理路由类型，首先需使用 [Azure 流量管理器 REST API](https://docs.microsoft.com/rest/api/trafficmanager/) 将地理区域关联到所有终结点，然后再将路由类型更改为地理路由。 如果使用门户，请先删除终结点，将配置文件的路由方法更改为地理路由，然后再添加终结点及其地理区域映射。 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>为何强烈建议客户创建嵌套式配置文件，而不是将终结点直接置于启用了地理路由的配置文件中？ 

如果使用地理路由类型，则只能将一个区域分配给配置文件中的一个终结点。 如果该终结点不是附加了子配置文件的嵌套类型，则当该终结点不正常时，流量管理器仍会继续向其发送流量，因为不发送流量也不会有任何改善。 流量管理器不会故障转移到其他终结点，即使所分配的区域是分配给不正常终结点的区域的“父”区域（例如，如果终结点的“西班牙”区域不正常，不会故障转移到为其分配了“欧洲”区域的另一终结点）。 这样做是为了确保流量管理器遵守客户在其配置文件中设置的地理边界。 为了确保在某个终结点不正常时能够故障转移到其他终结点，建议为地理区域分配包含多个终结点（而不是单个终结点）的嵌套式配置文件。 这样一来，如果嵌套式子配置文件中的某个终结点故障，则可将流量故障转移到同一嵌套式子配置文件中的其他终结点。

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>对于支持此路由类型的 API 版本，是否存在任何限制？

是的。仅 API 2017-03-01 及更高版本支持地理路由类型。 不能使用旧的 API 版本创建地理路由类型的配置文件或向终结点分配地理区域。 如果使用旧的 API 版本从 Azure 订阅检索配置文件，则不会返回任何地理路由类型的配置文件。 另外，在使用旧的 API 版本时，如果返回的配置文件的终结点进行了地理区域分配，则不会显示该地理区域分配。



## <a name="traffic-manager-endpoints"></a>流量管理器终结点

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>能否将流量管理器用于多个订阅的终结点？

不能对 Azure Web 应用使用多个订阅中的终结点。 Web 应用要求其所用的任何自定义域名只能在单个订阅中使用。 无法对多个订阅中的 Web 应用使用同一个域名。

对于其他终结点类型，可在多个订阅中结合使用流量管理器和终结点。 在 Resource Manager 中，只要配置流量管理器配置文件的人员具有终结点的读取访问权限，任何订阅的终结点就都可添加到流量管理器中。 可使用 [Azure Resource Manager 基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 授予这些权限。


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>能否将流量管理器用于云服务的“过渡”槽？

是的。 可以在流量管理器中将云服务的“过渡”槽配置为“外部”终结点。 运行状况检查仍按 Azure 终结点费率计费。 由于使用的是“外部”终结点类型，系统不会自动拾取对基础服务所做的更改。 使用外部终结点时，流量管理器无法检测停止或删除云服务的时间。 因此，在禁用或删除终结点之前，流量管理器会一直对运行状况检查计费。

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>流量管理器是否支持 IPv6 终结点？

流量管理器当前不提供 IPv6 可寻址的名称服务器。 但是，IPv6 客户端仍可使用流量管理器连接到 IPv6 终结点。 客户端不会直接向流量管理器发出 DNS 请求， 而是使用递归 DNS 服务。 仅使用 IPv6 的客户端通过 IPv6 向递归 DNS 服务发送请求。 然后，该递归服务应该能够使用 IPv4 联系流量管理器名称服务器。

流量管理器使用终结点的 DNS 名称做出响应。 若要支持 IPv6 终结点，必须有一条可将终结点 DNS 名称指向 IPv6 地址的 DNS AAAA 记录。 流量管理器运行状况检查仅支持 IPv4 地址。 服务需要在相同的 DNS 名称中公开 IPv4 终结点。

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>流量管理器是否可用于同一区域中的多个 Web 应用？

通常情况下，流量管理器用于将流量引导到部署在不同区域中的应用程序。 不过，流量管理器也可用于一个应用程序在同一区域中存在多个部署的情形。 流量管理器 Azure 终结点不允许将同一 Azure 区域中的多个 Web 应用终结点添加到同一流量管理器配置文件。

##  <a name="traffic-manager-endpoint-monitoring"></a>流量管理器终结点监视

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>流量管理器能否灵活应对 Azure 区域故障？

流量管理器是在 Azure 中提供高可用性应用程序的关键组件。
若要提供高可用性，流量管理器必须具有超高的可用性，并且能够灵活应对区域故障。

在设计上，流量管理器组件能够灵活应对任何 Azure 区域的全面故障。 这样的应对能力见于所有流量管理器组件：DNS 名称服务器、API、存储层、终结点监视服务。

在整个 Azure 区域发生服务中断的情况下（这种情况很少见），流量管理器仍可继续正常运行。 在多个 Azure 区域中部署的应用程序可以依赖流量管理器将流量定向到这些区域中的可用应用程序实例。

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>选择资源组位置会如何影响流量管理器？

流量管理器属于单一的全局性服务， 而不是区域性服务。 选择资源组位置对部署在该资源组中的流量管理器配置文件没有影响。

Azure Resource Manager 要求所有资源组指定一个位置，这决定了部署在该资源组中的资源的默认位置。 创建流量管理器配置文件时，将在资源组中创建该配置文件。 所有流量管理器配置文件使用“**全局**”作为位置，覆盖资源组的默认值。

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>如何确定每个终结点的当前运行状况？

除了总体配置文件，每个终结点的当前监视状态也显示在 Azure 门户中。 此信息也可通过流量监视器 [REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx)、[PowerShell cmdlet](https://msdn.microsoft.com/library/mt125941.aspx) 和[跨平台 Azure CLI](../cli-install-nodejs.md) 获取。

Azure 不提供有关过去终结点运行状况的历史信息，也不提供在终结点运行状况发生变化时引发警报的功能。

### <a name="can-i-monitor-https-endpoints"></a>能否监视 HTTPS 终结点？

是的。 流量管理器支持通过 HTTPS 进行探测。 在监视配置中将 **HTTPS** 配置为协议。

流量管理器无法提供任何证书验证，包括：

* 不验证服务器端证书
* 不支持 SNI 服务器端证书
* 不支持客户端证书

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>如果应用程序不支持 HTTP 或 HTTPS，是否仍可使用流量管理器？

是的。 可以将 TCP 指定为监视协议，而流量管理器可发起一个 TCP 连接，并等待来自终结点的响应。 如果终结点在超时期限内以建立连接为响应答复了连接请求，则将该终结点标记为正常。

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>使用 TCP 监视时，需要终结点的哪些特定响应？

在使用 TCP 监视时，流量管理器向指定端口的终结点发送 SYN 请求，从而发起三次 TCP 握手。 然后它会等待一段时间（在超时设置中指定），等待来自终结点的响应。 如果在超时期限（在监视设置中指定）内，终结点响应了 SYN 请求，并发出 SYN-ACK 响应，则将该终结点视为正常。 如果收到 SYN-ACK 响应，流量管理器通过 RST 回应，并重置连接。

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>流量管理器将用户从不正常的终结点移走的速度有多快？

流量管理器提供了多种设置，帮助用户控制流量管理器配置文件的故障转移行为，如下所示：
- 可通过将探测间隔设置为 10 秒，提高流量管理器探测终结点的频率。 这可确保尽快检测到任何不正常的终结点。 
- 可以指定运行状况检查请求超时之前的等待时间（最低值为 5 秒）。
- 可以指定将终结点标记为不正常之前的失败次数。 此值最低可以为 0，此时，只要第一次运行状况检查失败，就将该终结点标记为不正常。 但是，如果将容许的失败次数设置为最低值 0，则探测期间发生的任何暂时性问题都可能导致终结点被视为不正常。
- 可将 DNS 响应的生存时间 (TTL) 指定为最低值 0。 这意味着 DNS 解析器无法缓存响应，每个新查询获得的响应包含流量管理器拥有的最新运行状况信息。

使用这些设置，流量管理器可在终结点不正常后 10 秒内进行故障转移，并针对相应的配置文件进行 DNS 查询。

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>如何为配置文件中的不同终结点指定不同的监视设置？

流量管理器监视设置是在每个配置文件级别设置的。 如果只需对一个终结点使用不同的监视设置，可将该终结点作为一个[嵌套式配置文件](traffic-manager-nested-profiles.md)，它的监视设置不同于父配置文件。

### <a name="what-host-header-do-endpoint-health-checks-use"></a>终结点运行状况检查使用什么主机头？

流量管理器在 HTTP 和 HTTPS 运行状况检查中使用 host 标头。 流量管理器使用的 host 标头是在配置文件中配置的终结点目标名称。 在主机头中使用的值不能通过目标属性单独指定。

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>运行状况检查从哪些 IP 地址发起？

以下列表包含流量管理器可从中发起运行状况检查的 IP 地址。 可以使用此列表确保终结点上允许来自这些 IP 地址的传入连接，以便能够检查这些终结点的运行状况。

* 40.68.30.66
* 40.68.31.178
* 137.135.80.149
* 137.135.82.249
* 23.96.236.252
* 65.52.217.19
* 40.87.147.10
* 40.87.151.34
* 13.75.124.254
* 13.75.127.63
* 52.172.155.168
* 52.172.158.37
* 104.215.91.84
* 13.75.153.124
* 13.84.222.37
* 23.101.191.199
* 23.96.213.12
* 137.135.46.163
* 137.135.47.215
* 191.232.208.52
* 191.232.214.62
* 13.75.152.253
* 104.41.187.209
* 104.41.190.203

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>流量管理器可以对终结点进行多少次运行状况检查？

到达终结点的流量管理器运行状况检查次数取决于以下条件：
- 为监视间隔设置的值（在任意给定时期内，间隔越小意味着到达终结点的请求越多）。
- 发起运行状况检查的位置数（在前面的常见问题解答中，列出了可发起这些检查的 IP 地址）。

## <a name="traffic-manager-nested-profiles"></a>流量管理器嵌套式配置文件

### <a name="how-do-i-configure-nested-profiles"></a>如何配置嵌套式配置文件？

可以使用 Azure Resource Manager、经典 Azure REST API、Azure PowerShell cmdlet 和跨平台 Azure CLI 命令配置嵌套式流量管理器配置文件。 也支持通过新 Azure 门户配置这些配置文件。 不支持使用经典门户。

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>流量管理器支持多少层嵌套？

配置文件的嵌套最深可达 10 层。 不允许使用“循环”。

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>能否在同一流量管理器配置文件中将其他终结点类型与嵌套式子配置文件混合在一起使用？

是的。 至于如何在一个配置文件中组合使用不同类型的终结点，并无任何限制。

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>嵌套式配置文件如何应用计费模型？

使用嵌套式配置文件在价格方面不会给你带来负面影响。

流量管理器计费分两部分：终结点运行状况检查以及数百万的 DNS 查询

* 终结点运行状况检查：在父配置文件中将子配置文件配置为终结点时，不对子配置文件收费。 在子配置文件中监视终结点按正常方式计费。
* DNS 查询：每个查询仅统计一次。 对父配置文件执行查询时，如果返回了子配置文件中的终结点，则仅统计父配置文件。

如需完整的详细信息，请参阅[流量管理器定价页](https://azure.microsoft.com/pricing/details/traffic-manager/)。

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>嵌套式配置文件是否会造成性能影响？

不会。 使用嵌套式配置文件不会造成性能影响。

在处理每个 DNS 查询时，流量管理器名称服务器会在内部遍历配置文件层次结构。 对父配置文件执行 DNS 查询可能会收到终结点来自子配置文件的 DNS 响应。 不管使用的是单个配置文件还是嵌套式配置文件，都只使用一条 CNAME 记录。 不需要在层次结构中为每个配置文件创建一条 CNAME 记录。

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>在父配置文件中，流量管理器如何计算嵌套式终结点的运行状况？

父配置文件不会针对子配置文件直接执行运行状况检查。 子配置文件的终结点运行状况用于计算子配置文件的总体运行状况。 此信息在嵌套式配置文件层次结构中向上传播，确定嵌套式终结点的运行状况。 父配置文件使用此聚合运行状况信息确定是否可将流量定向到子配置文件。

下表描述了针对嵌套式终结点执行的流量管理器运行状况检查的行为。

| 子配置文件监视器状态 | 父级终结点监视器状态 | 说明 |
| --- | --- | --- |
| 已禁用。 子配置文件已禁用。 |已停止 |父级终结点状态为“已停止”，而不是“已禁用”。 “已禁用”状态保留用于指示你已在父配置文件中禁用了终结点。 |
| 已降级。 至少一个子配置文件终结点处于“已降级”状态。 |联机：子配置文件中处于“联机”状态的终结点的数目至少是 MinChildEndpoints 的值。<BR>正在检查终结点：子配置文件中处于“联机”和“正在检查终结点”状态的终结点的数目至少是 MinChildEndpoints 的值。<BR>已降级：其他。 |流量将路由到状态为“正在检查终结点”的终结点。 如果将 MinChildEndpoints 设置得过高，终结点将始终处于降级状态。 |
| 联机。 至少一个子配置文件终结点处于“联机”状态。 没有任何终结点处于“已降级”状态。 |见上。 | |
| 正在检查终结点。 至少一个子配置文件终结点处于“正在检查终结点”状态。 没有任何终结点处于“联机”或“已降级”状态 |同上。 | |
| 非活动。 所有子配置文件终结点都处于“Disabled”或“Stopped”状态，除非这是没有终结点的配置文件。 |已停止 | |

## <a name="next-steps"></a>后续步骤：
- 详细了解流量管理器[终结点监视和自动故障转移](../traffic-manager/traffic-manager-monitoring.md)。
- 详细了解流量管理器[流量路由方法](../traffic-manager/traffic-manager-routing-methods.md)。
