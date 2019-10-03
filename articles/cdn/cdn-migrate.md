---
title: 将 Azure CDN 配置文件从标准 Verizon 迁移到高级 Verizon | Microsoft Docs
description: 详细了解如何将配置文件从标准 Verizon 迁移到高级 Verizon。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: c11155223220c075c28769a19875691783cce3bd
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593628"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>将 Azure CDN 配置文件从标准 Verizon 迁移到高级 Verizon

创建 Azure 内容分发网络 (CDN) 配置文件时，Azure CDN 提供四种不同的产品供你选择。 有关不同产品及其可用功能的信息，请参阅[比较 Azure CDN 产品功能](cdn-features.md)。

如果已经创建 Verizon 的 Azure CDN 标准版  配置文件，并使用它来管理 CDN 终结点，则可以选择将其升级为 Verizon 的 Azure CDN 高级版  配置文件。 升级时，将保留 CDN 终结点和所有数据。 

> [!IMPORTANT]
> 升级到 Verizon 的 Azure CDN 高级版  配置文件后，以后将无法将其转换回 Verizon 的 Azure CDN 标准版  配置文件。
> 

若要升级 Verizon 的 Azure CDN 标准版  配置文件，请联系 [Microsoft 支持部门](https://azure.microsoft.com/support/options/)。

## <a name="profile-comparison"></a>配置文件比较
Verizon 的 Azure CDN 高级版  配置文件与 Verizon 的 Azure CDN 标准版  配置文件具有以下主要差异：
- 对于某些 Azure CDN 功能（如[压缩](cdn-improve-performance.md)、[缓存规则](cdn-caching-rules.md)和[地区筛选](cdn-restrict-access-by-country.md)），则不能使用 Azure CDN 接口，而必须通过“管理”  按钮使用 Verizon 门户。
- API：不同于与标准 Verizon 您不能使用 API 来控制从高级 Verizon 门户访问这些功能。 但是，你可以使用 API 来控制其他常见功能，如创建/删除终结点、清除/加载缓存的资产以及启用/禁用自定义域。
- 定价：高级 Verizon 的标准 Verizon 比数据传输具有不同的定价结构。 有关详细信息，请参阅[内容分发网络定价](https://azure.microsoft.com/pricing/details/cdn/)。

Verizon 的 Azure CDN 高级版  配置文件具有以下附加功能：
- [令牌身份验证](cdn-token-auth.md):允许用户获取并使用标记来提取安全的资源。
- [规则引擎](cdn-rules-engine.md):允许你自定义如何处理 HTTP 请求。
- 高级分析工具：
   - [详细的 HTTP 分析](cdn-advanced-http-reports.md)
   - [边缘性能分析](cdn-edge-performance.md)
   - [实时分析](cdn-real-time-alerts.md)


## <a name="next-steps"></a>后续步骤
若要了解有关规则引擎的详细信息，请参阅 [Azure CDN 规则引擎参考](cdn-rules-engine-reference.md)。

