---
title: 教程：训练和部署模型 - Azure IoT Edge 上的机器学习
description: 在本教程中，我们将使用 Azure 机器学习训练机器学习模型，然后将该模型打包为可作为 Azure IoT Edge 模块部署的容器映像。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a5c754373ba9437c631e62acbb5d6d246db4c862
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650752"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>教程：训练和部署 Azure 机器学习模型

> [!NOTE]
> 有一系列教程介绍如何在 IoT Edge 上使用 Azure 机器学习，本文是其中的一篇。 如果你是直接转到本文的，建议从本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)开始，以获得最佳学习效果。

在本文中，我们将执行以下任务：

* 使用 Azure Notebooks 训练机器学习模型。
* 将训练的模型打包为容器映像。
* 将容器映像部署为 Azure IoT Edge 模块。

Azure Notebooks 利用 Azure 机器学习工作区，该工作区是用于试验、训练和部署机器学习模型的基块。

本文中的步骤通常由数据科学家执行。

## <a name="set-up-azure-notebooks"></a>设置 Azure Notebooks

我们将使用 Azure Notebooks 来托管两个 Jupyter Notebook 和支持文件。 在此处，我们将创建并配置一个 Azure Notebooks 项目。 如果你未曾用过 Jupyter Notebook 和/或 Azure Notebooks，请参阅下面的几篇简介文档：

