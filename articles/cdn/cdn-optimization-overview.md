---
title: "针对方案优化 Azure 内容传送"
description: "如何针对特定方案优化内容传送"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.openlocfilehash: 98941c49b057380b3ef9164515bcc2a63ccb56ce
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="optimize-azure-content-delivery-for-your-scenario"></a>针对方案优化 Azure 内容传送

向许多全球受众传送内容时，请务必优化内容传送。 Azure 内容传送网络可以根据内容类型优化传送体验。 内容可以是网站、实时传送视频流、视频或可供下载的大文件。 创建内容传送网络 (CDN) 终结点时，请在“优化针对方案”选项中指定方案。 选择哪个方案决定了对通过 CDN 终结点传送的内容应用哪种优化。

优化选项旨在通过最佳做法行为来提升内容传送性能和改进源卸载。 选择的方案会修改部分缓存、对象区块和源故障重试策略的配置，从而影响性能。 

本文概述了各种优化功能以及何时应使用这些功能。 有关功能和限制的详细信息，请参阅各个优化类型对应的文章。

> [!NOTE]
> “优化针对方案”选项可能会因选择的提供程序而异。 CDN 提供程序以不同的方式应用增强功能，具体视方案而定。 

## <a name="provider-options"></a>提供程序选项

Akamai 的 Azure 内容传送网络支持：

* 常规 Web 传送 

* 常规媒体流式处理

* 点播视频媒体流式处理

* 大文件下载

* 动态站点加速 

Verizon 的 Azure 内容传送网络仅支持“常规 Web 传送”。 此选项可用于点播视频和大文件下载。 无需选择优化类型。

强烈建议测试不同提供程序的性能差异，以选择最适合传送内容的提供程序。

## <a name="select-and-configure-optimization-types"></a>选择并配置优化类型

若要新建终结点，请选择与方案和要通过终结点传送的内容类型最匹配的优化类型。 系统默认选择“常规 Web 传送”。 可以随时更新任何现有 Akamai 终结点的优化选项。 此更改不会中断 CDN 内容传送。 

1. 在标准 Akamai 配置文件中，选择一个终结点。

    ![终结点选择 ](./media/cdn-optimization-overview/01_Akamai.png)

2. 在“设置”下，选择“优化”。 然后，在“优化针对方案”下拉列表中选择一种类型。

    ![优化和类型选择](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>适用于特定方案的优化类型

可以为下列方案之一优化 CDN 终结点。 

### <a name="general-web-delivery"></a>常规 Web 传送

常规 Web 传送优化是最常见的优化选项。 此选项用于优化常规 Web 内容，如网页和 Web 应用程序。 此优化选项还可用于优化文件和视频下载。

典型网站包含静态和动态内容。 静态内容包括可以缓存并传送给不同用户的图像、JavaScript 库和样式表。 动态内容针对各个用户进行个性化设置，如针对用户配置文件定制的新闻项。 由于对每个用户都是唯一的，因此动态内容不进行缓存，如购物车内容。 常规 Web 传送优化可以优化整个网站。 

> [!NOTE]
> 如果使用 Akamai 的 Azure 内容传送网络，建议在平均文件大小不到 10MB 时使用此优化。 如果平均文件大小超过 10MB，请从“优化针对方案”下拉列表中选择“大文件下载”。

### <a name="general-media-streaming"></a>常规媒体流式处理

如果需要将终结点用于实时传送视频流和点播视频流，建议使用常规媒体流式处理优化。

媒体流式处理为时间敏感型，因为客户端上延迟到达的数据包可能会导致浏览体验下降，如视频内容频繁缓冲。 媒体流式处理优化可减少媒体内容传送延迟，并为用户提供平滑流式处理体验。 

这种方案对 Azure 媒体服务客户很常见。 使用 Azure 媒体服务时，可获得一个用于实时传送视频流和点播视频流的流式处理终结点。 对于这种方案，客户在从实时传送视频流更改为点播视频流时，无需切换到其他终结点。 常规媒体流式处理优化支持这种类型的方案。

Verizon 的 Azure 内容传送网络使用常规 Web 传送优化类型来提供流媒体内容。

若要详细了解媒体流式处理优化，请参阅[媒体流式处理优化](cdn-media-streaming-optimization.md)。

### <a name="video-on-demand-media-streaming"></a>点播视频媒体流式处理

点播视频媒体流式处理优化可优化点播视频流内容。 如果将终结点用于点播视频流，建议使用此选项。

Verizon 的 Azure 内容传送网络使用常规 Web 传送优化类型来提供流媒体内容。

若要详细了解媒体流式处理优化，请参阅[媒体流式处理优化](cdn-media-streaming-optimization.md)。

> [!NOTE]
> 如果终结点主要用于点播视频内容，请使用此优化类型。 这种优化与常规媒体流式处理优化的主要区别在于连接重试超时。 实时传送视频流方案的超时时间要短得多。

### <a name="large-file-download"></a>大文件下载

如果使用 Akamai 的 Azure 内容传送网络，必须使用大文件下载优化来传送大于 1.8GB 的文件。 如果是常规 Web 传送优化，Verizon 的 Azure 内容传送网络对文件下载大小没有限制。

如果使用 Akamai 的 Azure 内容传送网络，对大于 10MB 的内容使用大文件下载优化。 如果平均文件大小不到 10MB，建议使用常规 Web 传送优化。 如果平均文件大小始终超过 10MB，为大文件单独创建终结点可能会更有效。 例如，固件或软件更新通常是大文件。

Verizon 的 Azure 内容传送网络使用常规 Web 传送优化类型来提供流媒体内容。

若要详细了解大文件优化，请参阅[大文件优化](cdn-large-file-optimization.md)。

### <a name="dynamic-site-acceleration"></a>动态站点加速

 Akamai 和 Verizon 内容传送网络配置文件均可提供动态站点加速。 此优化需要额外付费才能使用。 有关详细信息，请参阅定价页。

动态站点加速包括各种对动态内容延迟和性能有益的技术。 这些技术包括路由和网络优化、TCP 优化等。 

此优化可用于加速许多响应都不可缓存的 Web 应用。 例如，搜索结果、签出事务或实时数据。 可以继续对静态数据使用核心 CDN 缓存功能。 



