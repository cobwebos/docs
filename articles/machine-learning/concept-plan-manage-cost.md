---
title: 计划和管理成本
titleSuffix: Azure Machine Learning
description: 通过 Azure 门户中的成本分析来计划和管理 Azure 机器学习的成本。 当你生成机器学习模型时，请了解更多节省成本的技巧来降低成本。
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: ae1beeebfddfe250ae20a70c3e78ec32774218d4
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996315"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>规划和管理 Azure 机器学习的成本

本文介绍如何计划和管理 Azure 机器学习的成本。 首先，使用 Azure 定价计算器在添加任何资源之前，帮助规划成本。 接下来，当你添加 Azure 资源时，请查看预估成本。 最后，在使用托管 Azure 机器学习计算群集训练模型时，使用节省成本的技巧。

开始使用 Azure 机器学习资源后，使用成本管理功能来设置预算和监视成本。 还应查看预测的成本并确定支出趋势，以确定你可能想要采取的措施。

了解 Azure 机器学习的成本仅是 Azure 帐单中每月费用的一部分。 如果你正在使用其他 Azure 服务，则需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。 本文介绍如何为 Azure 机器学习规划和管理成本。 熟悉管理 Azure 机器学习的成本后，应用类似的方法来管理订阅中使用的所有 Azure 服务的成本。

训练机器学习模型时，请使用托管 Azure 机器学习计算群集来利用更节省成本的技巧：

* 为自动缩放配置定型群集
* 设置订阅和工作区的配额
* 设置训练运行的终止策略
* 使用低优先级虚拟机（VM）
* 使用 Azure 保留 VM 实例

## <a name="prerequisites"></a>先决条件

成本分析支持各种 Azure 帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 

若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md)。

## <a name="estimate-costs"></a>估算成本

