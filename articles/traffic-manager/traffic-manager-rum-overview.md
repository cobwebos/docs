---
title: "Azure 流量管理器中的实际用户度量 | Microsoft Docs"
description: "流量管理器中的实际用户度量简介"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: a7e8ae605b6234341d9ab8b790f4c54d8627f29f
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="traffic-manager-real-user-measurements-overview"></a>流量管理器的实际用户度量概述

>[!NOTE]
>流量管理器中的“实际用户度量”功能以公共预览版形式提供，并且其可用性和可靠性与正式发布版本中的功能可能不在同一级别。 此功能不受支持，可能存在功能限制，并且可能不是在所有 Azure 区域都可用。 有关此功能可用性和状态方面的最新通知，请参阅 [Azure 流量管理器更新](https://azure.microsoft.com/updates/?product=traffic-manager)页。

将流量管理器配置文件设置为使用性能路由方法时，该服务将查看 DNS 查询请求的来源，做出路由决策将这些请求者定向到可为其提供最低延迟的 Azure 区域。 这是通过利用流量管理器为不同最终用户网络维护的网络延迟智能实现的。

使用实际用户度量，可通过最终用户使用的客户端应用程序度量 Azure 区域的网络延迟，让流量管理器在做出路由决策时也考虑该信息。 选择使用“实际用户度量”，可以提高来自最终用户所在这些网络的请求的路由准确性。 

## <a name="how-real-user-measurements-work"></a>实际用户度量的工作原理

实际用户度量的工作原理是让客户端应用程序度量 Azure 区域的延迟，因为从使用这些应用程序的最终用户网络可以看到此延迟。 例如，如果你的网页被不同位置（例如，在北美区域中）中的用户访问，在使用性能路由方法时可以利用“实际用户度量”的强大功能将这些用户定位到托管服务器应用程序的最佳 Azure 区域。

首先，将 Azure 提供的 JavaScript（其中包含唯一密钥）嵌入到网页中。 完成该操作后，每当用户访问该网页时，JavaScript 都会查询流量管理器以获取有关它应度量的 Azure 区域的信息。 服务将一组终结点返回给脚本，然后脚本将连续度量这些区域，方法是下载这些 Azure 区域中托管的单像素图像，并记下发送请求的时间与收到第一个字节的时间之间的延迟。 然后将这些度量报告回流量管理器服务。

在一段时间内，这种情况在多个网络上多次发生使流量管理器可以获取有关最终用户所在网络的延迟特征的更准确信息。 此信息开始包括在流量管理器做出的路由决策中。 因此，提高了基于发送的实际用户度量做出的这些决策的准确性。

使用实际用户度量时，将基于发送到流量管理器的度量数进行计费。 有关定价的详细信息，请访问[流量管理器定价页](https://azure.microsoft.com/pricing/details/traffic-manager/)。

## <a name="next-steps"></a>后续步骤
- 了解如何[将实际用户度量用于网页](traffic-manager-create-rum-web-pages.md)
- 了解[流量管理器工作原理](traffic-manager-overview.md)
- 详细了解 [Mobile Center](https://docs.microsoft.com/mobile-center/)
- 详细了解流量管理器支持的[流量路由方法](traffic-manager-routing-methods.md)
- 了解如何[创建流量管理器配置文件](traffic-manager-create-profile.md)


