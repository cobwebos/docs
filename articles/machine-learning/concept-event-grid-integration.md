---
title: 使用 Azure 机器学习事件
titleSuffix: Azure Machine Learning
description: 本文介绍如何使用 Azure 事件网格订阅、响应和取消订阅 Azure 机器学习生成的事件。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139039"
---
# <a name="consume-azure-machine-learning-events-preview"></a>使用 Azure 机器学习事件（预览）

Azure 机器学习管理机器学习过程的整个生命周期，包括模型定型、模型部署和监视。 Azure 机器学习事件允许应用程序在机器学习生命周期内对事件做出反应，例如完成培训、注册和部署模型，以及使用新式无服务器检测数据偏差架构. 

这些事件通过[Azure 事件网格](https://azure.microsoft.com/services/event-grid/)发布。 使用 Azure 门户、Powershell 或 Azure CLI，客户可以通过[指定一个或多个事件类型和筛选条件](/azure/event-grid/event-filtering)来轻松地订阅事件。 客户还可以选择构建各种事件处理程序，如 Azure Functions、Azure 逻辑应用程序或通用 webhook。 Azure 机器学习与 Azure 事件网格一起，为你提供了高可用性、可靠且容错的事件交付平台，用于生成事件驱动的应用程序。

有关将 Azure 机器学习与事件网格结合使用的信息，请参阅[创建事件驱动机器学习工作流（预览版）](how-to-use-event-grid.md)。

## <a name="the-event-model"></a>事件模型 

Azure 事件网格从源中读取事件，例如 Azure 机器学习和其他 Azure 服务。 然后，将这些事件发送到事件处理程序，如 Azure 事件中心、Azure Functions、逻辑应用等。 下图显示了事件网格如何连接源和处理程序，但并不是受支持集成的完整列表。

![Azure 事件网格功能模型](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

有关事件源和事件处理程序的详细信息，请参阅[什么是事件网格？](/azure/event-grid/overview)。

## <a name="azure-machine-learning-event-types"></a>Azure 机器学习事件类型

Azure 机器学习在机器学习生命周期的各个点提供事件： 

| 事件类型 | 说明 |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | 在机器学习试验运行完成时引发 |
| `Microsoft.MachineLearningServices.ModelRegistered` | 在工作区中注册机器学习模型时引发 |
| `Microsoft.MachineLearningServices.ModelDeployed` | 当完成使用一个或多个模型的推理服务部署时引发 |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | 当完成两个数据集的数据偏移检测作业时引发 |
| `Microsoft.MachineLearningServices.RunStatusChanged` | 当运行状态更改时引发，当前仅在运行状态为 "失败" 时引发 |

## <a name="subscribe-to-machine-learning-events"></a>订阅机器学习事件

基于角色的访问控制（RBAC）保护 Azure 机器学习事件的订阅。 只有工作区的[参与者或所有者](how-to-assign-roles.md#default-roles)才能创建、更新和删除事件订阅。

可以根据各种条件筛选事件订阅。 可在[创建](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)事件订阅期间或[以后](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)将筛选器应用于事件订阅。 

### <a name="filter-by-event-type"></a>按事件类型筛选
事件订阅可以指定一个或多个 Azure 机器学习事件类型。

### <a name="filter-by-event-subject"></a>按事件使用者筛选
Azure 事件网格支持基于的__开头为__的主题筛选器，以匹配项__结束__，以便将具有匹配主题的事件传递给订阅服务器。 不同的机器学习事件具有不同的使用者格式。

| 事件类型 | 使用者格式 | 示例主题 |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>高级筛选

Azure 事件网格还支持基于已发布事件架构的高级筛选。 Azure 机器学习事件架构详细信息，请参阅[Azure 事件网格事件架构中的 Azure 机器学习](../event-grid/event-schema-machine-learning.md)。

可以执行的一些高级 filterings 示例包括：

* 对于 `Microsoft.MachineLearningServices.ModelRegistered` 事件，要筛选模型的标记值：

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

若要了解有关如何应用筛选器的详细信息，请参阅 "[事件网格的筛选事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)"。

## <a name="consume-machine-learning-events"></a>使用机器学习事件

处理机器学习事件的应用程序应遵循以下建议做法：

> [!div class="checklist"]
> * 由于可以将多个订阅配置为将事件路由到同一个事件处理程序，因此不要假设事件来自特定的源，而是检查消息的主题，以确保它来自您所需的机器学习工作区。
> * 同样，检查 eventType 是否为准备处理的项，并且不假定所接收的全部事件都是期望的类型。
> * 消息在一段延迟时间后会无序到达，请使用 etag 字段来了解对象的相关信息是否是最新的。  此外，还可使用 sequencer 字段来了解任何特定对象的事件顺序。
> * 忽略不了解的字段。 此做法有助于适应将来可能添加的新功能。
> * 失败或取消 Azure 机器学习操作不会触发事件。 例如，如果模型部署失败，则不会触发 MachineLearningServices。 ModelDeployed。 设计应用程序时，请考虑此类失败模式。 始终可以使用 Azure 机器学习 SDK、CLI 或门户来检查操作状态并了解详细的失败原因。

Azure 事件网格允许客户生成可由 Azure 机器学习事件触发的反耦合消息处理程序。 消息处理程序的一些值得注意的示例如下：
* Azure Functions
* Azure 逻辑应用
* Azure 事件中心
* Azure 数据工厂管道
* 通用 webhook，可以托管在 Azure 平台上或其他位置

## <a name="next-steps"></a>后续步骤

了解有关事件网格的详细信息并为 Azure 机器学习事件进行尝试：

- [关于事件网格](../event-grid/overview.md)
- [Azure 机器学习的 Azure 事件网格事件架构](../event-grid/event-schema-machine-learning.md)
- [创建具有 Azure 机器学习的事件驱动的工作流](how-to-use-event-grid.md)
