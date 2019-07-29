---
title: 快速入门：在云中运行笔记本
titleSuffix: Azure Machine Learning service
description: 本教程介绍如何开始使用 Azure 机器学习服务并在云中使用托管笔记本服务器。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 1124bb17abb9340b442d8d6075551ffe0dc681f7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68371054"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>快速入门：通过基于云的 Notebook 服务器开始使用 Azure 机器学习

本快速入门讲授如何通过在云中使用托管笔记本服务器开始使用 Azure 机器学习服务。 不需进行安装。 如果希望改为将 SDK 安装到你自己的 Python 环境中，请参阅[快速入门：通过自己的 Notebook 服务器开始使用 Azure 机器学习](quickstart-run-local-notebook.md)。

本快速入门介绍如何使用 [Azure 机器学习服务工作区](concept-azure-machine-learning-architecture.md)来跟踪机器学习 (ML) 试验。 为此，我们将创建[笔记本虚拟机（预览版）](how-to-configure-environment.md#notebookvm)：一个安全的基于云的 Azure 工作站，提供 Jupyter 笔记本服务器、JupyterLab 和一个完全准备好的 ML 环境。 然后，在此虚拟机 (VM) 上运行一个 Python 笔记本，用于将值记录到工作区中。

为此，请执行以下操作：

* 创建工作区。
* 在你的工作区中创建一个笔记本 VM。
* 打开 Jupyter Web 界面。
* 打开一个笔记本，其中包含的代码可以在每次迭代时消除 pi 和日志错误。
* 运行笔记本。
* 在工作区中查看所记录的错误值。 以下示例介绍如何通过工作区跟踪脚本中生成的信息。

如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

## <a name="create-a-workspace"></a>创建工作区

如果你有一个 Azure 机器学习服务工作区，请跳至[下一部分](#create-notebook)。 否则，请立即创建一个。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-notebook"></a>创建笔记本 VM

 在工作区中创建云资源，开始使用 Jupyter 笔记本。 此资源是一个基于云的平台，该平台已预先配置了运行 Azure 机器学习服务所需的一切。

1. 在 [Azure 门户](https://portal.azure.com/)中打开你的工作区。 如果不确定如何在门户中查找工作区，请参阅如何[查看工作区](how-to-manage-workspace.md#view)。

1. 在工作区页上，选择左侧的“笔记本 VM”。 

1. 选择“+新建”，创建一个笔记本 VM。   

     ![选择“新建 VM”](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. 为 VM 提供一个名称。 然后选择“创建”  。

    > [!NOTE]
    > 笔记本 VM 的名称必须为 2 到 16 个字符。 字母、数字和连字符为有效字符。 名称必须在 Azure 订阅中独一无二。

    ![创建新 VM](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. 等待约 4 到 5 分钟，直至状态更改为“正在运行”。 


## <a name="open-the-jupyter-web-interface"></a>打开 Jupyter Web 界面

在 VM 运行以后，使用“笔记本 VM”部分打开 Jupyter Web 界面。 

1. 在 VM 的“URI”列中选择“Jupyter”。    

    ![启动 Jupyter 笔记本服务器](./media/quickstart-run-cloud-notebook/start-server.png)

    此链接启动笔记本服务器并在新的浏览器标签页中打开 Jupyter 笔记本网页。此链接将仅适用于创建 VM 的人。  工作区的每个用户必须创建自己的 VM。

1. 在 Jupyter 笔记本网页上，顶部文件夹的名称为用户名。 选择该文件夹。

    > [!TIP]
    > 此文件夹位于工作区中的[存储容器](concept-workspace.md#resources)上，而不是位于笔记本 VM 本身上。  可以删除笔记本 VM，但仍保留所有工作。  稍后创建新的笔记本 VM 时，它将加载此同一文件夹。  如果将工作区与他人共享，则他人会看到你的文件夹，你会看到他人的文件夹。 

1. samples 文件夹名称包含版本号（例如 **samples-1.0.33.1**）。 选择 samples 文件夹。

1. 选择 **Quickstart** 文件夹。

## <a name="run-the-notebook"></a>运行笔记本

运行一个笔记本，估算 pi 并将错误记录到工作区。

1. 选择 **01.run-experiment.ipynb** 以打开笔记本。

1. 如果看到“未找到内核”警报，请选择内核“Python 3.6 - AzureML”  （大约位于列表的中间位置）并设置内核。

1. 选择第一个代码单元，然后选择“运行”。 

    > [!NOTE]
    > 代码单元之前有括号。 如果括号是空的 ( __[  ]__ )，则表明代码尚未运行。 运行代码时，会出现一个星号 ( __[*]__ )。 代码完成后，会显示数字 **[1]** 。  该数字表明单元的运行顺序。
    >
    > 使用 **Shift + Enter** 作为运行某个单元的快捷方式。

    ![运行第一个代码单元](media/quickstart-run-cloud-notebook/cell1.png)

1. 运行第二个代码单元。 如果看到身份验证说明，请复制代码，然后单击登录链接。 登录后，浏览器会记住该设置。  

    ![身份验证](media/quickstart-run-cloud-notebook/authenticate.png)

1. 代码单元运行成功后，会显示单元编号 __[2]__ 。 如果已登录过，则会看到一条表明身份验证成功的状态消息。   如果未登录过，则不会看到此单元的任何输出， 只会看到编号，表明该单元已成功运行。

    ![成功消息](media/quickstart-run-cloud-notebook/success.png)

1. 运行余下的代码单元。 当每个单元完成运行时，其单元编号会显示。 只有最后一个单元显示任意其他输出。  

    在最大的代码单元中，`run.log` 出现在多个位置。 每个 `run.log` 都会将其值添加到工作区。

## <a name="view-logged-values"></a>查看所记录的值

1. `run` 单元的输出包含一个回到 Azure 门户的链接，可用于在工作区中查看试验结果。

    ![查看试验](./media/quickstart-run-cloud-notebook/view-exp.png)

1. 选择“链接到 Azure 门户”，查看工作区中的运行的相关信息。  此链接打开 Azure 门户中的工作区。

1. 你看到的已记录值的绘图已自动在工作区中创建。 只要使用相同的名称参数记录了多个值，系统就会自动为你生成绘图。 下面是一个示例：

   ![查看历史记录](./media/quickstart-run-cloud-notebook/web-results.png)

由于估算 pi 的代码使用随机值，因此你的绘图可能看起来不同。  

## <a name="clean-up-resources"></a>清理资源

### <a name="stop-the-notebook-vm"></a>停止笔记本 VM

在不使用笔记本 VM 时，请将其停止，以便降低成本。  

1. 在工作区中，选择“笔记本 VM”。 

   ![停止 VM 服务器](./media/quickstart-run-cloud-notebook/stop-server.png)

1. 从列表中选择 VM。

1. 选择“停止”  。

1. 准备好再次使用服务器时，选择“启动”  。

### <a name="delete-everything"></a>删除所有内容

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

还可保留资源组，但请删除单个工作区。 显示工作区属性，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

完成这些任务后，请转到 Jupyter 笔记本网页。 在 **Quickstart** 文件夹中打开并运行 **02.deploy-web-service.ipynb** 笔记本，了解如何部署 Web 服务。

若要将其他 Python 包安装到 Jupyter 环境中，请在笔记本中使用以下代码：

```
!source activate py36 && pip install <packagename>
```

同样在 Jupyter Notebook 网页上浏览 samples 文件夹中的其他笔记本，详细了解 Azure 机器学习服务。

若要深入体验工作流，请按照机器学习教程来训练和部署模型：  

> [!div class="nextstepaction"]
> [教程：训练图像分类模型](tutorial-train-models-with-aml.md)
