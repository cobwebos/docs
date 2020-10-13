---
title: 自动增长存储-Azure 门户-Azure Database for PostgreSQL-单一服务器
description: 本文介绍如何使用 Azure Database for PostgreSQL 单一服务器中的 Azure 门户配置存储自动增长
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: 1853bbb0e44526988b34ed23126664c5a2285a7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907511"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>使用 Azure 门户在 Azure Database for PostgreSQL（单一服务器）中自动增长存储
本文介绍如何将 Azure Database for PostgreSQL 服务器存储配置为在不影响工作负荷的情况下增长。

在服务器达到了分配的存储限制时，该服务器将被标记为只读。 但是，如果你启用存储自动增长，则服务器存储会增长，以容纳不断增加的数据。 对于预配的存储大小小于 100 GB 的服务器，可用存储空间一旦小于 1 GB 或预配存储的 10%（以这二者中的较大值为准），预配的存储大小就会立即增加 5 GB。 对于预配的存储大小大于 100 GB 的服务器，可用存储空间小于预配的存储大小的 5% 时，预配的存储大小就会增加 5%。 [此处](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage)所指定的最大存储限制适用。

## <a name="prerequisites"></a>必备条件
若要完成本操作指南，需要：
- [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>启用存储自动增长 

请按照下列步骤设置 PostgreSQL 服务器存储自动增长：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for PostgreSQL 服务器。

2. 在 PostgreSQL 服务器页上，单击“设置”  下的“定价层”  ，以打开“定价层”页。

3. 在“自动增长”部分中，选择“是”   以启用存储自动增长。

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for PostgreSQL - Settings_Pricing_tier - 自动增长":::

4. 单击“确定”以保存更改  。

5. 此时将显示一则通知，确认自动增长已成功启用。

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Azure Database for PostgreSQL - Settings_Pricing_tier - 自动增长":::

## <a name="next-steps"></a>后续步骤

了解[如何基于指标创建警报](howto-alert-on-metric.md)。