* **快速入门：** [创建和共享笔记本](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **教程：** [使用 Python 创建并运行 Jupyter Notebook](../notebooks/tutorial-create-run-jupyter-notebook.md)

使用 Azure Notebooks 可以确保使用一致的环境来完成本练习。

> [!NOTE]
> 完成设置后，可以从任何计算机访问 Azure Notebooks 服务。 在设置期间应使用开发 VM，其中包含所需的全部文件。

### <a name="create-an-azure-notebooks-account"></a>创建 Azure Notebooks 帐户

若要使用 Azure Notebooks，需要创建一个帐户。 Azure Notebooks 帐户独立于 Azure 订阅。

1. 导航到 [Azure Notebooks](https://notebooks.azure.com)。

1. 在页面右上角单击“登录”。 

1. 使用工作或学校帐户 (Azure Active Directory) 或个人帐户（Microsoft 帐户）登录。

1. 如果你以前未曾用过 Azure Notebooks，系统会提示你为 Azure Notebooks 应用授予访问权限。

1. 输入 Azure Notebooks 的用户 ID。

### <a name="upload-jupyter-notebook-files"></a>上传 Jupyter Notebook 文件

我们将示例 Notebook 文件上传到新的 Azure Notebooks 项目中。

1. 在新帐户的用户页上，从顶部菜单栏中选择“我的项目”。 

1. 在“创建新项目”对话框中提供一个**项目名称**，该名称也会自动构成**项目 ID**。 

1. 将“公开”和“自述文件”保留未选中状态，因为项目不需要公开，也不需要附带自述文件。  

1. 选择“创建”  。

1. 选择“上传”（向上箭头图标），然后选择“从计算机”。  

1. 选择“选择文件”。 

1. 导航到 **C:\source\IoTEdgeAndMlSample\AzureNotebooks**。 选择列表中的所有文件，然后单击“打开”。 

1. 选择“上传”开始上传。上传过程完成后，选择“完成”。  

### <a name="azure-notebook-files"></a>Azure Notebook 文件

让我们查看已上传到 Azure Notebooks 项目的文件。 本教程部分中的活动跨越两个 Notebook 文件，这些文件使用几个支持文件。

* **01-turbofan\_regression.ipynb：** 此 Notebook 使用机器学习服务工作区来创建并运行机器学习试验。 概括而言，该 Notebook 执行以下步骤：

  1. 从 Azure 存储帐户下载设备装备生成的数据。
  1. 浏览并准备数据，然后使用数据训练分类器模型。
  1. 使用测试数据集 (Test\_FD003.txt) 通过试验评估模型。
  1. 将最佳的分类器模型发布到机器学习服务工作区。

* **02-turbofan\_deploy\_model.ipynb：** 此 Notebook 使用在上一个 Notebook 中创建的模型来创建一个随时可部署到 Azure IoT Edge 设备的容器映像。 该 Notebook 执行以下步骤：

  1. 创建模型的评分脚本。
  1. 使用机器学习服务工作区中保存的分类器模型生成容器映像。
  1. 将映像部署为 Azure 容器实例上的 Web 服务。
  1. 使用该 Web 服务来验证模型和映像是否按预期方式工作。 已验证的映像将部署到本教程的[创建并部署自定义 IoT Edge 模块](tutorial-machine-learning-edge-06-custom-modules.md)部分所述的 IoT Edge 设备中。

* **Test\_FD003.txt：** 此文件包含我们在验证已训练的分类器时用作测试集的数据。 为方便演示，我们已选择为原始竞赛提供的测试数据作为测试集。

* **RUL\_FD003.txt：** 此文件包含 RUL\_FD003.txt 文件中每个设备的最后一个周期的 RUL。 有关数据的详细说明，请参阅 C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan 中的 readme.txt 和 Damage Propagation Modeling.pdf 文件。

* **Utils.py：** 包含一组用于处理数据的 Python 实用工具函数。 第一个 Notebook 包含函数的详细说明。

* **README.md：** 描述 Notebook 用法的自述文件。  

## <a name="run-azure-notebooks"></a>运行 Azure Notebooks

创建该项目后，可以运行 Notebook。 

1. 在项目页中，选择“01-turbofan\_regression.ipynb”。 

    ![选择要运行的第一个 Notebook](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. 出现提示时，请在对话框中选择“Python 3.6 内核”，然后选择“设置内核”。 

1. 如果该 Notebook 列为“不受信任”，请单击 Notebook 右上角的“不受信任”小组件。   对话框出现后，选择“信任”。 

1. 在 Notebook 中，向下滚动到接在“设置全局属性”指令后面的、以代码 `AZURE_SUBSCRIPTION_ID =` 开头的单元，并填写 Azure 订阅、设置和资源的值。 

    ![在 Notebook 中设置全局属性](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. 选择工具栏上的“运行”来运行该单元。 

    运行某个单元时，方括号之间会显示一个星号 ([\*])。 该单元的操作完成后，星号将替换为数字，并可能显示相关的输出。 Notebook 中的单元按顺序生成，每次只能运行一个单元。

    请遵照 Notebook 中的说明操作。 还可以使用“单元”菜单中的运行选项、按 `Ctrl` + `Enter` 来运行单元，以及按 `Shift` + `Enter` 来运行某个单元并转到下一个单元。 

    > [!TIP]
    > 为使单元操作结果一致，请避免在浏览器的多个标签页中运行同一个 Notebook。

1. 向下滚动到紧接在“创建工作区”概述文本后面的单元，并运行该单元。  在该单元的输出中，查看指示你登录以进行身份验证的链接。 

    ![设备身份验证的登录提示](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    打开该链接并输入指定的代码。 此登录过程使用 Microsoft Azure 跨平台命令行接口对 Jupyter Notebook 访问 Azure 资源的行为进行身份验证。  

    ![确认已对设备上的应用程序进行身份验证](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. 此时，可以运行剩余的单元。 最好是运行所有单元，以便按顺序运行单元中的代码。 从“单元”菜单中选择“全部运行”。   向上滚动 Notebook，查看单元操作的完成方式。

    在“浏览数据”部分，可以查看“传感器读数和 RUL”子部分中的单元，其中显示了传感器测量值的散点图。  

    ![传感器读数散点图](media/tutorial-machine-learning-edge-04-train-model/sensor-readings.png)

1. 保存 Notebook，并单击 Notebook 右上角的项目名称返回到项目页，或者在浏览器中返回到项目页。

1. 打开 **02-turbofan\_deploy\_model.ipynb** 并重复本过程所述的步骤，以运行第二个 Notebook。

1. 保存 Notebook，并单击 Notebook 右上角的项目名称返回到项目页，或者在浏览器中返回到项目页。

### <a name="verify-success"></a>验证是否成功

若要验证 Notebook 是否已成功完成，请验证是否创建了几个项。

1. 在 Azure Notebooks 项目页上，选择“显示隐藏的项”，以显示以句点开头的项名称。 

1. 验证是否已创建以下文件：

    | 文件 | 说明 |
    | --- | --- |
    | ./aml_config/.azureml/config.json | 用于创建 Azure 机器学习工作区的配置文件。 |
    | ./aml_config/model_config.json | 在 Azure 上的 **turbofanDemo** 机器学习工作区中部署模型所需的配置文件。 |
    | myenv.yml| 提供有关已部署机器学习模型的依赖项的信息。|

1. 在 Azure 门户上验证资源组中是否存在 **turboFanDemo** 机器学习工作区。

### <a name="debugging"></a>调试

可将 Python 语句插入 Notebook 中进行调试（主要是插入 `print()` 命令）。 如果发现存在未定义的变量或对象，请运行首次声明或实例化它们的单元。

## <a name="next-steps"></a>后续步骤

在本文中，我们已使用 Azure Notebooks 中运行的两个 Jupyter Notebook 基于 turbofan 设备中的数据训练剩余使用寿命 (RUL) 分类器、将该分类器保存为模型、创建容器映像，然后测试该映像并将其部署为 Web 服务。

请继续学习下一篇文章了解如何创建 IoT Edge 设备。

> [!div class="nextstepaction"]
> [配置 IoT Edge 设备](tutorial-machine-learning-edge-05-configure-edge-device.md)
