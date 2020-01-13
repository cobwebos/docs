---
title: 选择适当的定价层 |Microsoft Azure 映射
description: 在本文中，你将了解 Microsoft Azure 地图提供的定价层。
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5ff774f9848db948058075a98504e6c13db5467a
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911754"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>在 Azure Maps 中选择适当的定价层

Azure Maps 提供了两个定价层。 本文旨在帮助你根据需要选择适当的定价层。 要帮助选择适当的定价层，请考虑以下两个问题。

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>计划使用哪些地理空间功能？
如果核心地理空间 API 满足服务需求，则 S0 定价层适合你。 如果想为应用程序的提供更高级的功能，请考虑选择 S1 定价层。 示例功能包括区域加混合图像、获取路径范围和批量地理编码。 可通过下面的定价层功能表详细了解应用程序的需求。 还可通过该表选择最适合应用程序的定价层。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>计划支持多少位并发用户？ 
S0 和 S1 定价层处理不同数量的数据吞吐量。 在选择 Azure Maps 定价层之前，请考虑一些问题。 例如，“要支持多少位并发用户？” S0 定价层每秒最多可处理 50 次查询。 S1 定价层每秒可处理 50 次及以上查询。

### <a name="pricing-tier-capabilities"></a>定价层功能

| 功能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 搜索（fwd/rev 地理编码，相关点）  |        ✓           |     ✓    |
| Batch 地理编码（预览版）              |                   |     ✓    |
| 来自搜索的多边形          |                   |     ✓    |
| 路线                                 |        ✓           |     ✓    |
| 路由范围                    |                   |     ✓    |
| Batch 路由（预览版）                |                   |     ✓    |
| 矩阵路由（预览版）               |                   |     ✓    |
| 呈现                                  |        ✓           |     ✓    |
| 图像加混合图像    |            |     ✓    |
| 交通                                 |        ✓           |     ✓    |
| 时区                              |        ✓           |     ✓    |
| 地理位置（预览版）                |        ✓           |     ✓    |
| 数据（预览版）               |                   |     ✓    |
| 空间（预览版）               |                   |     ✓    |
| 地理围栏（预览版）               |                   |     ✓    |



这些其他数据点值得考虑：
* 拥有哪种企业？
* 要生成的应用程序有多重要？

请参阅“定价层目标客户”表，以便更好地了解 S0 和 S1 定价层。 有关详细信息，请参阅 [Azure Maps 定价](https://azure.microsoft.com/pricing/details/azure-maps/)。 

### <a name="pricing-tier-targeted-customers"></a>定价层目标客户

| 定价层  |     目标客户                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 定价层适用于中小型企业的客户。 如果没有大量并发用户，那么该定价层是适合的定价层。 如果上表中显示的核心地理空间 API 满足服务需求，该定价层也是适合的定价层。 此层已正式发布。 该定价层适用于所有生产阶段的应用程序：从概念验证开发和早期阶段测试到应用程序生产和部署。<p>|
| S1            |    <p>S1 定价层适用于需要支持大型企业、任务关键型应用程序或并发用户量大的客户。 它也适用于那些需要高级地理空间服务的客户。</p>|

## <a name="next-steps"></a>后续步骤

了解有关如何查看和更改定价层的详细信息：

> [!div class="nextstepaction"] 
> [管理定价层](how-to-manage-pricing-tier.md)
