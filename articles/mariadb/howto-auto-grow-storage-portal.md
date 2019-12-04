---
title: 自动增长存储-Azure 门户-Azure Database for MariaDB
description: 本文介绍如何使用 Azure 门户为 Azure Database for MariaDB 启用自动增长存储
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 5c5e9154260a255d9e9b8bc5775a479df7e41522
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767494"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>使用 Azure 门户自动增长 Azure Database for MariaDB 中的存储
本文介绍如何在不影响工作负荷的情况下，将 Azure Database for MariaDB 服务器存储配置为扩展。

当服务器达到分配的存储限制时，服务器将被标记为只读。 但是，如果启用存储自动增长，服务器存储将增加以容纳不断增长的数据。 对于预配的存储小于 100 GB 的服务器，预配的存储大小将在可用存储小于 1 GB 或10% 的预配存储空间后立即增加 5 GB。 对于预配存储超过 100 GB 的服务器，当可用存储空间低于预配的存储大小的5% 时，预配的存储大小将增加5%。 [此处](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage)指定的最大存储限制为 "应用"。

## <a name="prerequisites"></a>必备组件
若要完成本操作指南，需要：
- [Azure Database for MariaDB 服务器](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>启用存储自动增长 

请按照以下步骤设置 MariaDB 服务器存储自动增长：

1. 在[Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for MariaDB 服务器。

2. 在 "MariaDB 服务器" 页上的 "**设置**" 标题下，单击 "**定价层**" 以打开 "定价层" 页。

3. 在 "自动增长" 部分，选择 **"是"** 以启用存储自动增长。

    ![Azure Database for MariaDB-Settings_Pricing_tier 自动增长](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. 单击“确定”以保存更改 。

5. 通知会确认已成功启用自动增长。

    ![Azure Database for MariaDB-自动增长成功](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>后续步骤

了解[如何创建有关指标的警报](howto-alert-metric.md)。
