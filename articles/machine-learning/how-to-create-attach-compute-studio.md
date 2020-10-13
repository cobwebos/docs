---
title: '创建定型 & 部署 (studio 计算) '
titleSuffix: Azure Machine Learning
description: 使用工作室为机器学习创建训练和部署计算资源（计算目标）
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: c22593fbd1e1653efa98c760d5bbb73b03761059
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708389"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>在 Azure 机器学习工作室中创建计算目标以进行模型训练和部署

本文介绍了如何在 Azure 机器学习工作室中创建和管理计算目标。  也可以使用以下 SDK 与扩展创建和管理计算目标：

* Azure 机器学习的 Azure 机器学习学习 SDK 或 CLI 扩展
  * [计算实例](how-to-create-manage-compute-instance.md)
  * [计算群集](how-to-create-attach-compute-cluster.md)
  * [Azure Kubernetes 服务群集](how-to-create-attach-kubernetes.md)
  * [其他计算资源](how-to-attach-compute-targets.md)
* 用于 Azure 机器学习的 [VS Code 扩展](how-to-manage-resources-vscode.md#compute-clusters)。


## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用 [Azure 机器学习的免费版或付费版](https://aka.ms/AMLFree)
* 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>什么是计算目标？ 

使用 Azure 机器学习可以在不同的资源或环境（统称为[__计算目标__](concept-azure-machine-learning-architecture.md#compute-targets)）中训练模型。 计算目标可以是本地计算机，也可以是云资源，例如 Azure 机器学习计算、Azure HDInsight 或远程虚拟机。  还可以为模型部署创建计算目标，如[“部署模型的位置和方式”](how-to-deploy-and-where.md)中所述。

## <a name="view-compute-targets"></a><a id="portal-view"></a>查看计算目标

若要查看工作区的所有计算目标，请使用以下步骤：

1. 导航到 [Azure 机器学习工作室](https://ml.azure.com)。
 
1. 在“管理”下，选择“计算” 。

1. 选择顶部的选项卡以显示各类计算目标。

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="查看计算目标的列表":::

## <a name="create-compute-target"></a><a id="portal-create"></a>创建计算目标

遵循上述步骤查看计算目标的列表。 然后使用以下步骤创建计算目标：

1. 在顶部选择对应于将要创建的计算类型的选项卡。

1. 如果你没有计算目标，请选择页面中间的“创建”。
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="查看计算目标的列表":::

1. 如果看到计算资源的列表，请选择列表上方的“+ 新建”。

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="查看计算目标的列表":::


1. 为你的计算类型填写表单：

  * [计算实例](#compute-instance)
  * [计算群集](#amlcompute)
  * [推理群集](#inference-clusters)
  * [附加的计算](#attached-compute)

1. 选择“创建”。

1. 通过在列表中选择计算目标来查看创建操作的状态：

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="查看计算目标的列表":::


### <a name="compute-instance"></a>计算实例

使用[上述步骤](#portal-create)创建计算实例。  然后按如下所示填写表单：

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="查看计算目标的列表":::


|字段  |说明  |
|---------|---------|
|计算名称     |  <li>名称是必须提供的，且长度必须介于 3 到 24 个字符之间。</li><li>有效字符为大小写字母、数字和 **-** 字符。</li><li>名称必须以字母开头</li><li>名称必须在 Azure 区域内的全部现有计算中都是唯一的。 如果选择的名称不是唯一的，则会显示警报</li><li>如果在名称中使用了 **-** 字符，在此字符之后必须至少跟有一个字母</li>     |
|虚拟机类型 |  选择“CPU”或“GPU”。 此类型在创建后无法更改     |
|虚拟机大小     |  在你的区域中，支持的虚拟机大小可能会受到限制。 请查看[可用性列表](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|启用/禁用 SSH 访问     |   默认情况下会禁用 SSH 访问。  SSH 访问 在创建后无法更改。 如果计划使用 [VS Code Remote](how-to-set-up-vs-code-remote.md) 以交互模式进行调试，请确保启用访问权限   |
|高级设置     |  可选。 配置虚拟网络 指定**资源组**、**虚拟网络**和**子网**，以在 Azure 虚拟网络 (vnet) 中创建计算实例。 有关详细信息，请参阅 vnet 的这些[网络要求](how-to-enable-virtual-network.md#compute-instance)。  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> 计算群集

为训练、批量推理或强化学习工作负载创建单个或多个节点计算群集。 使用[上述步骤](#portal-create)创建计算群集。  然后按如下所示填写表单：


|字段  |说明  |
|---------|---------|
|计算名称     |  <li>名称是必须提供的，且长度必须介于 3 到 24 个字符之间。</li><li>有效字符为大小写字母、数字和 **-** 字符。</li><li>名称必须以字母开头</li><li>名称必须在 Azure 区域内的全部现有计算中都是唯一的。 如果选择的名称不是唯一的，则会显示警报</li><li>如果在名称中使用了 **-** 字符，在此字符之后必须至少跟有一个字母</li>     |
|虚拟机类型 |  选择“CPU”或“GPU”。 此类型在创建后无法更改     |
|虚拟机优先级 | 选择“专用”或“低优先级”。  低优先级虚拟机的费用更低，但不能保证计算节点。 其他作业可能会抢先于你的作业执行。
|虚拟机大小     |  在你的区域中，支持的虚拟机大小可能会受到限制。 请查看[可用性列表](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|最小节点数 | 需要预配的节点的最小数量。 如果需要专用数量的节点，请在此处设置所需计数。 将最小值设置为 0 可节省费用，这样在群集空闲时就不需要为任何节点付费。 |
|最大节点数 | 需要预配的节点的最大数量。 提交作业时，计算将自动缩放到此节点计数的最大值。 |
|高级设置     |  可选。 配置虚拟网络 指定**资源组**、**虚拟网络**和**子网**，以在 Azure 虚拟网络 (vnet) 中创建计算实例。 有关详细信息，请参阅 vnet 的这些[网络要求](how-to-enable-virtual-network.md#compute-instance)。   另外请附加[托管标识](#managed-identity)以授予对资源的访问权限     |

#### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> 设置托管标识

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

在群集创建过程中或在编辑计算群集详细信息时，请在“高级设置”中切换“分配托管标识”并指定系统分配的标识或用户分配的标识 。

#### <a name="managed-identity-usage"></a>托管标识用法

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>推理群集

> [!IMPORTANT]
> 将 Azure Kubernetes 服务与 Azure 机器学习配合使用有多个配置选项。 某些场景（如网络）需要额外的设置和配置。 有关将 AKS 与 Azure 机器学习配合使用的详细信息，请参阅[创建和附加 Azure Kubernetes 服务群集](how-to-create-attach-kubernetes.md)。

创建或附加 Azure Kubernetes 服务 (AKS) 群集以用于大规模推理。 使用[上述步骤](#portal-create)创建 AKS 群集。  然后按如下所示填写表单：


|字段  |说明  |
|---------|---------|
|计算名称     |  <li>必须提供名称。 名称必须包含 2 到 16 个字符。 </li><li>有效字符为大小写字母、数字和 **-** 字符。</li><li>名称必须以字母开头</li><li>名称必须在 Azure 区域内的全部现有计算中都是唯一的。 如果选择的名称不是唯一的，则会显示警报</li><li>如果在名称中使用了 **-** 字符，在此字符之后必须至少跟有一个字母</li>     |
|Kubernetes 服务 | 选择“新建”，并填写表单的其余部分。  或者选择“使用现有”，然后从订阅中选择现有的 AKS 群集。
|区域 |  选择将要在其中创建该群集的区域 |
|虚拟机大小     |  在你的区域中，支持的虚拟机大小可能会受到限制。 请查看[可用性列表](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|群集目的  | 请选择“生产”或“开发测试”  |
|节点数 | 节点数乘以虚拟机的核心 (vCPU) 数的结果必须大于等于 12。 |
| 网络配置 | 选择“高级”以在现有虚拟网络中创建计算。 若要详细了解虚拟网络中的 AKS，请参阅[使用专用终结点和虚拟网络的训练和推理过程中的网络隔离](how-to-enable-virtual-network.md#aksvnet)。 |
| 启用 SSL 配置 | 此选项用于针对计算配置 SSL 证书 |

### <a name="attached-compute"></a>附加的计算

若要使用在 Azure 机器学习工作区外部创建的计算目标，必须附加这些计算目标。 附加计算目标会使其可供你的工作区使用。  附加的计算用于为训练附加计算目标 。  推理群集用于为推理附加 AKS 群集 。

请使用[上述步骤](#portal-create)来附加计算。  然后按如下所示填写表单：

1. 输入计算目标的名称。 
1. 选择要附加的计算类型。 并非所有计算类型都可以从 Azure 机器学习工作室附加。 目前，可为训练附加的计算类型包括：
    * 远程 VM
    * Azure Databricks（在机器学习管道中使用）
    * Azure Data Lake Analytics（在机器学习管道中使用）
    * Azure HDInsight

1. 填写表单，并提供必需属性的值。

    > [!NOTE]
    > Microsoft 建议使用 SSH 密钥，因为它们比密码更安全。 密码很容易受到暴力破解攻击。 SSH 密钥依赖于加密签名。 若要了解如何创建用于 Azure 虚拟机的 SSH 密钥，请参阅以下文档：
    >
    > * [在 Linux 或 macOS 上创建和使用 SSH 密钥](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [在 Windows 上创建和使用 SSH 密钥](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. 选择“附加”。 


## <a name="next-steps"></a>后续步骤

在创建目标并将其附加到工作区后，通过 `ComputeTarget` 对象在[运行配置](how-to-set-up-training-targets.md)中使用该目标：

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* 使用计算资源来[提交训练运行](how-to-set-up-training-targets.md)。
* [教程：训练模型](tutorial-train-models-with-aml.md)使用托管计算目标来训练模型。
* 若要构建更好的模型，请了解如何[高效地优化超参数](how-to-tune-hyperparameters.md)。
* 训练模型后，了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。
* [通过 Azure 虚拟网络使用 Azure 机器学习](how-to-enable-virtual-network.md)
