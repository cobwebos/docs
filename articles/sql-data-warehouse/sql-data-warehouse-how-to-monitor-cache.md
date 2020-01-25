---
title: 优化 Gen2 缓存
description: Azure Portal을 사용하여 Gen2 캐시를 모니터링하는 방법에 대해 알아봅니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 924705b7ce1d324583077797714491bdf3fc5cc9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721211"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Gen2 캐시 모니터링 방법
Gen2 스토리지 아키텍처는 Gen2 데이터 웨어하우스용으로 설계된 NVMe 기반의 SSD에 있는 캐시에 가장 자주 쿼리되는 columnstore 세그먼트를 자동으로 계층화합니다. 쿼리가 캐시에 있는 세그먼트를 검색하면 성능이 향상됩니다. 이 문서에서는 워크로드가 Gen2 캐시를 최적으로 활용하는지 판단하여 쿼리 성능을 모니터링하고 문제를 해결하는 방법에 대해 설명합니다.  
## <a name="troubleshoot-using-the-azure-portal"></a>Azure Portal을 사용하여 문제 해결
Azure Monitor를 사용하여 Gen2 캐시 메트릭을 보고 쿼리 성능 문제를 해결할 수 있습니다. 먼저 Azure Portal로 이동하여 Azure Monitor를 클릭합니다.

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

选择 "指标" 按钮，并填写数据仓库的 "**订阅**"、"**资源** **组**"、"**资源类型**" 和 "**资源名称**"。

Gen2 캐시 문제 해결에 대한 키 메트릭은 **캐시 적중 비율** 및 **캐시 사용 비율**입니다. 이러한 두 개의 메트릭을 표시하도록 Azure 메트릭 차트를 구성합니다.

![캐시 메트릭](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>캐시 적중 및 사용 비율
아래 매트릭스에서는 캐시 메트릭의 값에 따라 시나리오를 설명합니다.

|                                | **높은 캐시 적중 비율** | **낮은 캐시 적중 비율** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **높은 캐시 사용 비율** |          시나리오 1           |          시나리오 2          |
| **낮은 캐시 사용 비율**  |          시나리오 3           |          시나리오 4          |

**시나리오 1:** 현재 캐시를 최적으로 사용하고 있습니다. [排查](sql-data-warehouse-manage-monitor.md)可能会减慢查询速度的其他方面。

**시나리오 2:** 현재 작업 중인 데이터 집합이 물리적 읽기로 인해 낮은 캐시 적중 비율을 초래할 수 있어 캐시에 적합하지 않습니다. 성능 수준의 규모를 확장하고 워크로드를 다시 실행하여 캐시를 채우는 것을 고려하세요.

**시나리오 3:** 캐시와 관련이 없는 이유로 인해 쿼리가 느리게 실행되는 것 같습니다. [排查](sql-data-warehouse-manage-monitor.md)可能会减慢查询速度的其他方面。 또한 캐시 크기를 줄여 비용을 절약하도록 [인스턴스 규모를 축소](sql-data-warehouse-manage-monitor.md)하는 것을 고려해 보세요. 

**시나리오 4:** 쿼리가 느려진 원인일 수 있는 콜드 캐시가 있습니다. 작업 데이터 세트가 캐시될 때 쿼리를 다시 실행해보세요. 

> [!IMPORTANT]
> 如果在重新运行工作负荷后未更新缓存命中百分比或缓存已用百分比，则工作集可能已驻留在内存中。 仅缓存聚集列存储表。

## <a name="next-steps"></a>다음 단계
일반 쿼리 성능 튜닝에 대한 자세한 내용은 [쿼리 실행 모니터링](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution)을 참조하세요.
