---
title: Azure 流量管理器中的真实用户度量
description: 流量管理器中的实际用户度量简介
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 4aa2649ba2e49e1fec1b9b124a9b82313280cee9
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333724"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>流量管理器的实际用户度量概述

将流量管理器配置文件设置为使用性能路由方法时，该服务将查看 DNS 查询请求的来源，做出路由决策将这些请求者定向到可为其提供最低延迟的 Azure 区域。 这是通过利用流量管理器为不同最终用户网络维护的网络延迟智能实现的。

使用实际用户度量，可通过最终用户使用的客户端应用程序度量 Azure 区域的网络延迟，让流量管理器在做出路由决策时也考虑该信息。 选择使用“实际用户度量”，可以提高来自最终用户所在这些网络的请求的路由准确性。 

## <a name="how-real-user-measurements-work"></a>实际用户度量的工作原理

真实用户度量的工作原理是让客户端应用程序度量 Azure 区域的延迟，因为从使用这些应用程序的最终用户网络可以看到此延迟。 例如，如果你的网页被不同位置（例如，在北美区域中）的用户访问，那么你可以使用带有性能路由方法的真实用户度量来将其带到服务器应用程序所在的最佳 Azure 区域。

首先，将 Azure 提供的 JavaScript（其中包含唯一密钥）嵌入到网页中。 完成该操作后，每当用户访问该网页时，JavaScript 都会查询流量管理器以获取有关它应度量的 Azure 区域的信息。 服务将一组终结点返回给脚本，然后脚本将连续度量这些区域，方法是下载这些 Azure 区域中托管的单像素图像，并记下发送请求的时间与收到第一个字节的时间之间的延迟。 然后将这些度量报告回流量管理器服务。

在一段时间内，这种情况在多个网络上多次发生使流量管理器可以获取有关最终用户所在网络的延迟特征的更准确信息。 此信息开始包括在流量管理器做出的路由决策中。 因此，提高了基于发送的实际用户度量做出的这些决策的准确性。

使用实际用户度量时，将基于发送到流量管理器的度量数进行计费。 有关定价的详细信息，请访问[流量管理器定价页](https://azure.microsoft.com/pricing/details/traffic-manager/)。

## <a name="faqs"></a>常见问题

* [使用真实用户度量有哪些好处？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-benefits-of-using-real-user-measurements)

* [能否在非 Azure 区域使用真实用户度量？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-non-azure-regions)

* [哪个路由方法受益于真实用户度量？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#which-routing-method-benefits-from-real-user-measurements)

* [是否需要单独启用每个配置文件真实用户度量？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [如何实现关闭我的订阅真实用户度量？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [是否可以将真实用户度量用于 web pages 以外的客户端应用程序？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [每次呈现我的真实用户度量启用的网页时, 会进行多少度量？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [在我的网页中运行真实用户度量脚本之前是否有延迟？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [能否只使用需要衡量的 Azure 区域的真实用户度量？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [能否限制对特定数量的度量值的数量？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [我是否可以查看客户端应用程序在真实用户度量过程中所占用的度量值？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [能否修改流量管理器提供的度量脚本？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [其他人是否可以看到我在真实用户度量中使用的密钥？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [其他人是否会滥用我的 RUM 密钥？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-others-abuse-my-rum-key)

* [是否需要将度量 JavaScript 放入我的所有网页？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [如果使用真实用户度量, 可以通过流量管理器识别我的最终用户的相关信息？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [网页是否需要使用流量管理器进行路由真实用户度量？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [是否需要在 Azure 区域托管任何服务以便与真实用户度量一起使用？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [使用真实用户度量时, Azure 带宽使用率是否会增加？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>后续步骤
- 了解如何[将实际用户度量用于网页](traffic-manager-create-rum-web-pages.md)
- 了解[流量管理器工作原理](traffic-manager-overview.md)
- 详细了解 [Mobile Center](https://docs.microsoft.com/mobile-center/)
- 详细了解流量管理器支持的[流量路由方法](traffic-manager-routing-methods.md)
- 了解如何[创建流量管理器配置文件](traffic-manager-create-profile.md)

