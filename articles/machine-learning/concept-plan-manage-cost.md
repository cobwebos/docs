---
title: 计划和管理成本
titleSuffix: Azure Machine Learning
description: 使用 Azure 门户中的成本分析来计划和管理 Azure 机器学习成本。 生成机器学习模型时，进一步了解节省成本的技巧以降低成本。
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: ae1beeebfddfe250ae20a70c3e78ec32774218d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82996315"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>计划和管理 Azure 机器学习成本

本文介绍如何计划和管理 Azure 机器学习成本。 首先，请在添加任何资源以前使用 Azure 定价计算器来帮助计划成本。 接下来，在添加 Azure 资源时，查看预估成本。 最后，在使用托管 Azure 机器学习计算群集训练模型时，请运用成本节省技巧。

开始使用 Azure 机器学习资源后，使用成本管理功能来设置预算和监视成本。 另外，还应查看预测的成本并识别支出趋势，以确定可能需要实施措施的领域。

请知晓，Azure 机器学习的成本只是 Azure 账单中每月成本的一部分。 如果在使用其他 Azure 服务，需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。 本文介绍如何为 Azure 机器学习计划和管理成本。 在熟悉了如何管理 Azure 机器学习成本之后，应用类似的方法来管理订阅中使用的所有 Azure 服务的成本。

训练机器学习模型时，通过使用托管 Azure 机器学习计算群集来利用更多节省成本的技巧：

* 配置训练群集以实现自动缩放
* 设置订阅和工作区上的配额
* 针对“训练运行”设置终止策略
* 使用低优先级虚拟机 (VM)
* 使用 Azure 虚拟机预留实例

## <a name="prerequisites"></a>先决条件

成本分析支持各种 Azure 帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 

若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md)。

## <a name="estimate-costs"></a>估算成本

在 Azure 机器学习帐户中创建资源之前，使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。 在左侧，选择“AI + 机器学习”，然后选择“Azure 机器学习”，开始操作**** ****。  

以下屏幕截图显示了使用计算器进行成本估算：

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 计算器中的成本估算":::

向工作区添加新资源时，请返回到此计算器，并在此处添加相同资源来更新成本估算。

