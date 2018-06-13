---
title: 教程 - 使用 Azure 成本管理优化预订实例成本 | Microsoft Docs
description: 本教程介绍如何优化 Azure 和 Amazon Web Services (AWS) 的预订实例成本。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: f0edad58256ecc29e2fd215095e8b5ab13d69ce8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177317"
---
<!-- Intent: As a cloud-consuming administrator, I need to ensure that my reserved instances are optimized for cost and usage
-->

# <a name="tutorial-optimize-reserved-instances"></a>教程：优化预订实例

本教程介绍如何通过成本管理来优化 Azure 和 Amazon Web Services (AWS) 的预订实例成本和使用率。 云服务提供商提供的预订实例是一种长期合同承诺。在该合同中，你对将来使用 VM 的情况进行先期承诺。 与标准的即用即付 VM 定价模型相比，它可以节省大量的资金。 仅当完全使用预订实例的容量时，潜在的节省才会实现。

本教程介绍如何通过成本管理对 Azure 和 AWS 预订实例 (RI) 提供支持， 同时还介绍如何优化预订实例成本。 主要介绍如何确保你充分利用预订。 在本教程中，将：

> [!div class="checklist"]
> * 了解 Azure RI 成本
> * 了解 RI 的优点
> * 优化 Azure RI 成本
> * 查看 RI 成本
> * 评估 Azure RI 成本效益
> * 优化 AWS RI 成本
> * 购买建议的 RI
> * 修改未使用的预订

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- 必须具有 Azure 帐户。
- 必须有 Azure 成本管理的试用注册版或付费订阅版。
- 必须已购买 Azure 或 AWS 中的 RI。

## <a name="understand-azure-ri-costs"></a>了解 Azure RI 成本

购买 Azure 预订 VM 实例时，需通过先期支付的方式购买未来的使用量。 先期支付涵盖的是未来使用下述 VM 的成本：

- 特定类型的 VM
- 特定区域的 VM
- 一到三年期限的 VM
- 达到某个购买数量的 VM。

可以在 Azure 门户的[预订](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中查看购买的 Azure 预订 VM 实例。

术语“Azure 预订 VM 实例”仅适用于定价模型。 它根本不会更改正在运行的 VM。 此术语特定于 Azure，更多情况下是称为“预订实例”或“预订”。 已购买的预订实例不适用于特定 VM，而是适用于任何匹配的 VM。 例如，某个 VM 类型的预订，该类型在你为购买的预订选择的区域中运行。

购买的预订实例仅适用于基本硬件， 不涵盖 VM 的软件许可证。 例如，你可能预订了一个实例，同时你有一个运行 Windows 的匹配 VM。 预订实例仅涵盖 VM 的基础成本。 在此示例中，你需要支付任何必需 Windows 许可证的全价。 若要获取操作系统或其他在 VM 上运行的软件的折扣，应考虑使用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit)。 混合权益为软件许可证提供的折扣类型类似于预订实例为基础 VM 提供的折扣类型。

预订实例使用率不直接影响成本。 换言之，运行 VM 的时候，不管 CPU 使用率是 100% 还是 0%，其效果是相同的：你预付的是 VM 分配费用，不是实际使用率费用。

让我们看看与预订实例相比，标准的按需 VM 使用与成本的关系如何（见下图）：

![按需成本与预订实例成本对比](./media/tutorial-optimize-reserved-instances/azure01.png)



红条表示购买预订实例后的累计成本。 只支付一次性费用。 VM 的使用免费。 蓝条表示同一 VM 在使用即用即付或按需定价模型时的累计成本。 在 VM 使用情况图的第 7 个月和第 8 个月左右的地方，有一个“保本点”。 在此示例中，你从第 8 个月开始省钱。

## <a name="benefits-of-ris"></a>RI 的优点

购买的每个预订实例适用于特定大小和位置的 VM。 例如，在“美国西部”位置运行的 D2s\_v3，如下图所示：

![Azure 预订实例详细信息](./media/tutorial-optimize-reserved-instances/azure02.png)

当 VM 运行的小时数足够长，达到了预订保本点后，购买预订实例就变得对用户有利。 VM 必须与预订实例的大小和位置相符。 例如，在上一图表中，保本点大约在 7 个半月的位置。 因此，当与预订相符的 VM 运行至少 7.5 月 \* 30 天 \* 24 小时 = 5,400 小时后，购买的预订实例就会对用户有利。 如果匹配的 VM 的运行时间不到 5,400 小时，则与即用即付相比，预订显得更昂贵。

保本点可能因 VM 大小和位置而异， 并且还取决于协商的 VM 即用即付价格。 在进行购买之前，应根据自己的情况查看相应的保本点。

购买预订时需要考虑的另一点是预订实例范围。 该范围决定了预订权益是共享的，还是适用于特定的订阅。 共享预订实例随机应用于所有订阅中第一个被发现的匹配 VM。

共享购买范围是最灵活的，建议尽可能选择它。 使用共享范围时，使用所有预订实例的机会将显著提高。 不过，当某个订阅的所有者为预订实例付款时，该所有者可能无法进行选择，只能购买“单一预订”范围的预订实例。

