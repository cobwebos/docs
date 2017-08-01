---
title: "应用程序代理应用程序加载耗时过长 | Microsoft Docs"
description: "对 Azure AD 应用程序代理的页面加载性能问题进行故障排除"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: e172f55d45bccfd126928225eb9f78da61a670d3
ms.contentlocale: zh-cn
ms.lasthandoff: 04/18/2017

---

# <a name="an-application-proxy-application-takes-too-long-to-load"></a>应用程序代理应用程序加载耗时过长

本文可帮助了解 Azure AD 应用程序代理应用程序加载耗时过长的原因，以及解决此问题的方法。

## <a name="overview"></a>概述
如果应用程序可以运行但存在长延迟，可以考虑对网络拓扑进行一些微调来提高速度。 有关不同拓扑的评估，请参阅[网络注意事项文档](https://docs.microsoft.com/azure/active-directory/application-proxy-network-topology-considerations)。

如果这些注意事项并未奏效，很遗憾，我们当前无法提供进一步的性能调整建议。 随着应用程序代理服务扩展到更多数据中心并且可能更贴近用户，用户可直观地看到延迟得以改善。 若要查看 Azure 数据中心的完整列表，可以查看[延迟测试页](http://www.azurespeed.com/Azure/Latency)。 

具有应用程序代理服务的数据中心可以在[连接器端口测试工具](https://aadap-portcheck.connectorporttest.msappproxy.net/)上找到。 

## <a name="feedback-on-application-proxy-data-center-locations"></a>有关应用程序代理数据中心位置的反馈 
到目前为止，可能有 Azure 数据中心未包含应用程序代理，但会带来很大的延迟改进。 请通过电子邮件将数据中心位置发送至 <aadapfeedback@microsoft.com>，以便我们可以在扩展时使用反馈进行规划。

我们正致力于推出一些附加功能，以帮助当前面临长延迟的租户改善延迟，并确保一旦可用，便会共享文档。

## <a name="next-steps"></a>后续步骤
[使用现有的本地代理服务器](application-proxy-working-with-proxy-servers.md)