Enterprise 版预览版无 ML 附加费用。 Enterprise 版正式发布后，将收取附加费（用于训练和推理）。  有关详细信息，请参阅 [Azure 机器学习定价](https://azure.microsoft.com/pricing/details/machine-learning/)。

## <a name="get-cost-alerts"></a>获取成本警报

请创建[预算](../cost-management/tutorial-acm-create-budgets.md)来管理成本，并创建[警报](../cost-management/cost-mgt-alerts-monitor-usage-spending.md)以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 但是，若要管理单个 Azure 服务成本，预算和警报的功能可能有限，因为它们的作用主要是在更宽泛的层面上跟踪成本。

## <a name="monitor-costs"></a>监视成本

将 Azure 机器学习与资源一起使用时，会产生成本。 Azure 资源使用的单位成本随时间间隔（秒、分钟、小时和天）或请求的单位使用量而变化。 一旦开始使用 Azure 机器学习，就会产生成本。 请在 Azure 门户的[成本分析](../cost-management/quick-acm-cost-analysis.md)窗格中查看这些成本。

可以在不同时间间隔的关系图和表中查看相应的成本。 还可以对比预算和预测的成本来查看成本。 切换到随时间推移的较长视图有助于确定支出趋势及了解可能超支的领域。 如果已创建预算，请查看超出预算的领域。  

不会为机器学习单独显示一个服务区域。  而是会显示已添加到机器学习工作区中的各种资源。

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>使用 Azure 机器学习计算群集 (AmlCompute)

随着数据的不断变化，需要快速且精简的模型训练和重训练流程来维持模型准确。 但是，持续训练需要付出一定代价，尤其是 GPU 上的深度学习模型。 

Azure 机器学习用户可以使用托管 Azure 机器学习计算群集（也称为 AmlCompute）。 AmlCompute 支持多种 GPU 和 CPU 选项。 AmlCompute 是由 Azure 机器学习代表你的订阅在内部进行托管的。 它在 Azure IaaS 云规模上提供相同的企业级安全性、合规性和治理功能。

由于这些计算池位于 Azure IaaS 基础结构内，因此，你可以按照与基础结构的其余部分相同的安全性和合规性要求来部署、缩放和管理训练。  这些部署在你的订阅中发生，遵循你的治理规则。 详细了解 [Azure 机器学习计算](how-to-set-up-training-targets.md#amlcompute)。

## <a name="configure-training-clusters-for-autoscaling"></a>配置训练群集以实现自动缩放

根据工作负载要求自动缩放群集有助于降低成本，这样就可以只使用所需的内容。

AmlCompute 群集的设计旨在根据工作负载动态进行缩放。 群集可以纵向扩展到所配置的最大节点数。 每次运行完成后，群集将释放节点并缩放到所配置的最小节点计数。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

还可以配置节点在纵向缩减之前处于空闲状态的时间量。 默认情况下，纵向缩减之前的空闲时间设置为 120 秒。

+ 如果执行迭代较少的试验，请缩短此时间以节省成本。 
+ 如果迭代较多的开发/测试试验，可能需要增加此时间，这样就不用在每次更改训练脚本或环境后为纵向扩展或纵向缩减付费。

可以使用 [AmlCompute SDK 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)、[AmlCompute CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute) 以及 [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable) 来配置 AmlCompute 群集，以适应 Azure 门户中不断变化的工作负载要求。

```azure cli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>设置资源上的配额

AmlCompute 附带一个[配额（或限制）配置](how-to-manage-quotas.md#azure-machine-learning-compute)。 此配额根据 VM 系列（例如 Dv2 系列、NCv3 系列）而定，且因每个订阅的区域而异。 订阅从较小的默认值开始以便于你上手，但使用此设置可以控制在订阅中逐渐增加的 Amlcompute 资源量。 

对于订阅内的每个工作区，还需配置[按 VM 系列的工作区级别配额](how-to-manage-quotas.md#workspace-level-quota)。 这样，你就可以对每个工作区可能产生的成本进行更精细的控制并限制某些 VM 系列。 

若要在工作区级别设置配额，请使用 [Azure 门户](https://portal.azure.com)。  选择订阅中的任何工作区，然后在左窗格中选择“使用量 + 配额”****。 然后选择“配置配额”选项卡以查看配额****。 你需要订阅范围的权限来设置配额，因为这是一个会影响多个工作区的设置。

## <a name="set-run-autotermination-policies"></a>设置“运行自动终止”策略 

在某些情况下，应对“训练运行”进行配置以限制其持续时间或提前终止。 例如，当使用 Azure 机器学习的内置超参数优化或自动化机器学习时。

以下是可执行的几个选择：
* 在 RunConfiguration 中定义名为 `max_run_duration_seconds` 的参数，用于控制一个运行可在所选计算（本地或远程云计算）上持续的最长持续时间。
* 对于[超参数优化](how-to-tune-hyperparameters.md#early-termination)，请在老虎机策略、中间值停止策略或截断选择策略中定义提前终止策略。 若要进一步控制超参数整理，请使用 `max_total_runs` 或 `max_duration_minutes` 等参数。
* 对于[自动化机器学习](how-to-configure-auto-train.md#exit)，请使用 `enable_early_stopping` 标志设置类似的终止策略。 另外，请使用诸如 `iteration_timeout_minutes` 和 `experiment_timeout_minutes` 等属性来控制运行的最长持续时间或整个试验的最长持续时间。

## <a name="use-low-priority-vms"></a>使用低优先级 VM

Azure 允许在虚拟机规模集、Batch 和机器学习服务中将未利用的多余容量作为低优先级 VM 来使用。 这些容量分配是可预先抢占的，其价格比专用 VM 低。 通常，建议为 Batch 工作负载使用低优先级 VM。 如果可通过重新提交（对于 Batch 推理）或通过重启（对于具有检查点的深度学习培训）从中断中恢复，也应使用它们。

低优先级 VM 具有与专用配额值（按 VM 系列）不同的单一配额。 了解[有关 AmlCompute 配额的详细信息](how-to-manage-quotas.md)。

采用以下任一方式设置 VM 优先级：

* 在工作室中，在创建 VM 时选择“低优先级”****。

* 使用 Python SDK，在预配配置中设置 `vm_priority` 属性。  

    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                               vm_priority='lowpriority',
                                                               max_nodes=4)
    ```

* 使用 CLI 设置 `vm-priority`：

    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 低优先级 VM 不适用于计算实例，因为后者需要支持交互式笔记本体验。 

## <a name="use-reserved-instances"></a>使用预留实例

节省计算资源成本的另一种方法是 Azure 虚拟机预留实例。 此产品按一年或三年的期限提供。 其折扣最高可达即用即付价格的 72%，并直接应用于每月 Azure 帐单。

Azure 机器学习计算本身就支持预留实例。 如果你购买了一年或三年的预留实例，将自动根据你的 Azure 机器学习托管计算来应用折扣。


## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：
* [管理和增加资源配额](how-to-manage-quotas.md)
* [使用[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md)管理成本。
* [Azure 机器学习计算](how-to-set-up-training-targets.md#amlcompute)。