## <a name="optimize-azure-ri-costs"></a>优化 Azure RI 成本

Azure 成本管理通过以下方式支持预订实例和混合权益：

- 显示与定价模型相关联的成本
- 跟踪 RI 使用情况
- 评估 RI 影响
- 按策略分配 RI 成本

在购买预订实例之前，应该执行的首个操作是评估进行 RI 购买的影响：

- 它的成本会是多少？
- 你可以省多少钱？
- 保本点在什么位置？

“预订实例购买影响”报表可能会提供这些问题的答案。

## <a name="assess-azure-ri-cost-effectiveness"></a>评估 Azure RI 成本效益

在 Cloudyn 门户中，导航到“优化器” > “RI 比较”，然后选择“预订实例购买影响”。

在“预订实例购买影响”报表中，选择 VM 大小（实例类型）、位置（区域）、预订期限、数量以及预期的运行时。 然后即可评估购买是否会省钱。

例如，如果为类型为 DS1\_v2、区域为“美国东部”的 VM 购买了一个预订，且该 VM 全天候运行了一整年的时间，则每年可以节省 $369.48。 保本点在五个月的时候出现。 参看下图：

![Azure 预订实例保本点](./media/tutorial-optimize-reserved-instances/azure03.png)

但是，如果该 VM 只在 50% 的时间运行，则保本点会在 10 个月的时候出现，则每年只节省 $49.74。 在此示例中，你可能不会因购买该实例类型的预订而受益。 参看下图：

![Azure 中的保本点](./media/tutorial-optimize-reserved-instances/azure04.png)

## <a name="view-ri-costs"></a>查看 RI 成本

购买预订时，需一次性付款。 可以在成本管理中通过两种方式来查看付款：

- 实际成本
- 摊销成本

### <a name="actual-reserved-instance-cost"></a>实际预订实例成本

“实际成本分析”和“时段分析”报表显示的是为预订支付的总金额，从购买的那个月算起。 可以通过这两个报表了解某个时段的实际支出。

在 Cloudyn 门户中导航到“成本” > “成本分析”，然后选择“实际成本分析”或“时段实际成本”。 接下来设置筛选器。 例如，直接筛选 Azure/VM 服务，并按“资源类型”和“价格模型”分组。 参看下图：

![预订实例的实际成本](./media/tutorial-optimize-reserved-instances/azure05.png)

可以按服务（在此示例中为 **Azure/VM**）筛选，并按“价格模型”和“资源类型”分组，如下图所示：

![“实际成本”报表组和筛选器](./media/tutorial-optimize-reserved-instances/azure06.png)

也可分析已付款的类型，例如一次性费用、使用费用、许可费用。

### <a name="amortized-reserved-instance-cost"></a>摊销预订实例成本

你先期支付的费用显示在购买 RI 时对应的购买月份中。 它不显示在后续发票中。 因此，查看每月使用情况可能会误导你。 每月的实际成本是每月的使用费用加上以前支付的一次性费用的按比例（摊销）部分。 可以通过“摊销成本”报表了解实际情况。

计算摊销的预订实例成本时，先获取预订的一次性费用，然后将其按预订期限进行摊销。 在“实际成本”报表中，一次性费用显示在购买预订时的那一个月中。 每日和每月支出不显示在部署的实际成本中。 “摊销成本”报表按时间段显示部署的实际成本。  只能通过“摊销成本”报表来查看真实的成本趋势。 也只能通过“摊销成本”报表来预测未来的支出。

在“实际成本”报表中，在 11 月 16 日出现了一个金额为 $747 的 RI 购买峰值。 在“摊销成本”报表（见下图）中，在 11 月 16 日出现了一个分摊的每日成本。 从 11 月 17 日开始，看到的摊销 RI 成本为 $747/365 = $2.05。 偶然情况下，你还会注意到购买的预订未使用，这时就可以切换到其他 VM 大小，以便进行优化。

若要进行查看，请导航到“成本” > “成本分析”，然后选择“摊销成本分析”或“时段摊销成本”。

![摊销预订实例成本](./media/tutorial-optimize-reserved-instances/azure07.png)

## <a name="optimize-aws-ri-costs"></a>优化 AWS RI 成本

预订实例是开放式的承诺。 预订实例在持续使用 VM 的情况下很有用，因为预订实例比按需实例价格低。 但是，预订实例的使用需要达到足够的量。 承诺是使用资源（通常为 VM）一定的时间（一到三年）。 承诺购买时，需以预订方式预付资源款。 但是，你可能并非始终能够完全使用在预订中承诺使用的量。

例如，你可能会在进行环境评估后，认定你在去年经常运行 20 个标准的 D2 实例。 你可以为它们购买预订，这样就可能节省大笔资金。 在另一示例中，你可能已承诺在该年使用十个 MA4 实例。 但你目前可能只使用了五个。 这两个示例都是说明 RI 使用效率不高的例子。 使用 Cloudyn 优化报表时，可以通过两种方式来优化预订实例的成本：

