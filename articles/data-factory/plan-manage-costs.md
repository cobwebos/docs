---
title: 计划和管理 Azure 数据工厂的成本
description: 本文介绍如何计划和管理 Azure 数据工厂的成本
documentationcenter: ''
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: ca76563475dfbf8d35595c1de3cdee37f80e3ce1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83690233"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>计划和管理 Azure 数据工厂的成本

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure 数据工厂是为实现云规模缩放而构建的无服务器和弹性数据集成服务。  也就是不需要为固定大小的计算规划峰值负载；可以指定每个操作按需分配的资源量，从而以可缩放性更强的方式设计 ETL 进程。 此外，ADF 基于消耗计费，即仅为所用的部分付费。

本文介绍如何计划和管理 Azure 数据工厂的成本。

*   首先，在 ETL 项目开始时会进行概念证明，并结合每个管道的消耗和定价计算器来估算成本。
*   在将管道部署到生产环境后，可以使用成本管理功能来设置预算并监视成本。 还可以查看预测出的成本并确定支出趋势。
*   此外，还可以查看关于每个管道的消耗和每个活动的消耗的信息，以了解哪些管道以及哪些活动的成本最高，并确定降低成本时的候选项。

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>使用管道和活动的运行消耗量以及定价计算器来估算成本

可以使用 [ADF 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=data-factory)来估算在 Azure 数据工厂中运行 ETL 工作负载的成本。  若要使用计算器，必须输入详细信息，例如活动运行数、数据集成单位小时数、用于数据流的计算类型、核心数、实例数、执行持续时间等。

关于定价计算器的常见问题之一是应将哪些值用作输入。  在概念证明阶段，可以使用示例数据集执行试验运行，以了解各项 ADF 计量的消耗情况。  然后，根据示例数据集的消耗，可以得出完整数据集和操作化计划的消耗。

> [!NOTE]
> 以下示例中使用的是假定价格，并不是指实际定价。

例如，假设每日需要将 1 TB 的数据从 AWS S3 移动到 Azure Data Lake Gen2。  可以对移动 100 GB 数据进行 POC，以度量数据引入吞吐量并了解相应的计费消耗。

下面是复制活动运行详细信息的示例（实际数据会因特定数据集的形态、网络速度、S3 帐户的流出量限制、ADLS Gen2 上的流入量限制和其他因素而异）。

![S3 复制运行](media/plan-manage-costs/s3-copy-run-details.png)

通过利用[管道运行级别的消耗监视](#monitor-consumption-at-pipeline-run-level)，可以查看相应的数据移动计量消耗量：

![S3 复制管道消耗](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

因此，在一个月中每天移动 1 TB 所需的 DIU 小时总数为：

1.2667（DIU 小时）*（1 TB/100 GB）* 30（一个月中的天数）= 380 DIU 小时

现在可以将 30 个活动运行和 380 DIU 小时插入 ADF 定价计算器中以估算每月的费用：

![S3 复制定价计算器](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>使用预算和成本警报

可以创建[预算](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets)来管理成本，并创建警报以自动通知利益干系人支出异常和超支风险。  警报基于与预算和成本阈值相比的支出。  创建预算时，可以在订阅级别执行此操作，也可以在较低粒度通过添加其他筛选条件（例如资源 ID 和计量名称）来执行此操作。  但不能为工厂中的单个管道创建预算。

## <a name="monitor-costs-at-factory-level"></a>在工厂级别监视成本

开始使用 Azure 数据工厂时，可以在 Azure 门户的[成本分析](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis)窗格中看到产生的成本。

1. 若要查看[成本分析](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis)，请打开“成本管理 + 计费”窗口，从菜单中选择“成本管理”，然后选择“打开成本分析”  。
2. 默认视图显示当前月份的累计成本。  可以切换到不同的时间范围和不同的粒度（例如每日或每月）。
3. 若要缩小范围以查看单项服务（如 Azure 数据工厂）的成本，选择“添加筛选器”，然后选择“服务名称” 。  然后从列表中选择“Azure 数据工厂 v2”。
4. 可以添加其他筛选器，以分析特定工厂实例和特定的 ADF 计量粒度对应的成本。

   ![成本分析](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>监视管道运行级别的消耗情况

根据管道中的活动类型、移动和转换的数据量以及转换的复杂性，执行管道时将在 Azure 数据工厂中使用不同的计费计量。

在 Azure Data Factory 用户体验中，你可以查看各个管道运行的不同计量的消耗量。 若要打开监视体验，请选择 [Azure 门户](https://portal.azure.com/)的“数据工厂”边栏选项卡中的“监视和管理”磁贴。 如果已进入 ADF 用户体验，请单击左侧边栏上的“监视”图标。 默认监视视图是一个管道运行的列表。

单击管道名称旁边的“消耗”按钮将显示一个弹出窗口，其中显示了基于管道中所有活动的聚合量的管道运行的消耗。

![管道运行消耗](media/plan-manage-costs/pipeline-run-consumption.png)

![管道消耗详细信息](media/plan-manage-costs/pipeline-consumption-details.png)

“管道运行消耗”视图显示特定管道运行的每项 ADF 计量对应的使用量，但它不显示实际费用，因为收取的金额取决于你拥有的 Azure 帐户类型以及使用的货币类型。  若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data)。

## <a name="monitor-consumption-at-activity-run-level"></a>监视管道运行级别的消耗情况
了解管道运行级别的聚合消耗后，有时需要进一步深入了解并确定管道中成本最高的活动。

若要查看活动运行级别的消耗情况，请转到数据工厂中的“创作和监视”UI。 在“监视”选项卡中，可以查看管道运行列表，单击管道名称链接可以查看管道运行中的活动运行列表 。  单击活动名称旁边的“输出”按钮，然后在 JSON 输出中查找 billableDuration 属性 ：

下面是复制活动运行中的输出示例：

![复制输出](media/plan-manage-costs/copy-output.png)

下面是一个“映射数据流”活动运行中的输出示例：

![数据流输出](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解关于 Azure 数据工厂定价方式的详细信息：

- [Azure 数据工厂定价页](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [通过示例了解 Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/pricing-concepts)
- [Azure 数据工厂定价计算器](https://azure.microsoft.com/pricing/calculator/?service=data-factory)
