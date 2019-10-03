---
title: 训练和部署模型 - Azure IoT Edge 上的机器学习 | Microsoft Docs
description: 使用 Azure 机器学习训练机器学习模型，然后将该模型打包为可作为 Azure IoT Edge 模块部署的容器映像。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: acf0b1984eb3e68858be6ed68731612448e672f4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432710"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>教程：训练和部署 Azure 机器学习模型

> [!NOTE]
> 本文是介绍如何在 IoT Edge 上使用 Azure 机器学习的教程系列的一部分。 如果你是直接转到本文的，建议你从该系列中的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)开始，以获得最佳学习效果。

在本文中，我们先使用 Azure Notebooks 通过 Azure 机器学习训练机器学习模型，然后将该模型打包为可作为 Azure IoT Edge 模块部署的容器映像。 Azure Notebooks 利用 Azure 机器学习服务工作区，该工作区是用于试验、训练和部署机器学习模型的基块。

本教程部分中的活动已划分到两个 Notebook。

* **01-turbofan\_regression.ipynb：** 此 Notebook 演练使用 Azure 机器学习训练和发布模型的步骤。 概括地说，涉及的步骤包括：

  1. 下载、准备和探索训练数据
  2. 使用服务工作区创建并运行机器学习试验
  3. 评估模型的试验结果
  4. 将最佳模型发布到服务工作区

* **02-turbofan\_deploy\_model.ipynb：** 此 Notebook 使用在上一个 Notebook 中创建的模型来创建一个随时可部署到 Azure IoT Edge 设备的容器映像。

  1. 创建模型的评分脚本
  2. 创建并发布映像
  3. 将映像部署为 Azure 容器实例上的 Web 服务
  4. 使用该 Web 服务来验证模型和映像是否按预期方式工作

本文中的步骤通常由数据科学家执行。

## <a name="set-up-azure-notebooks"></a>设置 Azure Notebooks

我们将使用 Azure Notebooks 来托管两个 Jupyter Notebook 和支持文件。 在此处，我们将创建并配置一个 Azure Notebooks 项目。 如果你未曾用过 Jupyter Notebook 和/或 Azure Notebooks，请参阅下面的几篇简介文档：

* **快速入门：** [创建和共享笔记本](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **教程：** [使用 Python 创建并运行 Jupyter Notebook](../notebooks/tutorial-create-run-jupyter-notebook.md)

与以前在开发人员虚拟机上一样，使用 Azure Notebooks 可以确保使用一致的环境来完成本练习。

> [!NOTE]
> 完成设置后，可以从任何计算机访问 Azure Notebooks 服务。 在设置期间应使用开发虚拟机，其中包含所需的全部文件。

### <a name="create-an-azure-notebooks-account"></a>创建 Azure Notebooks 帐户

Azure Notebooks 帐户独立于 Azure 订阅。 若要使用 Azure Notebooks，需要创建一个帐户。

1. 导航到 [Azure Notebooks](https://notebooks.azure.com)。

2. 在页面右上角单击“登录”。 

3. 使用工作或学校帐户 (Azure Active Directory) 或个人帐户（Microsoft 帐户）登录。

4. 如果你以前未曾用过 Azure Notebooks，系统会提示你为 Azure Notebooks 应用授予访问权限。

5. 输入 Azure Notebooks 的用户 ID。

### <a name="upload-jupyter-notebooks-files"></a>上传 Jupyter Notebook 文件

在此步骤中，我们将创建一个新的 Azure Notebooks 项目并将文件上传到其中。 具体而言，要上传的文件包括：

* **01-turbofan\_regression.ipynb**：Jupyter Notebook 文件，用于演练以下过程：从 Azure 存储帐户下载设备装备生成的数据；探索并准备用于训练分类器的数据；训练模型；使用 Test\_FD003.txt 文件中的测试数据集测试数据；最后，在机器学习服务工作区中保存分类器模型。

* **02-turbofan\_deploy\_model.ipynb：** Jupyter Notebook，引导你完成使用机器学习服务工作区中保存的分类器模型生成容器映像的过程。 创建映像后，该 Notebook 会引导你完成将映像部署为 Web 服务的过程，以便可以验证该映像是否按预期方式工作。 已验证的映像将部署到本教程的[创建并部署自定义 IoT Edge 模块](tutorial-machine-learning-edge-06-custom-modules.md)部分所述的 Edge 设备中。

* **Test\_FD003.txt：** 此文件包含我们在验证已训练的分类器时用作测试集的数据。 为方便演示本示例，我们已选择为原始竞赛提供的测试数据作为测试集。

* **RUL\_FD003.txt：** 此文件包含 RUL\_FD003.txt 文件中每个设备的最后一个周期的 RUL。 有关数据的详细说明，请参阅 C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan 中的 **readme.txt** 和 **Damage Propagation Modeling.pdf** 文件。

* **Utils.py：** 包含一组用于处理数据的 Python 实用工具函数。 第一个 Notebook 包含函数的详细说明。

* **README.md：** 描述 Notebook 用法的自述文件。

创建一个新项目，并将文件上传到 Notebook。

1. 在顶部菜单栏中选择“我的项目”。 

1. 选择“+ 新建项目”。  提供名称和 ID。 项目不需要公开，也不需要附带自述文件。

1. 依次选择“上传”、“从计算机”。  

1. 选择“选择文件”。 

1. 导航到 **C:\source\IoTEdgeAndMlSample\AzureNotebooks**。 选择所有文件，然后单击“打开”。 

1. 选择“上传”开始上传。上传过程完成后，选择“完成”。  

## <a name="run-azure-notebooks"></a>运行 Azure Notebooks

创建项目后，运行 **01-turbofan\_regression.ipynb** Notebook。

1. 在 turbofan 项目页中，选择“01-turbofan\_regression.ipynb”。 

    ![选择要运行的第一个 Notebook](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. 出现提示时，请在对话框中选择“Python 3.6 内核”，然后选择“设置内核”。 

3. 如果该 Notebook 列为“不受信任”，请单击 Notebook 右上角的“不受信任”小组件。   对话框出现后，选择“信任”。 

4. 请遵照 Notebook 中的说明操作。

    * 按 `Ctrl + Enter` 会运行一个单元。
    * 按 `Shift + Enter` 会运行一个单元并导航到下一个单元。
    * 运行某个单元时，方括号之间会出现一个星号，例如 **[\*]** 。 完成后，星号将替换为数字，下面可能会显示相关的输出。 由于单元通常是基于前面的工作生成的，因此每次只能运行一个单元。

5. 运行完 **01-turbofan\_regression.ipynb** Notebook 后，返回到项目页。

6. 打开 **02-turbofan\_deploy\_model.ipynb** 并重复本部分所述的步骤，以运行第二个 Notebook。

## <a name="next-steps"></a>后续步骤

在本文中，我们已使用 Azure Notebooks 中运行的两个 Jupyter Notebook 基于 turbofan 设备中的数据训练剩余使用寿命 (RUL) 分类器、将该分类器保存为模型、创建容器映像，然后测试该映像并将其部署为 Web 服务。

请继续学习下一篇文章了解如何创建 IoT Edge 设备。

> [!div class="nextstepaction"]
> [配置 IoT Edge 设备](tutorial-machine-learning-edge-05-configure-edge-device.md)
