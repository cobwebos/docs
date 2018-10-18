---
title: Azure Database for PostgreSQL 中的性能建议
description: 本文介绍可在 Azure Database for PostgreSQL 中获得的性能建议。
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 6de302dbcfa9d6d1d2b311f41b03d8e54aeb63f6
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395434"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 中的性能建议

适用于：Azure Database for PostgreSQL 9.6 和 10

> [!IMPORTANT]
> 性能建议在一些有限区域的公共预览版中。

“性能建议”功能将标识可在 Azure Database for PostgreSQL 服务器中创建的顶部索引，以便提高性能。 若要生成索引建议，该功能会考虑各种数据库特征，包括其架构以及如“查询存储”报告的工作负载。 实施任何性能建议后，客户应测试性能以评估这些更改的影响。 

## <a name="permissions"></a>权限
使用性能建议功能运行分析需要“所有者”或“参与者”权限。

## <a name="performance-recommendations"></a>性能建议
[性能建议](concepts-performance-recommendations.md)功能可以跨服务器分析工作负载以标识可能会提高性能的索引。

在 PostgreSQL 服务器的 Azure 门户页上，从菜单栏的“支持 + 故障排除”部分打开“性能建议”。

![性能建议登录页](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

选择“分析”并选择数据库。 随即会开始分析。 这可能需要几分钟的时间才能完成，具体要取决于你的工作负载。 分析完成后，门户中将出现一条通知。

如果找到任何建议，则“性能建议”窗口将显示一个建议列表。 建议将显示有关相关“数据库”、“表”、“列”和“索引大小”的信息。

![性能建议新页](./media/concepts-performance-recommendations/performance-recommendations-result.png)

若要实施建议，请复制查询文本并从所选的客户端中运行。

## <a name="next-steps"></a>后续步骤
- 了解有关如何在 Azure Database for PostgreSQL 中进行[监视和优化](concepts-monitoring.md)的详细信息。

