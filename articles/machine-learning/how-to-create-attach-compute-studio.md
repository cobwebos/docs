---
title: 在工作室中创建计算资源
titleSuffix: Azure Machine Learning
description: 使用 studio 创建培训和部署计算资源 (计算目标) 用于机器学习
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: 2e32eb9a2a13b8e247388e8da80dd5f5967fdc6d
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147374"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>在 Azure 机器学习 studio 中创建模型定型和部署的计算目标
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何在 Azure Machine studio 中创建和管理计算目标。  还可以通过以下方式创建和管理计算目标：

* [Azure 机器学习学习 SDK](how-to-create-attach-compute-sdk.md)， 
* 适用于 Azure 机器学习的[CLI 扩展](reference-azure-machine-learning-cli.md#resource-management)
* Azure 机器学习的 [VS Code 扩展](how-to-manage-resources-vscode.md#compute-clusters) 。


## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用 [Azure 机器学习的免费版或付费版](https://aka.ms/AMLFree)
* 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>什么是计算目标？ 

使用 Azure 机器学习可以在不同的资源或环境（统称为[__计算目标__](concept-azure-machine-learning-architecture.md#compute-targets)）中训练模型。 计算目标可以是本地计算机，也可以是云资源，例如 Azure 机器学习计算、Azure HDInsight 或远程虚拟机。  还可以为模型部署创建计算目标，如[“部署模型的位置和方式”](how-to-deploy-and-where.md)中所述。

## <a name="view-compute-targets"></a><a id="portal-view"></a>查看计算目标

若要查看工作区的所有计算目标，请使用以下步骤：

1. 导航到 [Azure 机器学习工作室](https://ml.azure.com)。
 
1. 在 " __管理__" 下，选择 " __计算__"。

1. 选择顶部的选项卡以显示每种类型的计算目标。

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="查看计算目标的列表":::

## <a name="create-compute-target"></a><a id="portal-create"></a>创建计算目标

遵循上述步骤查看计算目标的列表。 然后使用以下步骤创建计算目标：

1. 选择与将创建的计算类型对应的顶部的选项卡。

1. 如果没有计算目标，请在页面的中间选择 "  **创建** "。
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="创建计算目标":::

1. 如果看到计算资源列表，请在列表上方选择 " **+ 新建** "。

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="选择新":::


1. 填写计算类型的窗体：

  * [计算实例](#compute-instance)
  * [计算群集](#amlcompute)
  * [推理群集](#inference-clusters)
  * [附加计算](#attached-compute)

1. 选择“创建” 。

1. 通过在列表中选择计算目标来查看创建操作的状态：

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="查看列表中的计算状态":::


### <a name="compute-instance"></a>计算实例

使用 [上述步骤](#portal-create) 创建计算实例。  然后填写表单，如下所示：

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="新建计算实例":::


|字段  |说明  |
|---------|---------|
|计算名称     |  <li>名称是必须提供的，且长度必须介于 3 到 24 个字符之间。</li><li>有效字符为大小写字母、数字和 **-** 字符。</li><li>名称必须以字母开头</li><li>名称必须在 Azure 区域内的全部现有计算中都是唯一的。 如果选择的名称不是唯一的，则会显示警报</li><li>如果在名称中使用了 **-** 字符，在此字符之后必须至少跟有一个字母</li>     |
|虚拟机类型 |  选择“CPU”或“GPU”。 此类型在创建后无法更改     |
|虚拟机大小     |  在你的区域中，支持的虚拟机大小可能会受到限制。 请查看[可用性列表](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|启用/禁用 SSH 访问     |   默认情况下会禁用 SSH 访问。  SSH 访问 在创建后无法更改。 如果计划使用 [VS Code Remote](how-to-set-up-vs-code-remote.md) 以交互模式进行调试，请确保启用访问权限   |
|高级设置     |  可选。 配置虚拟网络 指定**资源组**、**虚拟网络**和**子网**，以在 Azure 虚拟网络 (vnet) 中创建计算实例。 有关详细信息，请参阅 vnet 的这些 [网络要求](how-to-enable-virtual-network.md#compute-instance) 。  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> 计算群集

为您的培训、batch 推断或强化学习工作负荷创建单个或多个节点计算群集。 使用 [上述步骤](#portal-create) 来创建计算群集。  然后填写表单，如下所示：


|字段  |说明  |
|---------|---------|
|计算名称     |  <li>名称是必须提供的，且长度必须介于 3 到 24 个字符之间。</li><li>有效字符为大小写字母、数字和 **-** 字符。</li><li>名称必须以字母开头</li><li>名称必须在 Azure 区域内的全部现有计算中都是唯一的。 如果选择的名称不是唯一的，则会显示警报</li><li>如果在名称中使用了 **-** 字符，在此字符之后必须至少跟有一个字母</li>     |
|虚拟机类型 |  选择“CPU”或“GPU”。 此类型在创建后无法更改     |
|虚拟机优先级 | 选择 " **专用** " 或 " **低" 优先级**。  低优先级虚拟机的费用更低，但不能保证计算节点。 作业可能被抢占。
|虚拟机大小     |  在你的区域中，支持的虚拟机大小可能会受到限制。 请查看[可用性列表](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|最小节点数 | 要预配的节点的最小数目。 如果需要专用数量的节点，请在此处设置此计数。 将最小值设置为0，以便在群集处于空闲状态时无需支付任何节点的费用。 |
|最大节点数 | 要预配的节点的最大数目。 提交作业时，计算将自动缩放到此节点计数的最大值。 |
|高级设置     |  可选。 配置虚拟网络 指定**资源组**、**虚拟网络**和**子网**，以在 Azure 虚拟网络 (vnet) 中创建计算实例。 有关详细信息，请参阅 vnet 的这些 [网络要求](how-to-enable-virtual-network.md#compute-instance) 。   还附加 [托管标识](#managed-identity) 以授予对资源的访问权限     |

#### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> 设置托管标识

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

在群集创建过程中或在编辑计算群集详细信息时，在 " **高级设置**" 中，切换 **分配托管标识** ，并指定系统分配的标识或用户分配的标识。

#### <a name="managed-identity-usage"></a>托管标识用法

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>推理群集

创建或附加 Azure Kubernetes Service (AKS) 群集以进行大规模推断。 使用 [上述步骤](#portal-create) 创建 AKS 群集。  然后填写表单，如下所示：


|字段  |说明  |
|---------|---------|
|计算名称     |  <li>名称是必需的。 名称必须介于2到16个字符之间。 </li><li>有效字符为大小写字母、数字和 **-** 字符。</li><li>名称必须以字母开头</li><li>名称必须在 Azure 区域内的全部现有计算中都是唯一的。 如果选择的名称不是唯一的，则会显示警报</li><li>如果在名称中使用了 **-** 字符，在此字符之后必须至少跟有一个字母</li>     |
|Kubernetes 服务 | 选择 " **新建** " 并填写表单的其余部分。  或者选择 " **使用现有** "，然后从订阅中选择现有的 AKS 群集。
|区域 |  选择将在其中创建群集的区域 |
|虚拟机大小     |  在你的区域中，支持的虚拟机大小可能会受到限制。 请查看[可用性列表](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|群集目的  | 选择 **生产** 或 **开发-测试** |
|节点数 | 节点数乘以虚拟机的内核数 (个 vcpu) 必须大于或等于12。 |
| 网络配置 | 选择 " **高级** " 以在现有虚拟网络中创建计算。 有关虚拟网络中的 AKS 的详细信息，请参阅 [使用专用终结点和虚拟网络进行定型和推理期间的网络隔离](how-to-enable-virtual-network.md#aksvnet)。 |
| 启用 SSL 配置 | 用于在计算上配置 SSL 证书 |

### <a name="attached-compute"></a>附加计算

若要使用在 Azure 机器学习工作区外部创建的计算目标，必须附加这些计算目标。 附加计算目标会使其可供你的工作区使用。  使用 **附加计算** 附加计算目标以进行 **定型**。  使用 **推理群集** 为 **推断**附加 AKS 群集。

使用 [上述步骤](#portal-create) 连接计算。  然后填写表单，如下所示：

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

创建目标并将其附加到工作区后，可在 [运行配置](how-to-set-up-training-targets.md) 中将其用于 `ComputeTarget` 对象：

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* 使用计算资源 [提交定型运行](how-to-set-up-training-targets.md)。
* [教程：定型模型](tutorial-train-models-with-aml.md) 使用托管计算目标来定型模型。
* 若要构建更好的模型，请了解如何[高效地优化超参数](how-to-tune-hyperparameters.md)。
* 训练模型后，了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。
* [通过 Azure 虚拟网络使用 Azure 机器学习](how-to-enable-virtual-network.md)