在 Azure 机器学习帐户中创建资源之前，请使用[Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。 在左侧，选择 " **AI + 机器学习**"，然后选择 " **Azure 机器学习**" 开始 "。  

以下屏幕截图通过使用计算器显示成本估算：

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 计算器中的成本估算":::

向工作区添加新资源时，请返回到此计算器，并在此处添加相同资源来更新成本估算。

虽然 Enterprise edition 处于预览阶段，但没有 ML 附加费。 企业版公开上市后，会产生附加费（用于定型和推断）。  有关详细信息，请参阅[Azure 机器学习定价](https://azure.microsoft.com/pricing/details/machine-learning/)。

## <a name="get-cost-alerts"></a>获取成本警报

创建[预算](../cost-management/tutorial-acm-create-budgets.md)以管理成本和创建[警报](../cost-management/cost-mgt-alerts-monitor-usage-spending.md)，以自动通知利益干系人支出异常和超支风险的利益干系人。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，因此它们在总体成本监视策略中非常有用。 但预算和警报的功能可能有限，无法管理单个 Azure 服务成本，因为它们旨在跟踪更高级别的成本。

## <a name="monitor-costs"></a>监视成本

在 Azure 机器学习中使用资源时，会产生成本。 Azure 资源使用情况单位成本随时间间隔（秒、分钟、小时和天）或请求单位使用情况的不同而不同。 一旦开始使用 Azure 机器学习，就会产生成本。 在 Azure 门户的 "[成本分析](../cost-management/quick-acm-cost-analysis.md)" 窗格中查看这些成本。

可以查看不同时间间隔的关系图和表中的成本。 你还可以查看预算和预测成本的成本。 随着时间的推移切换到较长的视图，可帮助确定支出趋势并了解超支可能发生的位置。 如果已创建预算，请查看他们超出的位置。  

你不会看到机器学习的单独服务区域。  相反，你将看到已添加到机器学习工作区的各种资源。

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>使用 Azure 机器学习计算群集（AmlCompute）

随着不断变化的数据，您需要快速、简化的模型定型，并重新训练以维护准确的模型。 但是，持续定型的成本非常高，尤其对于 Gpu 上的深度学习模型。 

Azure 机器学习用户可以使用托管 Azure 机器学习计算群集（也称为 AmlCompute）。 AmlCompute 支持多种 GPU 和 CPU 选项。 AmlCompute 是由 Azure 机器学习代表您的订阅在内部托管的。 它在 Azure IaaS 云规模上提供相同的企业级安全性、合规性和监管。

由于这些计算池位于 Azure IaaS 基础结构内，因此，你可以部署、缩放和管理你的培训，其安全性和符合性要求与基础结构的其余部分相同。  这些部署在你的订阅中进行，并遵循你的管理规则。 了解有关[Azure 机器学习计算](how-to-set-up-training-targets.md#amlcompute)的详细信息。

## <a name="configure-training-clusters-for-autoscaling"></a>配置用于自动缩放的定型群集

根据工作负荷要求自动缩放群集有助于降低成本，因此只需使用所需的内容即可。

AmlCompute 群集旨在根据工作负荷进行动态缩放。 群集可以扩展到配置的最大节点数。 每次运行完成时，群集将释放节点并缩放到配置的最小节点计数。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

你还可以配置节点在缩减之前处于空闲状态的时间量。 默认情况下，"缩减之前的空闲时间" 设置为120秒。

+ 如果执行更少的迭代试验，请缩短此时间以节省成本。 
+ 如果执行高度迭代的开发/测试试验，则可能需要增加时间，以便在每次更改训练脚本或环境后，不会向下扩展和向下缩放。

可以使用[AMLCOMPUTE SDK 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) [AmlCompute CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)和[REST api](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)，在 Azure 门户中配置不断变化的工作负荷要求。

```azure cli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>设置资源配额

AmlCompute 附带了[配额（或限制）配置](how-to-manage-quotas.md#azure-machine-learning-compute)。 此配额为 VM 系列（例如 Dv2 系列、NCv3 系列），并因每个订阅的区域而异。 订阅从较小的默认值开始，你可以使用此设置来控制订阅中可用的 Amlcompute 资源量。 

同时为订阅中的每个工作区配置[工作区级别配额，按 VM 系列](how-to-manage-quotas.md#workspace-level-quota)。 这样，你就可以对每个工作区可能产生的成本进行更细致的控制，并限制某些 VM 系列。 

若要在工作区级别设置配额，请从[Azure 门户](https://portal.azure.com)开始。  选择订阅中的任何工作区，并在左窗格中选择 "**使用情况 + 配额**"。 然后选择 "**配置配额**" 选项卡以查看配额。 你需要订阅范围的权限来设置配额，因为这是一个影响多个工作区的设置。

## <a name="set-run-autotermination-policies"></a>设置运行 autotermination 策略 

在某些情况下，你应该将定型运行配置为限制其持续时间或提前终止。 例如，使用 Azure 机器学习的内置超参数优化或自动机器学习。

下面是几个选项：
* 定义在 RunConfiguration 中`max_run_duration_seconds`调用的参数，以控制在所选计算（本地或远程云计算）上运行的最长持续时间。
* 对于[超参数优化](how-to-tune-hyperparameters.md#early-termination)，请从 Bandit 策略、中间停止策略或截断选择策略中定义早期终止策略。 若要进一步控制超参数扫描，请使用参数`max_total_runs` ， `max_duration_minutes`如或。
* 对于[自动机器学习](how-to-configure-auto-train.md#exit)，使用`enable_early_stopping`标志设置相似的终止策略。 还可以使用属性（ `iteration_timeout_minutes`如`experiment_timeout_minutes`和）来控制运行的持续时间或整个实验的最长持续时间。

## <a name="use-low-priority-vms"></a>使用低优先级 VM

Azure 允许在虚拟机规模集、Batch 和机器学习服务中使用过量的未利用容量作为低优先级 Vm。 这些分配是预 emptible 的，但与专用 Vm 相比，其价格会降低。 通常，我们建议为批处理工作负荷使用低优先级 Vm。 还应在中断可通过重新提交（对于批处理推断）或重新启动（对于深度学习定型，使用检查点）的情况下使用它们。

低优先级 Vm 具有与专用配额值不同的单一配额，即 VM 系列。 了解[有关 AmlCompute 配额的详细信息](how-to-manage-quotas.md)。

采用以下任一方式设置 VM 的优先级：

* 在工作室中，在创建 VM 时选择 "**低优先级**"。

* 在 Python SDK 中，在设置`vm_priority`配置中设置属性。  

    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                               vm_priority='lowpriority',
                                                               max_nodes=4)
    ```

* 使用 CLI，将`vm-priority`：

    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 低优先级 Vm 不适用于计算实例，因为它们需要支持交互式笔记本体验。 

## <a name="use-reserved-instances"></a>使用预留实例

节省计算资源费用的另一种方法是 Azure 保留 VM 实例。 对于此产品/服务，你承诺一年或三年。 这些折扣范围可达72% 的即用即付价格，并直接应用于每月的 Azure 帐单。

Azure 机器学习计算本身就支持保留实例。 如果你购买了一年或三年的预订实例，我们将根据你的 Azure 机器学习托管计算自动应用折扣。


## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：
* [管理和增加资源配额](how-to-manage-quotas.md)
* [通过[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md)管理成本。
* [Azure 机器学习计算](how-to-set-up-training-targets.md#amlcompute)。
