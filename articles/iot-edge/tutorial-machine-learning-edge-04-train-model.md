---
title: 教程：训练和部署模型 - Azure IoT Edge 上的机器学习
description: 在本教程中，我们将使用 Azure 机器学习训练机器学习模型，然后将该模型打包为可作为 Azure IoT Edge 模块部署的容器映像。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57630b789233dd23e61398f445b434e4ba08b48e
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2020
ms.locfileid: "80236029"
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

1. 通过选择 **+** 按钮添加新项目。

1. 在“创建新项目”  对话框中，提供项目名称  。 

1. 将“公开”和“自述文件”保留未选中状态，因为项目不需要公开，也不需要附带自述文件。  

1. 选择“创建”  。

1. 选择“上传”（向上箭头图标），然后选择“从计算机”。  

1. 选择“选择文件”。 

1. 导航到 **C:\source\IoTEdgeAndMlSample\AzureNotebooks**。 选择列表中的所有文件，然后单击“打开”。 

1. 选中“我信任这些文件的内容”  框。

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

* **RUL\_FD003.txt：** 此文件包含 Test\_FD003.txt 文件中每个设备的最后一个周期的剩余使用寿命 (RUL)。 有关数据的详细说明，请参阅 C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan 中的 readme.txt 和 Damage Propagation Modeling.pdf 文件。

* **Utils.py：** 包含一组用于处理数据的 Python 实用工具函数。 第一个 Notebook 包含函数的详细说明。

* **README.md：** 描述 Notebook 用法的自述文件。  

## <a name="run-azure-notebooks"></a>运行 Azure Notebooks

创建该项目后，可以运行 Notebook。 

1. 在项目页中，选择“01-turbofan\_regression.ipynb”。 

    ![选择要运行的第一个 Notebook](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. 如果该 Notebook 列为“不受信任”，请单击 Notebook 右上角的“不受信任”小组件。   对话框出现后，选择“信任”。 

1. 为获得最佳结果，请阅读每个单元的文档，并单独运行它。 在工具栏上选择“运行”  。 稍后，你会发现运行多个单元非常方便。 你可以忽略升级和弃用警告。

    运行某个单元时，方括号之间会显示一个星号 ([\*])。 该单元的操作完成后，星号将替换为数字，并可能显示相关的输出。 Notebook 中的单元按顺序生成，每次只能运行一个单元。

    还可以使用“单元”菜单中的运行选项、按 `Ctrl` + `Enter` 来运行单元，以及按 `Shift` + `Enter` 来运行某个单元并转到下一个单元。 

    > [!TIP]
    > 为使单元操作结果一致，请避免在浏览器的多个标签页中运行同一个 Notebook。

1. 在“设置全局属性”指令后面的单元中，填写 Azure 订阅、设置和资源的值。  然后运行该单元。

    ![在 Notebook 中设置全局属性](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. 在“工作区详细信息”  之前的单元格中，在运行它后，查找指示你登录以进行身份验证的链接：

    ![设备身份验证的登录提示](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    打开该链接并输入指定的代码。 此登录过程使用 Microsoft Azure 跨平台命令行接口对 Jupyter Notebook 访问 Azure 资源的行为进行身份验证。  

    ![确认已对设备上的应用程序进行身份验证](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. 在“浏览结果”  之前的单元中，复制运行 ID 中的值，并将其粘贴为“重建运行”  后面的单元中的运行 ID。

   ![在单元之间复制运行 ID](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. 运行笔记本中的剩余单元。

1. 保存笔记本并返回到你的项目页面。

1. 打开 **02-turbofan\_deploy\_model.ipynb** 并运行每个单元。 你将需要在“配置工作区”  后面的单元中进行身份验证。

1. 保存笔记本并返回到你的项目页面。

### <a name="verify-success"></a>验证是否成功

若要验证 Notebook 是否已成功完成，请验证是否创建了几个项。

1. 在 Azure Notebooks 项目页上，选择“显示隐藏的项”，以显示以句点开头的项名称。 

1. 验证是否已创建以下文件：

    | 文件 | 说明 |
    | --- | --- |
    | ./aml_config/.azureml/config.json | 用于创建 Azure 机器学习工作区的配置文件。 |
    | ./aml_config/model_config.json | 在 Azure 上的 **turbofanDemo** 机器学习工作区中部署模型所需的配置文件。 |
    | myenv.yml| 提供有关已部署机器学习模型的依赖项的信息。|

1. 验证是否已创建以下 Azure 资源。 某些资源名称追加了随机字符。

    | Azure 资源 | 名称 |
    | --- | --- |
    | 机器学习工作区 | turborfanDemo |
    | 容器注册表 | turbofandemoxxxxxxxx |
    | Applications Insights | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | 存储 | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>调试

可以在用于调试的 Notebook 中插入 Python 语句（例如 `print()` 命令）来显示值。 如果发现存在未定义的变量或对象，请运行首次声明或实例化它们的单元。

如果需要重做笔记本，则你可能必须删除以前创建的文件和 Azure 资源。

## <a name="next-steps"></a>后续步骤

在本文中，我们已使用 Azure Notebooks 中运行的两个 Jupyter Notebook 基于 turbofan 设备中的数据训练剩余使用寿命 (RUL) 分类器、将该分类器保存为模型、创建容器映像，然后测试该映像并将其部署为 Web 服务。

请继续学习下一篇文章了解如何创建 IoT Edge 设备。

> [!div class="nextstepaction"]
> [配置 IoT Edge 设备](tutorial-machine-learning-edge-05-configure-edge-device.md)
