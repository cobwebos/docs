---
title: Azure 实验室服务的成本管理指南
description: 了解查看 Lab 服务成本的不同方法。
author: rbest
ms.author: rbest
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: fbbaf4a3646260fc09467e214b82fd0213415635
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445298"
---
# <a name="cost-management-for-azure-lab-services"></a>Azure 实验室服务的成本管理

成本管理可分为两个不同的区域：成本估算和成本分析。  在设置实验室时，会进行成本估计，以确保实验室的初始结构适合于预期预算。  成本分析通常在月底发生，以分析成本并确定下个月的必要操作。

## <a name="estimating-the-lab-costs"></a>估计实验室成本

每个实验室仪表板都有**成本 & 计费**部分，该部分对实验室将按月收费的内容进行大致估计。  成本估算将使用用户的最大数量和每小时的预估成本来汇总小时使用量。  若要获取实验室所需的最准确估计值（包括[计划](how-to-create-schedules.md)），则仪表板将反映预估成本。  

这种估计可能不是所有可能的成本，但有几个资源并未包括在内。  模板准备成本不考虑成本估算。  创建模板所需的时间可能会有很大差异。 运行模板的成本与每小时的总体实验室成本相同。 任何[共享图像库](how-to-use-shared-image-gallery.md)费用不包含在实验室仪表板中，因为库可以在多个实验室之间共享。  最后，当实验室创建者启动计算机时产生的小时数从此估计值中排除。

> [!div class="mx-imgBorder"]
> ![仪表板成本估算](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyzing-previous-months-usage"></a>分析上个月使用情况

成本分析用于查看前几个月的使用情况，以帮助确定实验室的任何调整。  可以在[订阅成本分析](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)中找到过去成本的细分。  在 Azure 门户中，你可以在上面的搜索字段中键入 "订阅"，然后选择 "订阅" 选项。  

> [!div class="mx-imgBorder"]
> ![订阅搜索](./media/cost-management-guide/subscription-search.png)

选择要查看的特定订阅。

> [!div class="mx-imgBorder"]
> ![订阅选择](./media/cost-management-guide/subscription-select.png)

 在 "**成本管理**" 下的左窗格中选择 "成本分析"。

 > [!div class="mx-imgBorder"]
> ![订阅成本分析](./media/cost-management-guide/subscription-cost-analysis.png)

此仪表板将允许进行深度的成本分析，其中包括按计划导出到不同文件类型的功能。  成本管理具有众多功能，有关详细信息，请参阅[成本管理计费概述](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

按资源类型筛选： `microsoft.labservices/labaccounts` 将仅显示与实验室服务关联的成本。

## <a name="understanding-the-usage"></a>了解用法

下面是成本分析的示例。

> [!div class="mx-imgBorder"]
> ![订阅成本分析](./media/cost-management-guide/cost-analysis.png)

默认情况下，有六列：资源、资源类型、位置、资源组名称、标记、成本。  资源列包含有关实验室帐户、实验室名称和 VM 的信息。  实验室帐户/实验室名称/默认值为的行是实验室的成本，可以在第二行和第三行中查看。  使用的 Vm 将在实验室帐户/实验室名称/默认/VM 名称下收费。  在此示例中，添加第二行的第一行（以 "aaalab/dockerlab" 开头）将在 "aaalab" 实验室帐户中提供实验室 "dockerlab" 的总成本。

若要获取共享的映像库信息，请将资源类型更改为 `Microsoft.Compute/Galleries` ，这将为你提供映像库的总体成本。  共享映像库可能不会显示，具体取决于存储库的位置。

> [!NOTE]
> 共享映像库连接到实验室帐户。  这意味着多个实验室可以使用同一个映像。

## <a name="separating-costs"></a>分隔成本

某些大学使用实验室帐户和资源组来分隔不同的类。  每个类都具有自己的实验室帐户和资源组。 在 "成本分析" 窗格中，基于资源组名称添加一个筛选器，该筛选器的资源组名称与类的相应资源组名称相同，并且只有该类的成本才可见。  这使得在查看成本时，不同的类之间的略图更清晰。  成本分析的[计划导出](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)功能允许将每个类的成本下载到单独的文件中。

## <a name="managing-costs"></a>管理成本

根据类的类型，可以通过多种方法来管理开销，以减少在没有学生使用计算机的情况下运行 Vm 的情况。

### <a name="auto-shutdown-on-disconnect"></a>断开连接时自动关闭

在创建实验室时，实验室所有者可以将实验室中的 Vm 设置为在[与 VM 的 RDP 连接断开连接时关闭](how-to-enable-shutdown-disconnect.md)。  此设置可降低学生断开连接，但忘记停止 VM 的情况。

### <a name="quota-vs-scheduled-time"></a>配额与计划时间

了解[配额时间](classroom-labs-concepts.md#quota)与[计划时间](classroom-labs-concepts.md#schedules)将允许实验室所有者配置实验室，以便更好地满足教授和学生的需求。  计划时间是所有学生 Vm 均已启动并可用于连接到的设置时间。  通常情况下，当所有学生都具有自己的 VM，并在一天中的设置时间（如课堂时间）后，就会使用此方案。  缺点是所有学生 Vm 均已启动，并会产生成本，即使学生未登录到 VM 也是如此。  配额时间是分配给每个学生的时间，他们可以根据自己的意愿使用该时间，并经常用于独立研究。 在学生启动 VM 之前，不会启动 Vm。  

实验室可以使用配额时间、计划时间或二者的组合。 如果某个类不需要计划时间，则只使用配额时间来最有效地利用 Vm。

### <a name="scheduled-event---stop-only"></a>计划事件-仅停止

在计划中，你可以添加 "仅停止" 事件类型，它将在特定时间停止所有计算机。  某些实验室所有者已设置每日午夜的 "仅停止" 事件，以降低学生忘记关闭正在使用的 VM 时的成本和配额使用量。  此类事件的缺点是，即使学生正在使用 VM，也会关闭所有 Vm。

### <a name="other-costs-related-to-labs"></a>与实验室相关的其他成本 

有一些成本不会滚入实验室服务，但可以绑定到实验室服务。  共享映像库可以连接到实验室，但不会显示在实验室服务成本下，并会产生成本。  为了帮助保持总体成本降低，应从库中删除所有未使用的映像，因为映像具有 "继承存储成本"。  虚拟网络（VNet）在删除实验室后，实验室可以连接到其他 Azure 资源，则应删除 VNet 和其他资源。

## <a name="conclusion"></a>结束语

但愿以上信息可让您更好地了解使用成本，以及如何使用这些工具来减少额外成本。
