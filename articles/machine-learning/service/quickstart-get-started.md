---
title: 快速入门：创建机器学习工作区 - Azure
description: 使用 Azure 门户创建 Azure 机器学习工作区。 该工作区是基础的云端块，用于使用 Azure 机器学习服务试验、培训和部署机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: rastala
ms.author: roastala
ms.date: 09/24/2018
ms.openlocfilehash: c1610291e06255e2c724268f63d740f7e4debea4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959982"
---
# <a name="quickstart-get-started-with-azure-machine-learning-service"></a>快速入门：Azure 机器学习服务入门

在本快速入门中，你将使用 Azure 门户创建 Azure 机器学习工作区。 该工作区是基础的云端块，用于使用 Azure 机器学习服务试验、培训和部署机器学习模型。 

在本教程中，你将执行以下操作：

* 在 Azure 订阅中创建工作区
* 在 Azure Notebook 中搭配 Python 使用，并记录多个迭代中的值
* 在工作区中查看所记录的值

为方便起见，如果区域支持，下述资源将自动添加到你的工作区：[容器注册表](https://azure.microsoft.com/services/container-registry/)[存储](https://azure.microsoft.com/services/storage/)、[应用程序见解](https://azure.microsoft.com/services/application-insights/)和[密钥保险库](https://azure.microsoft.com/services/key-vault/)。

所创建的资源可用作其他 Azure 机器学习教程和操作方法文章的先决条件。 与其他 Azure 服务一样，与 Azure 机器学习服务相关联的某些资源存在限制（例如， BatchAI 群集大小）。 要了解默认限制以及申请更多配额的方法，请阅读[此](how-to-manage-quotas.md)文章。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="create-a-workspace"></a>创建工作区 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

在工作区页面上，单击 `Explore your Azure Machine Learning Workspace`

 ![浏览工作区](./media/quickstart-get-started/explore_aml.png)


## <a name="use-the-workspace"></a>使用工作区

现在看看工作区如何帮助你管理机器学习脚本。 在本部分，你将执行以下操作：

* 在 Azure Notebooks 中打开笔记本
* 运行会创建一些记录值的代码
* 在工作区中查看所记录的值

该示例展示了工作区如何帮助你跟踪脚本中生成的信息。 

### <a name="open-a-notebook"></a>打开笔记本 

Azure Notebooks 为 Jupyter 笔记本提供了一个免费的云平台，它预配置有运行 Azure 机器学习服务所需的一切内容。  

单击 `Open Azure Notebooks` 按钮进行第一项实验。

 ![启动 Azure Notebook](./media/quickstart-get-started/explore_ws.png)

登录后，会打开一个新的选项卡并显示 `Clone Library` 提示。  单击 `Clone`


### <a name="run-the-notebook"></a>运行笔记本

除了两个笔记本，还将显示 `config.json` 文件。  此配置文件包含刚才创建的工作区相关信息。  

单击 `01.run-experiment.ipynb` 打开笔记本。

可使用 `Shift`+`Enter` 一次运行一个单元。  或者，使用菜单 `Cells` > `Run All` 运行整个笔记本。

系统可能会提示你登录。  复制消息中的代码，然后单击链接并将代码粘贴到新窗口中。  请确保不要复制代码前后的空格。

 ![登录](./media/quickstart-get-started/login.png)

在笔记本中，第二个单元从 `config.json` 进行读取，以连接到工作区。
```
ws = Workspace.from_config()
```

第三个代码单元的开头是一个名为“my-first-experiment”的实验。  你将使用此名称查找工作区中的运行相关信息。

```
experiment = Experiment(workspace_object=ws, name = "my-first-experiment")
```

请注意，在笔记本的最后一个单元中，值写入到日志文件中。

```
# Log final results
run.log("Final estimate: ",pi_estimate)
run.log("Final error: ",math.pi-pi_estimate)
```

代码运行后，可在工作区中查看这些值。

## <a name="view-logged-values"></a>查看所记录的值

完成笔记本中的所有单元后，返回到门户页面。  

单击 `View Experiments`。

![查看实验](./media/quickstart-get-started/view_exp.png)

关闭 `Reports` 弹出窗口。

单击 `my-first-experiment`。

查看刚才执行的运行相关信息。  向下滚动页面以查找运行表，然后单击运行编号链接。

 ![运行历史记录链接](./media/quickstart-get-started/report.png)

你会看到自动创建的记录值的绘图：

   ![查看历史记录](./media/quickstart-get-started/plots.png)

## <a name="clean-up-resources"></a>清理资源 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

还可保留资源组，但显示工作区属性并选择“删除”按钮来删除单个工作区。

## <a name="next-steps"></a>后续步骤

现在，你已创建了必要的资源来开始试验和部署模型。 此外，在笔记本中运行了一些代码，并在云端工作区中查看了该代码的运行历史记录。

要深入体验工作流，请按照 Azure 机器学习教程来培训和部署模型。  

> [!div class="nextstepaction"]
> [教程：训练图像分类模型](tutorial-train-models-with-aml.md)
