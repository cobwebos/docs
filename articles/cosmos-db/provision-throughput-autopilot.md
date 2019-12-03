---
title: 在 autopilot 模式下创建 Azure Cosmos 容器和数据库。
description: 了解好处、用例，以及如何在 autopilot 模式下预配 Azure Cosmos 数据库和容器。
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2259343d2c7bca1f60a5256efcd572e6cc21b565
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706037"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>在 autopilot 模式下创建 Azure Cosmos 容器和数据库（预览版）

Azure Cosmos DB 允许在手动或 autopilot 模式下预配容器上的吞吐量。 本文介绍 autopilot 模式的优点和用例。

> [!NOTE]
> Autopilot 模式当前在公共预览版中可用。 若要为 Azure Cosmos 帐户启用 autopilot 功能，请参阅本文的[启用 autopilot](#enable-autopilot)部分。 只能为新数据库和容器启用 autopilot，而不能用于现有容器和数据库。

除了手动预配吞吐量，你现在还可以在 autopilot 模式下配置 Azure cosmos 容器。 在 autopilot 模式下配置的 Azure Cosmos 容器和数据库会根据**应用程序需要自动并立即缩放预配的吞吐量，而不会影响 sla。**

你不再需要手动管理预配的吞吐量或处理速率限制问题。 可以立即缩放在 autopilot 模式下配置的 Azure Cosmos 容器，以响应工作负荷，而不会影响全局工作负荷的可用性、延迟、吞吐量或性能。 在高利用率下，在 autopilot 模式下配置的 Azure Cosmos 容器可以向上或向下扩展，而不会影响正在进行的操作。

在 autopilot 模式下配置容器和数据库时，需要指定不超出 `Tmax` 的最大吞吐量。 然后，容器可根据 `0.1*Tmax < T < Tmax` 范围内的工作负荷需求立即进行缩放。 换句话说，容器和数据库可根据工作负荷的需要即时缩放，其最大吞吐量值为已配置的最大吞吐量值的10%，最大值为配置的最大吞吐量值。 可以在任何时间点在 autopilot 数据库或容器上更改最大吞吐量（Tmax）设置。 借助 autopilot 选项，每个容器或数据库的 400 RU/s 最小吞吐量将不再适用。

在 autopilot 预览过程中，对于容器或数据库上的指定最大吞吐量，系统允许在计算的存储限制内操作。 如果超过了存储限制，则最大吞吐量会自动调整为较高的值。 使用 autopilot 模式的数据库级别吞吐量时，数据库中允许的容器数将计算如下：（0.001 * 最大吞吐量）。 例如，如果你预配 20000 autopilot RU/s，则数据库可以具有20个容器。

## <a name="benefits-of-autopilot-mode"></a>Autopilot 模式的优点

在 autopilot 模式下配置的 Azure Cosmos 容器具有以下优势：

* **简单：** Autopilot 模式下的容器消除了为各种容器手动管理预配吞吐量（ru）和容量的复杂性。

* **可伸缩：** Autopilot 模式中的容器会根据需要无缝缩放预配的吞吐量容量。 客户端连接和应用程序不会中断，它们不会影响任何现有的 Sla。

* **经济高效：** 使用在 autopilot 模式下配置的 Azure Cosmos 容器时，只需为工作负荷每小时需要的资源付费。

* **高可用性：** Autopilot 模式下的 Azure Cosmos 容器使用相同的全球分布式容错、高可用后端，以确保数据持久性和高可用性。

## <a name="use-cases-of-autopilot-mode"></a>Autopilot 模式的使用案例

在 autopilot 模式下配置的 Azure Cosmos 容器的用例包括：

* **可变工作负荷：** 当你运行使用时间最长的应用程序时，每日最多使用1小时到几个小时，或每年数次。 示例包括用于人力资源、预算和操作报告的应用程序。 对于这种情况，可以使用在 autopilot 模式下配置的容器，而不再需要手动预配峰值或 average 容量。

* **不可预知的工作负荷：** 当你运行的工作负荷在一整天都有数据库使用情况，但也很难预测活动的高峰。 例如，当天气预测发生变化时，会看到一个流量站点出现冲击。 在 autopilot 模式下配置的容器调整容量，以满足应用程序峰值负载的需求，并在活动的电涌结束时向下缩放。

* **新应用程序：** 如果要部署新应用程序，并且不确定所需的预配吞吐量（即，多个 ru）。 在 autopilot 模式下配置容器后，可自动缩放以满足应用程序的容量需求和要求。

* 不常用的**应用程序：** 如果你有一个应用程序，该应用程序每天或每周或每月只使用几个小时，如一个小容量应用程序/web/博客网站。

* **开发和测试数据库：** 开发人员在工作时间使用 Azure Cosmos 帐户，但在夜间或周末不需要它们。 使用在 autopilot 模式下配置的容器，它们会在不使用时缩小到最小值。

* **计划的生产工作负荷/查询：** 如果在单个容器上有一系列计划的请求/操作/查询，并且如果有空闲时间段，则可以轻松地执行此操作。 将计划的查询/请求提交到在 autopilot 模式下配置的容器时，它将自动根据需要扩展，并运行操作。

以前问题的解决方案不仅需要大量的时间来实现，而且还会在配置或代码中带来复杂性，并经常需要手动干预来解决这些问题。 Autopilot 模式启用上述方案，使你无需再担心这些问题。

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>比较-在手动模式与 autopilot 模式下配置的容器

|  | 手动模式下配置的容器  | 在 autopilot 模式下配置的容器 |
|---------|---------|---------|
| **预配吞吐量** | 手动预配 | 根据工作负荷使用模式自动并即时缩放。 |
| **请求/操作的速率限制（429）**  | 如果消耗超出预配的容量，可能会发生这种情况。 | 如果使用的吞吐量在 autopilot 模式下所选的最大吞吐量范围内，则不会发生这种情况。   |
| **容量规划** |  你必须进行初始容量规划并设置所需的吞吐量。 |    无需担心容量规划。 系统会自动处理容量规划和容量管理。 |
| **定价** | 每小时手动预配 RU/秒。 | 对于单个写入区域帐户，使用每小时 autopilot RU/秒，按小时使用的吞吐量付费。 <br/><br/>对于具有多个写入区域的帐户，autopilot 不收取额外费用。 使用与每小时相同的多主机 RU/秒的速率为每小时使用的吞吐量付费。 |
| **最适合工作负荷类型** |  可预测和稳定的工作负载|   不可预测和可变工作负荷  |

## <a id="enable-autopilot"></a>从 Azure 门户启用 autopilot

可以通过 Azure 门户中的启用来试用 Azure Cosmos 帐户中的 autopilot。 使用以下步骤启用 autopilot 选项：

1. 登录到[Azure 门户。](https://portal.azure.com)

2. 导航到你的 Azure Cosmos 帐户并打开 "**新建功能**" 选项卡。选择 "**自动试验**" 并进行**注册**，如以下屏幕截图所示：

![在 autopilot 模式下创建容器](./media/provision-throughput-autopilot/enable-autopilot-azure-portal.png)

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>使用 autopilot 模式创建数据库或容器

可以在创建数据库或容器时为其配置 autopilot。 使用以下步骤来执行新的数据库或容器，启用 autopilot，并指定最大吞吐量。

1. 登录到[Azure 门户](https://portal.azure.com)或[Azure Cosmos 资源管理器。](https://cosmos.azure.com/)

1. 导航到你的 Azure Cosmos 帐户并打开**数据资源管理器**"选项卡。

1. 选择 "**新建容器**"，为容器输入名称，分区键为。 选择**Autopilot**选项，并在使用 Autopilot 选项时选择容器不能超过的最大吞吐量。

   ![在 autopilot 模式下创建容器](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. 选择“确定”

使用类似的步骤，还可以在 autopilot 模式下创建具有预配吞吐量的数据库。

## <a id="autopilot-limits"></a>Autopilot 的吞吐量和存储限制

下表显示了 autopilot 模式下不同选项的整个和存储限制：

|最大吞吐量限制  |最大存储限制  |
|---------|---------|
|4000 RU/秒  |   50 GB    |
|20000 RU/秒  |  200 GB  |
|100000 RU/秒    |  1 TB   |
|500000 RU/秒    |  5 TB  |

## <a name="next-steps"></a>后续步骤

* 详细了解[逻辑分区](partition-data.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
