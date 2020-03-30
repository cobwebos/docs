---
title: 选择正确的定价层 |微软 Azure 地图
description: 在本文中，您将了解 Microsoft Azure 地图提供的定价层。
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335662"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>在 Azure Maps 中选择适当的定价层

Azure 地图提供两个定价层，S0 和 S1。 本文旨在帮助你根据需要选择适当的定价层。 要选择正确的定价层，请问自己以下两个问题。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>计划支持多少位并发用户？ 
S0 和 S1 定价层处理不同数量的数据吞吐量。 S0 定价层每秒最多可处理 50 次查询****。 而 S1 层**每秒处理 50 多个查询**。

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>计划使用哪些地理空间功能？
如果核心地理空间 API 满足您的服务要求，S0 定价层适合您的选择。 如果想为应用程序的提供更高级的功能，请考虑选择 S1 定价层。 高级功能包括：航空和混合影像、获取路由范围和批处理地理编码。 查看**定价层功能**表以选择最适合您的应用程序的定价层。

### <a name="pricing-tier-capabilities"></a>定价层功能

| 功能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 地图渲染                              | ✓                   | ✓       |
| 卫星图像                       |                     | ✓        |
| 搜索                                  | ✓                    | ✓        |
| 批处理搜索                            |                     | ✓        |
| 路由                                   | ✓                    |✓        |
| 批处理路由                            |                    | ✓        |
| 矩阵路由                          |                     | ✓        |
| 路线范围（伊索奇罗内斯）                |                     | ✓        |
| 交通                                |✓                    |✓        |
| 时区                               |✓                    |✓        |
| 地理位置（预览）                    |✓                   |✓        |
| 空间操作                        |                    |✓        |
| 地理围栏                                |                    |✓        |
| Azure 地图数据（预览）                |                     | ✓        |
| 移动性（预览版）                       |                     | ✓        |
| 天气（预览）                        |✓                    |✓        |

请考虑以下其他要点：
* 您拥有哪种类型的企业？
* 您的应用程序有多重要？

### <a name="pricing-tier-targeted-customers"></a>定价层目标客户

请参阅“定价层目标客户”表，以便更好地了解 S0 和 S1 定价层****。 有关详细信息，请参阅 [Azure Maps 定价](https://azure.microsoft.com/pricing/details/azure-maps/)。 

| 定价层  |     目标客户                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 定价层适用于生产所有阶段的应用程序：从概念验证开发和早期测试到应用程序生产和部署。 但是，此层专为小规模开发或并发用户数低的客户或两者兼而有之而设计。 <p>|
| S1            |    <p>S1 定价层适用于具有大型企业应用程序、任务关键型应用程序或大量并发用户的客户。 它也适用于那些需要高级地理空间服务的客户。</p>|

## <a name="next-steps"></a>后续步骤

了解有关如何查看和更改定价层的详细信息：

> [!div class="nextstepaction"] 
> [管理定价层](how-to-manage-pricing-tier.md)
