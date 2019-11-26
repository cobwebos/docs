---
title: 在 autopilot 模式下创建 Azure Cosmos 容器和数据库。
description: 了解在 autopilot 模式下预配 Azure Cosmos 数据库和容器的优势、用例和方法。
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 584fedc2ebe93b2a3cfd8a3b538a410d29aebe9d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383093"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>在 autopilot 模式下创建 Azure Cosmos 容器和数据库（预览版）

Azure Cosmos DB 允许在手动或 autopilot 模式下对容器预配吞吐量。 本文介绍 autopilot 模式的优势和用例。

> [!NOTE]
> Autopilot 模式目前以公共预览版提供。 若要为 Azure Cosmos 帐户启用 autopilot 功能，请参阅本文的[启用 autopilot](#enable-autopilot)部分。 只能为新数据库和容器启用 autopilot，而不能用于现有容器和数据库。

除了手动预配吞吐量以外，现在还可以在 autopilot 模式下配置 Azure Cosmos 容器。 在 autopilot 模式下配置的 Azure Cosmos 容器和数据库将会**根据应用程序的需求自动即时缩放预配的吞吐量，且不影响 SLA**。

不再需要手动管理预配的吞吐量或处理速率限制问题。 在 autopilot 模式下配置的 Azure Cosmos 容器可以即时缩放以响应工作负荷，而不会全局性地影响工作负荷的可用性、延迟、吞吐量或性能。 在利用率较高的情况下，在 autopilot 模式下配置的 Azure Cosmos 容器可以纵向扩展或缩减，且不影响正在进行的操作。

在 autopilot 模式下配置容器和数据库时，需要指定不可超过的最大吞吐量 `Tmax`。 然后，容器可在 `0.1*Tmax < T < Tmax` 范围内根据工作负荷需求即时缩放。 换句话说，容器和数据库可根据工作负荷的需要即时缩放，其最大吞吐量值为已配置的最大吞吐量值的10%，最大值为配置的最大吞吐量值。 随时可以更改 autopilot 数据库或容器上的最大吞吐量 (Tmax) 设置。 借助 autopilot 选项，每个容器或数据库的 400 RU/s 最小吞吐量将不再适用。

在 autopilot 预览过程中，对于容器或数据库上的指定最大吞吐量，系统允许在计算的存储限制内操作。 如果超过了存储限制，则最大吞吐量会自动调整为较高的值。 使用 autopilot 模式的数据库级别吞吐量时，数据库中允许的容器数将计算如下：（0.001 * 最大吞吐量）。 例如，如果你预配 20000 autopilot RU/s，则数据库可以具有20个容器。

## <a name="benefits-of-autopilot-mode"></a>Autopilot 模式的优势

在 autopilot 模式下配置的 Azure Cosmos 容器具有以下优势：

* **简单：** Autopilot 模式下的容器消除了为各种容器手动管理预配吞吐量（ru）和容量的复杂性。

* **可伸缩：** Autopilot 模式中的容器会根据需要无缝缩放预配的吞吐量容量。 客户端连接和应用程序不会中断，且不影响任何现有的 SLA。

* **经济高效：** 使用在 autopilot 模式下配置的 Azure Cosmos 容器时，只需为工作负荷每小时需要的资源付费。

* **高可用性：** Autopilot 模式下的 Azure Cosmos 容器使用相同的全球分布式容错、高可用后端，以确保数据持久性和高可用性。

## <a name="use-cases-of-autopilot-mode"></a>Autopilot 模式的用例

在 autopilot 模式下配置的 Azure Cosmos 容器的用例包括：

* **可变工作负荷：** 当你运行使用时间最长的应用程序时，每日最多使用1小时到几个小时，或每年数次。 示例包括人力资源、预算和运营报告应用程序。 对于这种情况，可以使用在 autopilot 模式下配置的容器，而不再需要手动根据峰值或平均容量进行预配。

* **不可预知的工作负荷：** 当你运行的工作负荷在一整天都有数据库使用情况，但也很难预测活动的高峰。 示例包括当天气预报发生变化时出现活动浪涌的交通信息网站。 在 autopilot 模式下配置的容器会调整容量以满足应用程序峰值负载的需求，并在活动浪涌结束时缩减容量。

* **新应用程序：** 如果要部署新应用程序，并且不确定所需的预配吞吐量（即，多个 ru）。 如果在 autopilot 模式下配置容器，可以根据应用程序的容量需求和要求自动缩放。

* 不常用的**应用程序：** 如果你有一个应用程序，该应用程序每天或每周或每月只使用几个小时，如一个小容量应用程序/web/博客网站。

* **开发和测试数据库：** 开发人员在工作时间使用 Azure Cosmos 帐户，但在夜间或周末不需要它们。 如果在 autopilot 模式下配置容器，它们可以在不使用时缩减为最低的配置。

* **计划的生产工作负荷/查询：** 如果在单个容器上有一系列计划的请求/操作/查询，并且如果有空闲时间段，则可以轻松地执行此操作。 将计划的查询/请求提交到在 autopilot 模式下配置的容器后，它会根据最大需求自动扩展，并运行操作。

以往在解决问题时不仅需要投入大量的时间来进行实施，而且还会在配置或代码方面带来复杂性，此外往往需要人工干预才能解决问题。 Autopilot 模式现成地启用上述方案，使你不再需要担心这些问题。

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>比较 - 在手动模式与 autopilot 模式下配置的容器

|  | 在手动模式下配置的容器  | 在 autopilot 模式下配置的容器 |
|---------|---------|---------|
| **预配的吞吐量** | 手动预配 | 根据工作负荷使用模式自动并即时缩放。 |
| **请求/操作的速率限制 (429)**  | 如果消耗量超过预配的容量，则可能会发生。 | 如果使用的吞吐量在 autopilot 模式下所选的最大吞吐量范围内，则不会发生这种情况。   |
| **容量规划** |  必须对所需的吞吐量进行初始容量规划和预配。 |    无需考虑容量规划。 系统会自动处理容量规划和容量管理。 |
| **定价** | 根据手动预配的 RU/秒吞吐量按小时计费。 | 对于单写入区域帐户，需按小时费率为每小时使用的 autopilot RU/秒吞吐量付费。 <br/><br/>对于包含多个写入区域的帐户，autopilot 不收取额外的费用。 需按小时费率为每小时使用的相同多主机 RU/秒吞吐量付费。 |
| **最适合工作负荷类型** |  可预测的稳定工作负荷|   不可预测的可变工作负荷  |

## <a id="enable-autopilot"></a>从 Azure 门户启用 autopilot

可以通过 Azure 门户中的启用来试用 Azure Cosmos 帐户中的 autopilot。 使用以下步骤启用 autopilot 选项：

1. 登录到[Azure 门户。](https://portal.azure.com)

2. 导航到你的 Azure Cosmos 帐户并打开 "**新建功能**" 选项卡。选择 "**自动试验**" 并进行**注册**，如以下屏幕截图所示：

![在 autopilot 模式下创建容器](./media/provision-throughput-autopilot/enable-autopilot-azure-portal.png)

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>使用 autopilot 模式创建数据库或容器

可以在创建数据库或容器时为其配置 autopilot。 使用以下步骤来新建数据库或容器、启用 autopilot 和指定最大吞吐量。

1. 登录到[Azure 门户](https://portal.azure.com)或[Azure Cosmos 资源管理器。](https://cosmos.azure.com/)

1. 导航到你的 Azure Cosmos 帐户，打开“数据资源管理器”选项卡。

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
