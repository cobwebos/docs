---
title: 自动增长存储在 Azure 门户在 Azure 数据库中使用 for PostgreSQL-单个服务器
description: 本文介绍如何启用自动增长 PostegreSQL-单个服务器的 Azure 数据库中使用 Azure 门户的存储
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9f88fe3e8a30dd80c5331bd6c8ea7aec401c6fb9
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676753"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>自动增长存储在 Azure 门户在 Azure 数据库中使用 for PostgreSQL-单个服务器
本文介绍如何配置 Azure Database for PostgreSQL 服务器存储以增大且不会影响工作负荷。

当服务器达到已分配的存储限制时，服务器标记为只读的。 但是，如果启用存储自动增长，增加服务器存储，以适应不断增长的数据。 对于小于 100 GB，预配存储的服务器，预配的存储大小被增加 5 GB 的可用存储低于 1 GB 或 10%的预配的存储中的较大时，就立即。 对于包含多个 100 GB 的预配的存储的服务器，预配的存储大小被增加 5%时可用的存储空间低于 5%的预配的存储大小。 最大存储限制为指定[此处](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage)应用。

## <a name="prerequisites"></a>必备组件
若要完成本操作指南，需要：
- [Azure Database for PostgreSQL 服务器](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>启用存储自动增长 

请按照以下步骤来设置的 PostgreSQL 服务器存储自动增长操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure Database for PostgreSQL 服务器。

2. 在 PostgreSQL 服务器页上，在**设置**，单击**定价层**以打开定价层页。

3. 在中**自动增长**部分中，选择**是**启用存储自动增长。

    ![Azure Database for PostgreSQL-Settings_Pricing_tier-自动增长](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. 单击“确定”以保存更改  。

5. 通知会确认该自动增长已成功启用。

    ![Azure Database for PostgreSQL-自动增长成功](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>后续步骤

了解如何[如何针对指标创建警报](howto-alert-on-metric.md)。