- 根据历史使用情况查看购买建议，了解可以进行哪些购买
- 修改未使用的预订

可以根据“EC2 RI 购买建议”和”EC2 当前未使用预订”报表改进预订实例使用情况和成本。

## <a name="buy-recommended-ris"></a>购买建议的 RI

Cloudyn 会比较按需实例使用情况，并会将其与可能的预订实例进行比较。 如果发现哪些地方可以节省资金，它就会在“EC2 购买建议”报表中显示其建议。

在门户顶部的报表菜单中，单击“优化器” > “定价优化” > “EC2 RI 购买建议”。

下图显示报表中的购买建议。

![购买建议](./media/tutorial-optimize-reserved-instances/aws01.png)

在此示例中，Cloudyn\_A 帐户有 32 条预订实例购买建议。 如果遵循所有购买建议，则可能每年节省 $137,770。 请记住，Cloudyn 提供的购买建议假定运行工作负荷的用量会始终保持一致。

若要查看详细信息，了解每个购买的建议原因，请单击“理由”下的加号 ( **+** )。 下面是列表中第一个建议的示例。

![购买理由](./media/tutorial-optimize-reserved-instances/aws02.png)

上一示例显示，运行按需工作负荷每年的成本是 $90,456。 但是，如果提前购买预订，则同一工作负荷的成本是 $56,592，每年节省 $33,864。

单击“EC2 RI 购买影响”旁边的加号即可查看一年中的保本点，大致了解何时实现购买投资目标。 在以下示例中，在购买后大约八个月后，按需累计成本开始超过 RI 累计成本：

![购买影响](./media/tutorial-optimize-reserved-instances/aws03.png)

在该时间点开始省钱。

可以查看“时段实例数”，验证提供的购买建议的准确性。 在此示例中可以看到，在过去 30 天内，对于该工作负荷来说，平均使用的实例数是 6。

![时段实例数](./media/tutorial-optimize-reserved-instances/aws04.png)

## <a name="modify-unused-reservations"></a>修改未使用的预订

在许多云资源使用者的计算环境中，未使用的预订很常见。 如果出现未使用的预订，可以根据当前需求修改预订，确保充分使用未使用的预订，这样可以节省资金。 例如，你可能有一个订阅，其中包含在 Linux 上运行的标准 D3 实例。 如果无法充分利用预订，则可更改实例类型。 也可将未使用的资源移至其他预订或其他帐户。

AWS 针对特定的可用性区域和地区销售预订实例。 如果已针对特定的可用性区域购买了预订实例，则不能在区域之间移动预订。 但是，可以根据“EC2 当前未使用的预订”报表轻松地在区域之间移动地区预订实例。 也可以对它们进行修改，使它们有一个地区范围，然后它们就可以跨所有可用性区域应用匹配的实例。

在门户顶部的报表菜单中，单击“优化器” > “效率低下” > “EC2 当前未使用的预订”。

下图显示的报表包含未使用的预订实例。

![未使用的预订](./media/tutorial-optimize-reserved-instances/unused-ri01.png)

单击“详细信息”下的加号，查看特定预订的预订详细信息。

![未使用的预订的详细信息](./media/tutorial-optimize-reserved-instances/unused-ri02.png)

在前一示例中，各种可用性区域总共有 77 个未使用的预订。 第一个预订有 51 个未使用的实例。 在列表中向下查看，可能会发现预订实例修改，这些修改可以在 **us-east-1c** 可用性区域中使用 **m3.2xlarge** 实例类型来进行。

对于列表中的第一个预订，请单击“修改”以打开“修改 RI”页，以便显示有关预订的数据。

![修改 RI](./media/tutorial-optimize-reserved-instances/unused-ri03.png)

可以修改的预订实例已列出。 在以下示例图中，有 51 个可以修改的未使用预订，但在两次预订之间，却需要 54 个预订。 如果修改未使用的预订，以便将其全部用完，则四个实例将继续按需运行。 就此示例来说，请对未使用的预订进行拆分，使第一个预订使用 30 个实例，第二个预订使用 21 个实例。

单击第一个预订条目所对应的加号，将“预订数量”设置为 30。 对于第二个条目，请将预订数量设置为 **21**，然后单击“应用”。

![更改预订数量](./media/tutorial-optimize-reserved-instances/unused-ri04.png)

预订的所有未使用的实例都已充分利用，51 个实例不再按需运行。 在此示例中，你通过显著减少按需使用量并使用已经付费的预订，为组织节省了资金。

## <a name="next-steps"></a>后续步骤

在本教程中，你已成功完成以下任务：

> [!div class="checklist"]
> * 了解 Azure RI 成本
> * 了解 RI 的优点
> * 优化 Azure RI 成本
> * 查看 RI 成本
> * 评估 Azure RI 成本效益
> * 优化 AWS RI 成本
> * 购买建议的 RI
> * 修改未使用的预订


请转到下一教程，了解对数据访问的控制。

> [!div class="nextstepaction"]
> [控制对数据的访问](tutorial-user-access.md)
