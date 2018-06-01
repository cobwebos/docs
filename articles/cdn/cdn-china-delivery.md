---
title: 使用 Azure CDN 进行中国内容分发 | Microsoft Docs
description: 了解如何使用 Azure 内容分发网络 (CDN) 将内容分发给中国用户。
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
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 59788f301bb501103babd55a2ac37102932f4dcf
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259570"
---
# <a name="china-content-delivery-with-azure-cdn"></a>使用 Azure CDN 进行中国内容分发

Azure 内容分发网络 (CDN) 全球可以向中国用户提供内容，包括中国附近的接入点 (POP) 位置，或者任何可为源自中国的请求提供最佳性能的 POP。 但是，如果中国对你的客户来说是一个重要市场，并且他们需要快速的性能，请考虑改用 Azure CDN 中国。

Azure CDN 中国与 Azure CDN 全球不同之处在于，它通过与许多本地提供商合作提供来自中国内部 POP 的内容。 由于中国合规性和监管，你必须注册一个单独的订阅才能使用 Azure CDN 中国，并且你的网站需要具有 ICP 许可证。 在 Azure CDN 全球和 Azure CDN 中国之间，用于启用和管理内容分发的门户和 API 体验是相同的。

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Azure CDN 全球与 Azure CDN 中国的比较

Azure CDN 全球和 Azure CDN 中国具有以下功能：

- Azure CDN 全球：

     - 门户：https://portal.azure.com  

     - 在中国之外执行内容分发

     - 四个定价层：Microsoft 标准、Verizon 标准、Verizon 高级和 Akamai 标准

     - [文档](https://docs.microsoft.com/en-us/azure/cdn/)

- Azure CDN 中国：

     - 门户：https://portal.azure.cn

     - 在中国境内执行内容分发

     - 两个定价层：标准和高级

     - [文档](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure CDN 中国的详细信息，请参阅：

- [内容分发网络功能](https://www.azure.cn/en-us/home/features/cdn/)

- [Azure 内容分发网络概述](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [使用 Azure 内容分发网络](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [在中国可用的 Azure 服务](https://docs.microsoft.com/en-us/azure/china/china-get-started-service-availability)



