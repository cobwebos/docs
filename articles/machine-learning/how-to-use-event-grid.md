---
title: 创建事件驱动的机器学习工作流
titleSuffix: Azure Machine Learning
description: 了解如何使用事件网格和 Azure 机器学习来实现事件驱动的解决方案。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: 2a1440dcda27a487c89be4ac63e624a2bb6b393a
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111872"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>创建事件驱动的机器学习工作流（预览）

[Azure 事件网格](https://docs.microsoft.com/azure/event-grid/)支持 Azure 机器学习事件。 你可以在工作区中订阅和使用事件，例如运行状态更改、运行完成、模型注册、模型部署和数据偏移检测。

有关事件类型的详细信息，请参阅[Azure 机器学习与事件网格的集成](concept-event-grid-integration.md)和[Azure 机器学习事件网格架构](/azure/event-grid/event-schema-machine-learning)。

使用事件网格实现常见方案，例如：

* 在运行失败和运行完成后发送电子邮件
* 在注册模型后使用 Azure 函数
* 将 Azure 机器学习中的事件流式传输到各种终结点
* 检测到偏移时触发 ML 管道

> [!NOTE] 
> 目前，只有当运行状态为 **failed** 时才会触发 runStatusChanged 事件
>

## <a name="prerequisites"></a>必备条件
* 对要为其创建事件的 Azure 机器学习工作区具备参与者或所有者访问权限。

### <a name="configure-eventgrid-using-the-azure-portal"></a>使用 Azure 门户配置 EventGrid

1. 打开 [Azure 门户](https://portal.azure.com)并转到 Azure 机器学习工作区。

1. 在左侧栏中选择“事件”，然后选择“事件订阅”   。 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. 选择要使用的事件类型。 例如，以下屏幕截图中已选择“模型注册”、“模型部署”、“运行完成”以及“检测到数据集偏移”     ：

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. 选择要将事件发布到的终结点。 以下屏幕截图中选择的终结点是“事件中心”  ：

    ![select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

确认选择后，请单击“创建”  。 配置完成后，这些事件将被推送到终结点。


### <a name="configure-eventgrid-using-the-cli"></a>使用 CLI 配置 EventGrid

你可以安装最新的[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，或使用作为你的 Azure 订阅的一部分提供的 Azure Cloud Shell。

若要安装事件网格扩展，请在 CLI 中使用以下命令：

```azurecli-interactive
az add extension --name eventgrid
```

下面的示例演示如何选择 Azure 订阅，并为 Azure 机器学习创建新的事件订阅：

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

## <a name="filter-events"></a>筛选事件

设置事件时，你可以应用筛选器来仅针对特定的事件数据触发。 在下面的示例中，对于运行状态更改事件，你可以按运行类型进行筛选。 仅当满足条件时才会触发该事件。 请参阅 [Azure 机器学习事件网格架构](/azure/event-grid/event-schema-machine-learning)，了解可用作筛选依据的事件数据。 

1. 转到 Azure 门户，选择一个新订阅或现有订阅。 

1. 选择“筛选器”选项卡，向下滚动到“高级筛选器”。 对于 "**键**" 和 "**值**"，提供要按其进行筛选的属性类型。 在这里，你可以看到，只有当运行类型为管道运行或管道步骤运行时，事件才会触发。  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="筛选事件":::

## <a name="sample-send-email-alerts"></a>示例：发送电子邮件警报

使用[Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/)为所有事件配置电子邮件。 使用条件进行自定义并指定接收方，让合作团队能互相协作并收到通知。

1. 在 Azure 门户中转到 Azure 机器学习工作区，并从左侧栏中选择“事件”选项卡。 在此处选择“逻辑应用”____。 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. 登录到逻辑应用 UI，并选择机器学习服务作为主题类型。 

    ![select-topic-type](./media/how-to-use-event-grid/select-topic-type.png)

1. 选择要进行通知的事件。 例如，以下屏幕截图中选择的是“RunCompleted”____。

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. 你可以使用上面部分中的筛选方法，或者添加筛选器，以仅对事件类型的子集触发逻辑应用。 以下屏幕截图中采用的前缀筛选条件是 /datadriftID/runs/________。

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. 接下来添加一个使用此事件的步骤并搜索电子邮件。 可以使用多个不同的电子邮件帐户来接收事件。 还可以配置发送电子邮件警报的时间。

    ![select-email-action](./media/how-to-use-event-grid/select-email-action.png)

1. 选择“发送电子邮件”并填写参数____。 可以在主题中包含“事件类型”和“主题”来帮助筛选事件________。 还可以在邮件正文中包含指向工作区页面的链接。 

    ![configure-email-body](./media/how-to-use-event-grid/configure-email-body.png)

1. 若要保存此操作，请选择页面左上角的“另存为”****。 在出现的右侧栏中，确认已创建此操作。

    ![confirm-logic-app-create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


## <a name="sample-trigger-retraining-when-data-drift-occurs"></a>示例：在发生数据偏移时触发重新训练

模型会在一定时间后过时，并在运行该模型的上下文中失去效用。 要判断是否应重新训练某个模型，一种方法是检测数据偏移。 

此示例演示如何使用 Azure 逻辑应用和事件网格来触发重新训练。 当模型的训练和服务数据集之间发生数据偏移时，此示例将触发 Azure 数据工厂管道。

在开始之前，请执行以下操作：

* 设置数据集监视器以[检测工作区中的数据偏移]( https://aka.ms/datadrift)
* 创建已发布的 [Azure 数据工厂管道](https://docs.microsoft.com/azure/data-factory/)。

此示例中使用的是简单的数据工厂管道，用于将文件复制到 blob 存储区，并运行已发布的机器学习管道。 有关此方案的详细信息，请参阅如何设置 [Azure 数据工厂中的机器学习步骤](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline-stage](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. 首先，创建逻辑应用。 转到 [Azure 门户](https://portal.azure.com)，搜索逻辑应用并选择“创建”。

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. 填写必需的信息。 若要简化此过程，请使用与 Azure 数据工厂管道和 Azure 机器学习工作区相同的订阅和资源组。

    ![set-up-logic-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. 创建逻辑应用后，选择“当事件网格资源事件发生时”____。 

    ![select-event-grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. 登录并填写事件的详细信息。 将“资源名称”设为工作区名称____。 将“事件类型”设为 DatasetDriftDetected________。

    ![login-and-add-event](./media/how-to-use-event-grid/login-and-add-event.png)

1. 添加新步骤并搜索“Azure 数据工厂”____。 选择“创建管道运行”____。 

    ![create-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. 登录并指定要运行的已发布的 Azure 数据工厂管道。

    ![specify-adf-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. 使用页面左上角的“保存”按钮保存并创建逻辑应用****。 要查看创建的应用，请在 [Azure 门户](https://portal.azure.com)中转到自己的工作区并单击“事件”****。

    ![show-logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

现在，只要发生偏移，就会触发数据工厂管道。 在[新的工作区门户](https://ml.azure.com)上查看数据偏移运行和机器学习管道的详细信息。 

![view-in-workspace](./media/how-to-use-event-grid/view-in-workspace.png)

## <a name="sample-deploy-a-model-based-on-tags"></a>示例：基于标记部署模型

Azure 机器学习模型对象包含一些参数，可以基于这些参数进行部署，例如模型名称、版本、标记和属性。 模型注册事件可触发终结点，你可使用 Azure 函数基于这些参数的值部署模型。

有关示例，请参阅 [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) 存储库，并执行自述文件中的步骤  。

## <a name="next-steps"></a>后续步骤

* 若要详细了解可用事件，请参阅 [Azue 机器学习事件架构](/azure/event-grid/event-schema-machine-learning)
