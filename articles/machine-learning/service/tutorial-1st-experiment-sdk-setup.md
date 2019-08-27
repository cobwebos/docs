---
title: 教程：创建第一个 ML 试验：设置
titleSuffix: Azure Machine Learning service
description: 在本系列教程中，你将完成端到端的步骤，以开始在 Jupyter Notebook 中运行 Azure 机器学习 Python SDK。  第一部分介绍如何创建一个云笔记本服务器环境，以及如何创建一个工作区来管理试验和机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: cc16f3c8ea287e78d7b7b4d9a56f5a2e82c26f01
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515338"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment"></a>教程：开始创建第一个 ML 试验

在本教程中，你将完成端到端的步骤，以开始使用 Jupyter Notebook 中运行的 Azure 机器学习 Python SDK。 本教程是**由两个部分组成的系列教程的第一部分**，介绍如何设置和配置 Python 环境，以及如何创建工作区来管理试验模型和机器学习模型。 [**第二部分**](tutorial-1st-experiment-sdk-train.md)是在本文的基础上编写的，介绍如何训练多个机器学习模型，以及如何使用 Azure 门户和 SDK 来管理模型。

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建要在下一篇教程中使用的机器学习工作区。
> * 创建一个基于云的 Jupyter 笔记本 VM，其中已安装并预配置了 Azure 机器学习 Python SDK。

## <a name="prerequisites"></a>先决条件

本教程的唯一先决条件是提供一个 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

## <a name="create-a-workspace"></a>创建工作区

工作区是云中的基础资源，用于试验、训练和部署机器学习模型。 它将 Azure 订阅和资源组关联到 SDK 中一个易于使用的对象。 如果你已有一个 Azure 机器学习服务工作区，请跳转到[下一部分](#azure)。 否则，请立即创建一个。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>创建云笔记本服务器

本示例使用工作区中的云笔记本服务器来实现免安装的预配置体验。 如果你希望控制环境、包和依赖项，请使用[自己的环境](how-to-configure-environment.md#local)。

在工作区中创建云资源，以便开始使用 Jupyter 笔记本。 此资源是一个基于云的 Linux 虚拟机，其中预配置了运行 Azure 机器学习服务所需的一切组件。

1. 在 [Azure 门户](https://portal.azure.com/)中打开你的工作区。  如果不确定如何在门户中查找工作区，请参阅如何[查找工作区](how-to-manage-workspace.md#view)。

1. 在 Azure 门户的工作区页上，选择左侧的“笔记本 VM”。 

1. 选择“+新建”，创建一个笔记本 VM。 

     ![选择“新建 VM”](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. 为 VM 提供一个名称。 然后选择“创建”  。

    > [!NOTE]
    > 笔记本 VM 名称必须为 2 到 16 个字符。 有效字符为字母、数字和 - 字符。  名称还必须在 Azure 订阅中独一无二。

1. 等待状态更改为“正在运行”。 

### <a name="launch-jupyter-web-interface"></a>启动 Jupyter Web 界面

在 VM 运行以后，使用“笔记本 VM”部分启动 Jupyter Web 界面。 

1. 在 VM 的“URI”列中选择“Jupyter”。  

    ![启动 Jupyter 笔记本服务器](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

    此链接启动笔记本服务器并在新的浏览器标签页中打开 Jupyter 笔记本网页。此链接将仅适用于创建 VM 的人。 工作区的每个用户必须创建自己的 VM。

1. 在 Jupyter 笔记本网页上，顶部文件夹名称为用户名。  选择该文件夹。

    > [!TIP]
    > 此文件夹位于工作区中的[存储容器](concept-workspace.md#resources)上，而不是位于笔记本 VM 本身上。  可以删除笔记本 VM，但仍保留所有工作。  稍后创建新的笔记本 VM 时，它将加载此同一文件夹。 如果将工作区与他人共享，则他人会看到你的文件夹，你会看到他人的文件夹。

1. 打开 `samples-*` 子目录，然后打开 `tutorials/tutorial-1st-experiment-sdk-train.ipynb` 以运行本系列教程的**第二部分**。

## <a name="end"></a> 清理资源

如果你打算继续学习本系列教程的**第 2 部分**，请不要结束本部分。

### <a name="stop-the-notebook-vm"></a>停止笔记本 VM

如果你使用了云笔记本服务器，请停止未使用的 VM，以降低成本。

1. 在工作区中，选择“笔记本 VM”。 

   ![停止 VM 服务器](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. 从列表中选择 VM。

1. 选择“停止”  。

1. 准备好再次使用服务器时，选择“启动”  。

### <a name="delete-everything"></a>删除所有内容

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

还可保留资源组，但请删除单个工作区。 显示工作区属性，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本教程中，你已完成以下任务：

* 创建了 Azure 机器学习服务工作区。
* 在工作区中创建并配置了云笔记本服务器。

请继续学习本系列教程的**第 2 部分**，以训练一个简单的机器学习模型。

> [!div class="nextstepaction"]
> [教程：训练第一个模型](tutorial-1st-experiment-sdk-train.md)
