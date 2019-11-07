---
title: 创建事件驱动的机器学习工作流
titleSuffix: Azure Machine Learning
description: 了解如何使用事件网格和 Azure 机器学习来启用事件驱动的解决方案。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 11/04/2019
ms.openlocfilehash: b10b848918fda7a1f271a4e617b1706971f103d7
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622225"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>创建事件驱动的机器学习工作流（预览）

[Azure 事件网格](https://docs.microsoft.com/azure/event-grid/)支持 Azure 机器学习服务事件。 例如，你可以使用范围为工作区的运行完成、模型注册、模型部署和数据偏移检测的事件。

有关详细信息，请参阅[Azure 机器学习与事件网格的集成](concept-event-grid-integration.md)和[Azure 机器学习事件网格架构](/azure/event-grid/event-schema-machine-learning)。

使用事件网格启用常见方案，例如：

* 触发管道以便重新训练
* 将事件从 Azure 机器学习流式传输到各种终结点

## <a name="prerequisites"></a>先决条件

* 参与者或所有者访问 Azure 机器学习服务工作区，你将为其创建事件。
* 选择一个事件处理程序终结点，例如 webhook 或事件中心。 有关详细信息，请参阅[事件处理程序](https://docs.microsoft.com/azure/event-grid/event-handlers)。 

## <a name="register-resource-providers"></a>注册资源提供程序

如果在 1 2019 年11月之前使用过 Azure 事件网格或机器学习，则可能需要重新注册资源提供程序，然后才能按照本文档中的步骤进行操作。 若要重新注册提供程序，请使用以下步骤：

1. 中转到 Azure 门户并选择 "__订阅__"。 选择要使用的订阅。
1. 选择 "__资源提供程序__"，然后搜索 " __EventGrid__"。
1. 选择__EventGrid__条目，然后选择 "__重新注册__"。

    ![重新注册-资源提供程序](media/how-to-use-event-grid/re-register-resource-provider.png)

1. 搜索 " __MachineLearningServices__"，选择该条目，然后选择 "__重新注册__"。

> [!TIP]
> 如果没有完成这些步骤的权限，请要求订阅管理员执行这些步骤。

## <a name="configure-machine-learning-events-using-the-azure-portal"></a>使用 Azure 门户配置机器学习事件

1. 打开[Azure 门户](https://portal.azure.com)并中转到 Azure 机器学习工作区。

1. 在左侧栏中，选择 "__事件__"，然后选择 "**事件订阅**"。 

    ![select-events-in-workspace .png](media/how-to-use-event-grid/select-event.png)

1. 选择要使用的事件类型。 例如，以下屏幕截图已注册所选__模型__，已__部署模型__，__已完成运行__，并__检测到数据集偏移__：

    ![添加事件类型](media/how-to-use-event-grid/add-event-type.png)

1. 选择要将事件发布到的终结点。 在以下屏幕截图中，__事件中心__是选定的终结点：

    ![选择-事件处理程序](media/how-to-use-event-grid/select-event-handler.png)

确认选择后，单击 "__创建__"。 配置后，这些事件将被推送到你的终结点。

## <a name="set-up-azure-event-grid-using-cli"></a>使用 CLI 设置 Azure 事件网格

你可以安装最新的[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)，或使用作为你的 Azure 订阅的一部分提供的 Azure Cloud Shell。

若要安装事件网格扩展，请在 CLI 中使用以下命令：

```azurecli-interactive
az add extension --name eventgrid
```

下面的示例演示如何选择 Azure 订阅，然后为 Azure 机器学习创建新的事件订阅：

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

## <a name="sample-scenarios"></a>示例方案

### <a name="use-a-logic-app-to-send-email-alerts"></a>使用逻辑应用发送电子邮件警报

利用[Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/)为所有事件配置电子邮件。 使用条件进行自定义，并指定接收方，使团队能够在协同工作的团队间实现协作和认知。

1. 在 Azure 门户中，请切换到 Azure 机器学习工作区，并从左侧栏中选择 "事件" 选项卡。 在此处选择 "__逻辑应用__"。 

    ![选择-逻辑-ap](media/how-to-use-event-grid/select-logic-ap.png)

1. 登录到逻辑应用 UI，并选择机器学习服务作为主题类型。 

    ![选择-主题-类型](media/how-to-use-event-grid/select-topic-type.png)

1. 选择要通知的事件。 例如，以下屏幕截图__RunCompleted__。

    ![runcomplete](media/how-to-use-event-grid/select-event-runcomplete.png)

1. 你还可以添加筛选器，以仅对事件类型的子集触发逻辑应用。 在下面的屏幕截图中，将使用 __/datadriftID/runs/__ 的__前缀筛选器__。

    ![筛选器-事件](media/how-to-use-event-grid/filtering-events.png)

1. 接下来，添加一个使用此事件并搜索电子邮件的步骤。 可以使用多个不同的邮件帐户来接收事件。 你还可以配置发送电子邮件警报的时间。

    ![选择-电子邮件-操作](media/how-to-use-event-grid/select-email-action.png)

1. 选择 "__发送电子邮件__" 并填写参数。 在主题中，您可以包含__事件类型__和__主题__来帮助筛选事件。 你还可以在消息正文中包含运行的工作区页面的链接。 

    ![配置-电子邮件正文](media/how-to-use-event-grid/configure-email-body.png)

1. 若要保存此操作，请选择页面左侧的 "**另存为**"。 在出现的右侧栏中，确认已创建此操作。

    ![确认-逻辑应用-创建](media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>使用逻辑应用可在发生数据偏移时触发重新训练工作流

随着时间的推移，模型会过时，并且在运行它的上下文中也不会有用处。 判断是否可以重新训练模型的一种方法是检测数据偏移。 

此示例演示如何在 Azure 逻辑应用中使用事件网格来触发重新训练。 当模型的定型和服务数据集之间发生数据偏移时，此示例将触发 Azure 数据工厂管道。

在开始之前，请执行以下操作：

* 设置数据集监视器以检测工作区中的[数据偏差]( https://aka.ms/datadrift)
* 创建已发布的[Azure 数据工厂管道](https://docs.microsoft.com/azure/data-factory/)。

在此示例中，简单的数据工厂管道用于将文件复制到 blob 存储区，并运行已发布的机器学习管道。 有关此方案的详细信息，请参阅如何[在 Azure 数据工厂中设置机器学习步骤](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline](media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. 首先创建逻辑应用。 请参阅[Azure 门户](https://portal.azure.com)，搜索 "逻辑应用"，然后选择 "创建"。

    ![搜索-逻辑应用](media/how-to-use-event-grid/search-for-logic-app.png)

1. 填写所需的信息。 若要简化体验，请使用与 Azure 数据工厂管道相同的订阅和资源组，并 Azure 机器学习工作区。

    ![设置逻辑应用-adf 应用](media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. 创建逻辑应用后，选择__事件网格资源事件发生的时间__。 

    ![选择-事件-网格-触发器](media/how-to-use-event-grid/select-event-grid-trigger.png)

1. 登录并填写事件的详细信息。 将__资源名称__设置为工作区名称。 将__事件类型__设置为 " __DatasetDriftDetected__"。

    ![登录和添加事件](media/how-to-use-event-grid/login-and-add-event.png)

1. 添加一个新步骤，然后搜索 " __Azure 数据工厂__"。 选择 "__创建管道运行__"。 

    ![adfpipeline-运行](media/how-to-use-event-grid/create-adfpipeline-run.png)

1. 登录并指定要运行的已发布 Azure 数据工厂管道。

    ![指定-adf-管道](media/how-to-use-event-grid/specify-adf-pipeline.png)

1. 使用页面左上角的 "**保存**" 按钮保存并创建逻辑应用。 若要查看应用，请在[Azure 门户](https://portal.azure.com)中找到工作区，并单击 "**事件**"。

    ![显示逻辑应用-webhook](media/how-to-use-event-grid/show-logic-app-webhook.png)

现在发生偏移时，会触发数据工厂管道。 在[新的工作区门户](https://ml.azure.com)上查看有关数据偏移运行和机器学习管道的详细信息。 

![查看工作区](media/how-to-use-event-grid/view-in-workspace.png)


### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>使用 Azure Functions 基于标记部署模型

Azure 机器学习模型对象包含可对部署进行透视的参数，如模型名称、版本、标记和属性。 模型注册事件可触发一个终结点，你可以使用 Azure 函数基于这些参数的值部署一个模型。

有关示例，请参阅[https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid)存储库并按照**自述**文件中的步骤操作。

## <a name="next-steps"></a>后续步骤

* 若要了解有关可用事件的详细信息，请参阅[Azure 机器学习事件架构](/azure/event-grid/event-schema-machine-learning)