---
title: 选择适当的定价层 |Microsoft Azure 映射
description: 在本文中，你将了解 Microsoft Azure 地图提供的定价层。
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 8764e9161f952118ca7ae28343dcd16477cf1eee
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155751"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>在 Azure Maps 中选择适当的定价层

Azure Maps 提供了两个定价层： S0 和 S1。 本文旨在帮助你根据需要选择适当的定价层。 要帮助选择适当的定价层，请考虑以下两个问题。

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>计划使用哪些地理空间功能？
如果核心地理空间 API 满足服务需求，则 S0 定价层适合你。 如果想为应用程序的提供更高级的功能，请考虑选择 S1 定价层。 高级功能的示例：高空加混合图像、获取路由范围和批处理地理编码。 **定价层功能**表可帮助你选择最适合你的应用程序的定价层。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>计划支持多少位并发用户？ 
S0 和 S1 定价层处理不同数量的数据吞吐量。 S0 定价层**每秒**最多处理50个查询，而 S1 层**每秒**处理超过50个查询。

### <a name="pricing-tier-capabilities"></a>定价层功能

| 功能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 地图呈现                              | ✓                   | ✓       |
| 卫星图像                       |                     | ✓        |
| 搜索                                  | ✓                    | ✓        |
| 批搜索                            |                     | ✓        |
| 路由                                   | ✓                    |✓        |
| 批路由                            |                    | ✓        |
| 矩阵路由                          |                     | ✓        |
| 路线范围（等时线）                |                     | ✓        |
| 交通                                |✓                    |✓        |
| 时区                               |✓                    |✓        |
| 地理位置（预览）                    |✓                   |✓        |
| 空间操作                        |                    |✓        |
| 地理围栏                                |                    |✓        |
| Azure Maps 数据（预览版）                |                     | ✓        |
| 移动性（预览版）                       |                     | ✓        |
| 天气（预览）                        |✓                    |✓        |

这些其他数据点值得考虑：
* 你拥有哪种类型的企业？
* 应用程序的重要程度如何？

### <a name="pricing-tier-targeted-customers"></a>定价层目标客户

请参阅“定价层目标客户”表，以便更好地了解 S0 和 S1 定价层。 有关详细信息，请参阅 [Azure Maps 定价](https://azure.microsoft.com/pricing/details/azure-maps/)。 

| 定价层  |     目标客户                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 定价层适用于所有生产阶段的应用程序：从概念证明开发和初期阶段测试到应用程序生产和部署。 但是，此层专为小规模开发而设计，同时为具有低并发用户的客户或两者。 <p>|
| S1            |    <p>S1 定价层适用于需要支持大型企业、任务关键型应用程序或并发用户量大的客户。 它也适用于那些需要高级地理空间服务的客户。</p>|

## <a name="next-steps"></a>后续步骤

了解有关如何查看和更改定价层的详细信息：

> [!div class="nextstepaction"] 
> [管理定价层](how-to-manage-pricing-tier.md)
