---
title: 快速入门：在云中运行笔记本
titleSuffix: Azure Machine Learning service
description: Azure 机器学习服务入门。 在云中使用托管 Notebook 服务器来试用工作区。  该工作区是基础的云端块，用于进行机器学习模型的试验、训练和部署。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 0672d90a25bc4c879d28512ab212f98f29efbf3b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358216"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>快速入门：通过基于云的 Notebook 服务器开始使用 Azure 机器学习

本文介绍如何使用 Azure Notebooks 来运行在 Azure 机器学习服务[工作区](concept-azure-machine-learning-architecture.md)中记录的代码。 工作区是基础的云端块，用于通过机器学习进行机器学习模型的试验、训练和部署。 

本快速入门使用云资源，不需安装。 若要改用自己的环境，请参阅[快速入门：通过自己的 Notebook 服务器开始使用 Azure 机器学习](quickstart-run-local-notebook.md)。  
 
在本快速入门中，你将执行以下操作：

* 使用 Python 在 Jupyter 笔记本中连接到工作区。 该笔记本包含用于估计 PI 的代码，并在每次迭代时记录错误。 
* 在工作区中查看所记录的错误值。

如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

## <a name="prerequisite"></a>先决条件

1. [创建 Azure 机器学习工作区](setup-create-workspace.md#portal)（如果没有）。

1. 在 [Azure 门户](https://portal.azure.com/)中打开你的工作区。  了解如何[查找工作区](how-to-manage-workspace.md#view)。

## <a name="use-your-workspace"></a>使用工作区

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



了解如何通过工作区来管理机器学习脚本。 在本部分中执行以下步骤：

* 在 Azure Notebooks 中打开笔记本。
* 运行会创建一些记录值的代码。
* 在工作区中查看所记录的值。

该示例介绍如何通过工作区跟踪脚本中生成的信息。 

### <a name="open-a-notebook"></a>打开笔记本 

[Azure Notebooks](https://notebooks.azure.com) 为 Jupyter 笔记本提供了一个免费的云平台，它预配置有运行机器学习所需的一切内容。 可以从你的工作区启动此平台，开始使用 Azure 机器学习服务工作区。

1. 在工作区“概览”页上，选择“Azure Notebooks 入门”，在 Azure Notebooks 中尝试第一个试验。  Azure Notebooks 是一项单独的服务，可以用来在云中免费运行 Jupyter 笔记本。  当你使用服务的此链接时，系统会将有关如何连接到工作区的信息添加到你在 Azure Notebooks 中创建的库。

   ![浏览工作区](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. 登录 Azure Notebooks。  确保使用登录 Azure 门户的帐户进行登录。 组织可能需要[管理员许可](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent)才能让你登录。

1. 登录后，会打开一个新的选项卡并显示 `Clone Library` 提示。 克隆此库时，会将一组笔记本和其他文件加载到 Azure Notebooks 帐户中。  可以通过这些文件了解 Azure 机器学习的功能。

1. 取消选中“公共”，这样就不会与他人共享工作区信息。

1. 选择“克隆”。

   ![克隆库](./media/quickstart-run-cloud-notebook/clone.png)

1. 如果看到项目状态为“已停止”，请单击“在免费计算机上运行”，以便使用免费的笔记本服务器。

    ![在免费计算上运行项目](./media/quickstart-run-cloud-notebook/run-project.png)

### <a name="run-the-notebook"></a>运行笔记本

在此项目的文件列表中，可以看到一个 `config.json` 文件。 此配置文件包含在 Azure 门户中创建的工作区的相关信息。  代码可以使用此文件连接到工作区并向其添加信息。

1. 选择 **01.run-experiment.ipynb** 以打开笔记本。

1. 状态区域会告知你等待，直至内核启动。  内核就绪以后，此消息会消失。

    ![等待内核启动](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. 内核启动以后，请使用 **Shift+Enter** 一次运行一个单元。 也可选择“单元” > “全部运行”，运行整个笔记本。 如果在单元旁边看到星号 __*__，则表明单元仍在运行。 该单元的代码完成后，会显示一个数字。 

1. 按照笔记本中的说明，对 Azure 订阅进行身份验证。

运行完 Notebook 中的所有单元以后，即可在工作区中查看记录的值。

## <a name="view-logged-values"></a>查看所记录的值

1. `run` 单元的输出包含一个可以返回到 Azure 门户的链接，用于在工作区中查看试验结果。 

    ![查看试验](./media/quickstart-run-cloud-notebook/view-exp.png)

1. 单击“链接到 Azure 门户”，查看在工作区中的运行的相关信息。  此链接打开 Azure 门户中的工作区。

1. 你看到的已记录值的绘图已自动在工作区中创建。 只要使用相同的名称参数记录了多个值，系统就会自动为你生成绘图。

   ![查看历史记录](./media/quickstart-run-cloud-notebook/web-results.png)

由于估算 pi 的代码使用随机值，因此绘图会显示不同的值。  

## <a name="clean-up-resources"></a>清理资源 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

还可保留资源组，但请删除单个工作区。 显示工作区属性，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

你已创建了进行试验和部署模型所需的资源。 另外，已在 Notebook 中运行一些代码。 已在云工作区中浏览了该代码的运行历史记录。

若要深入体验工作流，请按照机器学习教程来训练和部署模型：  

> [!div class="nextstepaction"]
> [教程：训练映像分类模型](tutorial-train-models-with-aml.md)