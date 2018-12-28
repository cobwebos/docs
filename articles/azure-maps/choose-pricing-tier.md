---
title: 为 Azure Maps 选择适当的定价层 | Microsoft Docs
description: 详细了解由 Azure Maps 提供的定价层
author: walsehgal
ms.author: v-musehg
ms.date: 12/05/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: ee277867f449afddeb89c3fd73b5b577a68a4497
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998382"
---
# <a name="choosing-the-right-pricing-tier-in-azure-maps"></a>在 Azure Maps 中选择适当的定价层

Azure Maps 提供了两个定价层。 本文旨在帮助你根据需要选择适当的定价层。 要帮助选择适当的定价层，请考虑两个问题：

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>计划使用哪些地理空间功能？
如果你认为核心地理空间 API 满足服务需求，则 S0 定价层适合你。 如果你想为应用程序提供更高级的功能（如区域 + 混合图像、获取路径范围、批地理编码等），请考虑选择 S1 定价层。 通过以下包含“定价层功能”的表格，你可以更好地了解应用程序需求，同时选择最适合你的应用程序的定价层。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>计划支持多少位并发用户？ 
S0 和 S1 定价层可以处理不同数量的数据吞吐量。 在选择 Azure Maps 定价层之前，请考虑以下问题：例如，计划支持多少位并发用户？ S0 定价层每秒最多可处理“50 次查询”；S1 定价层每秒可处理“50 次以上的查询”。

### <a name="pricing-tier-capabilities"></a>定价层功能

| 功能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 搜索                                  |        ✓           |     ✓    |
| 路由                                 |        ✓           |     ✓    |
| 呈现                                  |        ✓           |     ✓    |
| 交通                                 |        ✓           |     ✓    |
| 时区                              |        ✓           |     ✓    |
| *图像 + 混合图像（预览版）     |        ✓           |     ✓    |
| *路由范围（预览版）                  |        ✓           |     ✓    |
| *IP 2 位置（预览版）                |        ✓           |     ✓    |
| *来自搜索的多边形（预览版）         |        ✓           |     ✓    |
| *Batch 地理编码（预览版）              |        ✓           |     ✓    |
| *Batch 路由（预览版）                |        ✓           |     ✓    |
| *矩阵路由（预览版）               |        ✓           |     ✓    |

> [!Note]
> 将于 2019 年 2 月 4 日之后弃用从 S0 定价层访问这些功能。

值得考虑的一些其他数据点是，你拥有哪种企业或要生成的应用程序有多重要？

请参阅“定价层目标客户”表，以便更好地了解 S0 和 S1 定价层。 有关 Azure Maps 定价的详细信息，请参阅 [Azure Maps 定价](https://azure.microsoft.com/pricing/details/azure-maps/)。 

### <a name="pricing-tier-targeted-customers"></a>定价层目标客户

| 定价层  |        目标客户                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 定价层适用于中小型企业的客户。 如果你不需要大量并发用户并且核心地理空间 API 满足服务需求，则它是适合你的定价层，如下表所示。 此层已正式发布，适用于从概念证明开发和早期阶段测试到应用程序生产和部署的所有生产阶段的应用程序。<p>|
| S1            |    <p>S1 定价层适用于需要支持大型企业、任务关键型应用程序、并发用户量大或需要高级地理空间服务的客户。</p>|


## <a name="next-steps"></a>后续步骤

详细了解查看和更改定价层：

> [!div class="nextstepaction"]
> [管理定价层](how-to-manage-pricing-tier.md)
