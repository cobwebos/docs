---
title: 在 ML 工作流中触发事件
titleSuffix: Azure Machine Learning
description: 在 Azure 机器学习中设置事件驱动的应用程序、进程或 CI/CD 机器学习工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 4a8c9c8088126a33b28bd36382536a8fe25ff0a7
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800479"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>基于 Azure 机器学习事件触发应用程序、进程或 CI/CD 工作流（预览）

本文介绍如何在 [Azure 事件网格](https://docs.microsoft.com/azure/event-grid/)检测到某些条件时，基于 Azure 机器学习事件（例如，失败通知电子邮件或 ML 管道运行）设置事件驱动的应用程序、进程或 CI/CD 工作流。 

Azure 机器学习管理机器学习进程的整个生命周期，包括模型训练、模型部署和监视。 可以借助现代无服务器体系结构，使用事件网格对 Azure 机器学习事件做出反应，例如完成训练运行、注册和部署模型以及检测数据偏移。 然后，可以订阅和使用事件，例如运行状态更改、运行完成、模型注册、模型部署以及工作区中的数据偏移检测。

何时将事件网格用于事件驱动的操作：
* 在运行失败和运行完成时发送电子邮件
* 注册模型后使用 Azure 函数
* 将事件从 Azure 机器学习流式传输到各种终结点
* 检测到偏移时触发 ML 管道

> [!NOTE] 
> 当前，runStatusChanged 事件仅在运行状态为“失败”时触发

## <a name="prerequisites"></a>先决条件
要使用事件网格，需要以参与者或所有者身份访问将为其创建事件的 Azure 机器学习工作区。

## <a name="the-event-model--types"></a>事件模型与类型

Azure 事件网格从 Azure 机器学习和其他 Azure 服务等源中读取事件。 然后，将这些事件发送到事件处理程序，例如 Azure 事件中心、Azure Functions、逻辑应用等。 下图显示了事件网格如何连接源和处理程序，但未完整列出受支持的集成。

![Azure 事件网格功能模型](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

有关事件源和事件处理程序的详细信息，请参阅[什么是事件网格？](/azure/event-grid/overview)。

### <a name="event-types-for-azure-machine-learning"></a>Azure 机器学习的事件类型

Azure 机器学习在机器学习生命周期的各个点提供事件： 

| 事件类型 | 说明 |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | 在机器学习试验运行完成时引发 |
| `Microsoft.MachineLearningServices.ModelRegistered` | 在工作区中注册机器学习模型时引发 |
| `Microsoft.MachineLearningServices.ModelDeployed` | 在具有一个或多个模型的推理服务部署完成时引发 |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | 在两个数据集的数据偏移检测作业完成时引发 |
| `Microsoft.MachineLearningServices.RunStatusChanged` | 运行状态更改时引发，当前仅在运行状态为“失败”时引发 |

### <a name="filter--subscribe-to-events"></a>筛选并订阅事件

这些事件通过 Azure 事件网格发布。 使用 Azure门户、PowerShell 或 Azure CLI，客户可以通过[指定一种或多种事件类型并筛选条件](/azure/event-grid/event-filtering)来轻松地订阅事件。 

设置事件时，可以应用筛选器，以仅在特定事件数据上触发。 在下面的示例中，对于运行状态更改的事件，可以按运行类型进行筛选。 仅在满足条件时触发事件。 要了解可以作为筛选依据的事件数据，请参阅 [Azure 机器学习事件网格架构](/azure/event-grid/event-schema-machine-learning)。 

Azure 机器学习事件的订阅受基于角色的访问控制 (RBAC) 保护。 只有工作区[参与者或所有者](how-to-assign-roles.md#default-roles)可以创建、更新和删除事件订阅。  可在[创建](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)事件订阅期间或以后将筛选器应用于事件订阅。 


1. 转到 Azure 门户，选择新订阅或现有订阅。 

1. 选择“筛选器”选项卡，然后向下滚动到“高级筛选器”。 对于“键”和“值”，提供要作为筛选依据的属性类型 。 可以在下面看到事件仅在运行类型为管道运行或管道步骤运行时触发。  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="筛选事件":::


+ **按事件类型筛选：** 事件订阅可以指定一个或多个 Azure 机器学习事件类型。

+ **按事件主题筛选：** Azure 事件网格支持根据“开头为”和“结尾为”的匹配筛选主题，以便将具有匹配主题的事件传递给订阅者 。 不同的机器学习事件具有不同的主题格式。

  | 事件类型 | 主题格式 | 示例主题 |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **高级筛选**：Azure 事件网格还支持基于已发布事件架构的高级筛选。 有关 Azure 机器学习事件架构的详细信息，请参阅 [Azure 机器学习的 Azure 事件网格事件架构](../event-grid/event-schema-machine-learning.md)。  可以执行的一些示例高级筛选包括：

  对于 `Microsoft.MachineLearningServices.ModelRegistered` 事件，筛选模型的标记值：

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  要了解有关如何应用筛选器的详细信息，请参阅[事件网格的筛选事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。

## <a name="consume-machine-learning-events"></a>使用机器学习事件

处理机器学习事件的应用程序应遵循一些建议的做法：

> [!div class="checklist"]
> * 由于可将多个订阅配置为将事件路由至相同的事件处理程序，因此请勿假定事件来自特定的源，而是应检查消息的主题，确保它来自所期望的机器学习工作区。
> * 同样，检查 eventType 是否为准备处理的项，并且不假定所接收的全部事件都是期望的类型。
> * 消息在一段延迟时间后会无序到达，请使用 etag 字段来了解对象的相关信息是否是最新的。  此外，还可使用 sequencer 字段来了解任何特定对象的事件顺序。
> * 忽略不了解的字段。 此做法有助于适应将来可能添加的新功能。
> * 失败或取消的 Azure 机器学习操作将不会触发事件。 例如，如果模型部署失败，则不会触发 Microsoft.MachineLearningServices.ModelDeployed。 设计应用程序时，请考虑此类失败模式。 始终可以使用 Azure 机器学习 SDK、CLI 或门户来检查操作的状态并详细了解失败原因。

使用 Azure 事件网格，客户可以构建解耦的消息处理程序，该消息处理程序可以由 Azure 机器学习事件触发。 一些需要注意的消息处理程序的示例是：
* Azure Functions
* Azure 逻辑应用
* Azure 事件中心
* Azure 数据工厂管道
* 通用 Webhook，可在 Azure 平台或其他平台上托管

## <a name="set-up-in-azure-portal"></a>在 Azure 门户中设置

1. 打开 [Azure 门户](https://portal.azure.com)，然后转到 Azure 机器学习工作区。

1. 在左侧边栏中，选择“事件”，然后选择“事件订阅”。 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. 选择要使用的事件类型。 例如，以下屏幕截图选中了“模型已注册”、“模型已部署”、“运行已完成”和“检测到数据集偏移”   ：

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. 选择要将事件发布到的终结点。 在以下屏幕截图中，“事件中心”是选定的终结点：

    ![select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

确认选择后，单击“创建”。 配置后，这些事件将被推送到终结点。


### <a name="set-up-with-the-cli"></a>使用 CLI 进行设置

可以安装最新的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，也可以使用作为 Azure 订阅的一部分提供的 Azure Cloud Shell。

要安装事件网格扩展，请在 CLI 中使用以下命令：

```azurecli-interactive
az add extension --name eventgrid
```

下面的示例演示如何选择 Azure 订阅以及为 Azure 机器学习创建新的事件订阅：

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>示例

### <a name="example-send-email-alerts"></a>示例：发送电子邮件警报

使用 [Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/)为所有事件配置电子邮件。 根据条件进行自定义，并指定收件人，以实现跨协作团队的协作和意识。

1. 在 Azure 门户中，转到 Azure 机器学习工作区，然后从左侧边栏中选择“事件”选项卡。 在此处选择“逻辑应用”。 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. 登录逻辑应用 UI，然后选择机器学习服务作为主题类型。 

    ![select-topic-type](./media/how-to-use-event-grid/select-topic-type.png)

1. 选择要通知的事件。 例如，以下屏幕截图 RunCompleted。

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. 可以使用上一部分中的筛选方法，也可以添加筛选器以仅针对部分事件类型触发逻辑应用。 在以下屏幕截图中，使用了前缀筛选器 /datadriftID/runs/ 。

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. 接下来，添加一个步骤，以使用此事件并搜索电子邮件。 可以使用几个不同的邮件帐户来接收事件。 还可以配置何时发送电子邮件警报的条件。

    ![select-email-action](./media/how-to-use-event-grid/select-email-action.png)

1. 选择“发送电子邮件”并填写参数。 在主题中，可以添加“事件类型”和“主题”以帮助筛选事件 。 还可以在消息正文中包含指向运行的工作区页面的链接。 

    ![configure-email-body](./media/how-to-use-event-grid/configure-email-body.png)

1. 要保存此操作，请选择页面左上角的“另存为”。 在出现的右侧边栏中，确认创建此操作。

    ![confirm-logic-app-create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>示例：重新训练数据偏移触发器

随着时间的推移，模型会过时，并且在其运行上下文中也不再有用。 判断是否需要重新训练模型的一种方法是检测数据偏移。 

本示例说明如何结合使用事件网格和 Azure 逻辑应用来触发重新训练。 当模型的训练数据集和服务数据集之间发生数据偏移时，该示例将触发 Azure 数据工厂管道。

在开始之前，请执行以下操作：

* 设置数据集监视器以[检测工作区中的数据偏移]( https://aka.ms/datadrift)
* 创建已发布的 [Azure 数据工厂管道](https://docs.microsoft.com/azure/data-factory/)。

在此示例中，简单的数据工厂管道用于将文件复制到 blob 存储中，并运行已发布的机器学习管道。 有关此方案的详细信息，请参阅如何[在 Azure 数据工厂中设置机器学习步骤](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline-stage](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. 首先创建逻辑应用。 转到 [Azure 门户](https://portal.azure.com)，搜索逻辑应用，然后选择创建。

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. 填写必需的信息。 为了简化体验，请使用与 Azure 数据工厂管道和 Azure 机器学习工作区相同的订阅和资源组。

    ![set-up-logic-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. 创建逻辑应用后，选择“事件网格资源事件发生时”。 

    ![select-event-grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. 登录并填写事件的详细信息。 将“资源名称”设置为工作区名称。 将“事件类型”设置为“DatasetDriftDetected” 。

    ![login-and-add-event](./media/how-to-use-event-grid/login-and-add-event.png)

1. 添加一个新步骤，并搜索 Azure 数据工厂。 选择“创建管道运行”。 

    ![create-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. 登录并指定要运行的已发布的 Azure 数据工厂管道。

    ![specify-adf-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. 使用页面左上方的“保存”按钮进行保存，并创建逻辑应用。 要查看应用，请转到 [Azure 门户](https://portal.azure.com)中的工作区，然后单击“事件”。

    ![show-logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

现在，当发生偏移时，将触发数据工厂管道。 在[新的工作区门户](https://ml.azure.com)上查看有关数据偏移运行和机器学习管道的详细信息。 

![view-in-workspace](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>示例：基于标记部署模型

Azure 机器学习模型对象包含可以基于其进行部署的参数，例如模型名称、版本、标记和属性。 模型注册事件可以触发终结点，你可以使用 Azure 函数基于这些参数的值来部署模型。

例如，请参阅 [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) 存储库，并按照自述文件中的步骤进行操作。

## <a name="next-steps"></a>后续步骤

要详细了解事件网格并尝试使用 Azure 机器学习事件，请参阅：

- [关于事件网格](../event-grid/overview.md)

- [Azure 机器学习的事件架构](../event-grid/event-schema-machine-learning.md)

