---
title: 教程：创建第一个 ML 试验：设置
titleSuffix: Azure Machine Learning
description: 在本系列教程中，你将完成端到端的步骤，以开始在 Jupyter Notebook 中运行 Azure 机器学习 Python SDK。  第一部分介绍如何创建一个云笔记本服务器环境，以及如何创建一个工作区来管理试验和机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 08/28/2019
ms.openlocfilehash: 9bc5b9688a8cd568b47fe2dad88d6d007ceca0c4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004063"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>教程：开始使用 Python SDK 创建第一个 ML 试验

在本教程中，你将完成端到端的步骤，以开始使用 Jupyter Notebook 中运行的 Azure 机器学习 Python SDK。 本教程是**由两个部分组成的系列教程的第一部分**，介绍如何设置和配置 Python 环境，以及如何创建工作区来管理试验模型和机器学习模型。 [**第二部分**](tutorial-1st-experiment-sdk-train.md)是在本文的基础上编写的，介绍如何训练多个机器学习模型，以及如何使用 Azure 门户和 SDK 来管理模型。

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建要在下一篇教程中使用的 [Azure 机器学习工作区](concept-workspace.md)。
> * 创建一个基于云的 Jupyter 笔记本 VM，其中已安装并预配置了 Azure 机器学习 Python SDK。

如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

## <a name="create-a-workspace"></a>创建工作区

Azure 机器学习工作区是云中的基础资源，用于试验、训练和部署机器学习模型。 它将 Azure 订阅和资源组关联到 SDK 中一个易于使用的对象。 如果已有 Azure 机器学习工作区，请跳至[下一部分](#azure)。 否则，请立即创建一个。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>创建云笔记本服务器

本示例使用工作区中的云笔记本服务器来实现免安装的预配置体验。 如果你希望控制环境、包和依赖项，请使用[自己的环境](how-to-configure-environment.md#local)。

在工作区中创建云资源，以便开始使用 Jupyter 笔记本。 此资源是一个基于云的 Linux 虚拟机，其中预配置了运行 Azure 机器学习所需的一切组件。

1. 在 [Azure 门户](https://portal.azure.com/)中打开你的工作区。  如果不确定如何在门户中查找工作区，请参阅如何[查找工作区](how-to-manage-workspace.md#view)。

1. 在 Azure 门户的工作区页上，选择左侧的“笔记本 VM”。 

1. 选择“+新建”，创建一个笔记本 VM。 

     ![选择“新建 VM”](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. 为 VM 提供一个名称。 
   + 笔记本 VM 名称必须为 2 到 16 个字符。 有效字符为字母、数字和 - 字符。  
   + 名称还必须在 Azure 订阅中独一无二。

1. 然后选择“创建”  。 设置 VM 可能需要一些时间。

1. 等待状态更改为“正在运行”。 
   在 VM 运行以后，使用“笔记本 VM”部分启动 Jupyter Web 界面。 

1. 在 VM 的“URI”列中选择“Jupyter”。  

    ![启动 Jupyter 笔记本服务器](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

   此链接启动笔记本服务器并在新的浏览器标签页中打开 Jupyter 笔记本网页。此链接将仅适用于创建 VM 的人。 工作区的每个用户必须创建自己的 VM。


## <a name="next-steps"></a>后续步骤

在本教程中，你已完成以下任务：

* 创建了 Azure 机器学习工作区。
* 在工作区中创建并配置了云笔记本服务器。

在本教程的**第二部分**中，你将运行 `tutorial-1st-experiment-sdk-train.ipynb` 中的代码来训练机器学习模型。 

> [!div class="nextstepaction"]
> [教程：训练第一个模型](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> 如果你不打算按照本教程的第 2 部分或任何其他教程进行操作，则应该在不使用云笔记本服务器 VM 时[停止它](tutorial-1st-experiment-sdk-train.md#clean-up-resources)，以降低成本。


