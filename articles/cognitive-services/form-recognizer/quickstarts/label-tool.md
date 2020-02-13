---
title: 快速入门：使用示例标记工具 - 表单识别器来标记表单、训练模型和分析表单
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用表单识别器示例标记工具来手动标记表单文档。 然后，将通过带标记的文档来训练自定义模型，并使用该模型来提取键/值对。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 8ab673c1a268f5ab663e8f423dd9b60cdfde14ab
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118372"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>使用示例标记工具通过标签来训练表单识别器模型

在本快速入门中，将示例标记工具和表单识别器 REST API 结合使用，通过手动标记的数据训练自定义模型。 有关此功能的详细信息，请参阅概述的[通过标签进行训练](../overview.md#train-with-labels)部分。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

若要完成本快速入门，必须具备以下条件：

- 至少有六个相同类型的表单。 你将使用此数据训练模型并测试表单。 在本快速入门中可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)。 将训练文件上传到 Azure 存储帐户中 blob 存储容器的根目录。

## <a name="set-up-the-sample-labeling-tool"></a>设置示例标记工具

你将使用 Docker 引擎来运行示例标记工具。 请按以下步骤设置 Docker 容器。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。
1. 首先，在主计算机上安装 Docker。 主计算机可以是本地计算机（[Windows](https://docs.docker.com/docker-for-windows/)、[MacOS](https://docs.docker.com/docker-for-mac/) 或 [Linux](https://docs.docker.com/install/)）。 或者，可以使用 Azure 中的 Docker 托管服务，例如 [Azure Kubernetes 服务](https://docs.microsoft.com/azure/aks/index)、[Azure 容器实例](https://docs.microsoft.com/azure/container-instances/index)或[部署到 Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy?view=azs-1910) 的 Kubernetes 群集。 主计算机必须满足以下硬件要求：

    | 容器 | 最小值 | 建议|
    |:--|:--|:--|
    |示例标记工具|2 核心，4 GB 内存|4 核心，8 GB 内存|
    
1. 通过 `docker pull` 命令获取示例标记工具容器。
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. 现在，已准备好通过 `docker run` 运行容器。
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   此命令将使示例标记工具可通过 web 浏览器提供。 转到[http://localhost:3000](http://localhost:3000)。

> [!NOTE]
> 还可以使用表单识别器 REST API 来标记文档和训练模型。 若要使用 REST API 进行训练和分析，请参阅[使用 REST API 和 Python 通过标签进行训练](./python-labeled-data.md)。

## <a name="set-up-input-data"></a>设置输入数据

首先，请确保所有训练文档的格式相同。 如果有多种格式的表单，请根据通用格式将它们组织到子文件夹中。 训练时，需要将 API 定向到子文件夹。

### <a name="configure-cross-domain-resource-sharing-cors"></a>配置跨域资源共享 (CORS)

对存储帐户启用 CORS。 选择 Azure 门户中的存储帐户，并单击左窗格上的“CORS”选项卡  。 在底部行上，填写以下值。 然后单击顶部的“保存”  。

* 允许的来源 = * 
* 允许的方法 = \[全选\]
* 允许的标头 = *
* 公开的标头 = * 
* 最长时间 = 200

> [!div class="mx-imgBorder"]
> ![Azure 门户中的 CORS 设置](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>连接到示例标记工具

示例标签工具连接到源（原始表单所在的位置）和目标（导出创建的标签和输出数据的位置）。

可以在项目之间设置和共享连接。 它们使用可扩展的提供程序模型，因此你可以轻松地添加新的源/目标提供程序。

若要创建新连接，请单击左侧导航栏中的“新建连接”（插头）图标  。

在字段中填写以下值：

* **显示名称** - 连接显示名称。
* **说明** - 项目说明。
* **SAS URL** - Azure Blob 存储容器的共享访问签名 (SAS) URL。 若要检索 SAS URL，请打开 Microsoft Azure 存储资源管理器，右键单击容器，然后选择“获取共享访问签名”  。 将到期时间设置为你使用服务后的某个时间。 确保选中“读取”、“写入”、“删除”和“列表”权限，然后单击“创建”      。 然后复制 **URL** 部分中的值。 它应当采用 `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 形式。

![示例标记工具的连接设置](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>创建新项目

在示例标记工具中，项目会存储你的配置和设置。 创建新项目并在字段中填写以下值：

* **显示名称** - 项目显示名称
* **安全令牌** - 某些项目设置可以包含敏感值，如 API 密钥或其他共享机密。 每个项目都会生成一个安全令牌，可用于加密/解密敏感项目设置。 可以在“应用程序设置”中找到安全令牌，方法是单击左侧导航栏下方角的齿轮图标。
* **源连接** - 上一步中创建的、用于此项目的 Azure Blob 存储连接。
* **文件夹路径** -（可选）如果源表单位于 blob 容器上的某个文件夹中，请在此处指定文件夹名称
* **表单识别器服务 Uri** - 表单识别器终结点 URL。
* **API 密钥** - 表单识别器订阅密钥。
* **说明** -（可选）项目说明

![示例标记工具上的“新建项目”页](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>标记表单

创建或打开项目时，会打开主标记编辑器窗口。 标记编辑器由三部分组成：

* 一个可调整大小的预览窗格，其中包含源连接中的可滚动表单列表。
* 可以应用标记的主编辑器窗格。
* 通过标记编辑器窗格，用户可以修改、锁定、重新排序和删除标记。 

### <a name="identify-text-elements"></a>标识文本元素

单击左侧窗格中的“对所有文件运行 OCR"，以获取每个文档的文本布局信息  。 标记工具将在每个文本元素周围绘制边界框。

### <a name="apply-labels-to-text"></a>将标签应用于文本

接下来，你将创建标签，并将其应用于希望模型识别的文本元素。

1. 首先，使用标记编辑器窗格创建要标识的标记（标签）。
1. 在主编辑器中，单击并拖动以从突出显示的文本元素中选择一个或多个字词。

    > [!NOTE]
    > 当前无法选择跨多个页面的文本。
1. 单击要应用的标记，或按相应的键盘键。 只能对每个所选文本元素应用一个标记，并且每个标记在每个页面只能应用一次。

    > [!TIP]
    > 数字键被分配为前十个标记的热键。 可以使用标记编辑器窗格中的向上和向下箭头图标对标记进行重新排序。

按照上述步骤来标记五个表单，然后进行下一步。

![示例标记工具的主编辑器窗口](../media/label-tool/main-editor.png)


## <a name="train-a-custom-model"></a>训练自定义模型

单击左侧窗格中的训练图标（火车车厢），打开“训练”页。 然后单击“训练”按钮，开始训练模型  。 训练过程完成后，你将看到以下信息：

* **模型 ID** - 已创建和训练的模型的 ID。 每个训练调用都将创建一个具有自己的 ID 的新模型。 将此字符串复制到安全位置；如果要通过 REST API 进行预测调用，则需要使用此字符串。
* **平均准确度** - 模型的平均准确性。 可以通过再次标记其他表单和训练来创建新的模型，从而提高模型准确度。 建议首先标记五个表单，然后根据需要添加更多表单。
* 标记列表，以及每个标记的估计准确度。

![训练视图](../media/label-tool/train-screen.png)

训练完成后，检查“平均准确度”值  。 如果该值较低，应添加更多输入文档，并重复上述步骤。 已标记的文档将保留在项目索引中。

> [!TIP]
> 还可以使用 REST API 调用来运行训练过程。 若要了解如何执行此操作，请参阅[使用 Python 通过标签进行训练](./python-labeled-data.md)。

## <a name="analyze-a-form"></a>分析表单

单击左侧的“预测（矩形）”图标，测试模型。 上传训练过程中未使用的表单文档。 然后单击右侧的“预测”按钮，获取表单的键/值预测  。 该工具将在边界框中应用标记，并报告每个标记的置信度。

> [!TIP]
> 还可以通过 REST 调用运行分析 API。 若要了解如何执行此操作，请参阅[使用 Python 通过标签进行训练](./python-labeled-data.md)。

## <a name="improve-results"></a>改进结果

可能需要执行进一步的训练来改进模型，具体取决于报告的准确度。 完成预测后，检查每个已应用标记的置信度值。 如果平均准确度训练值高，但置信度分数低（或结果不准确），则应将用于预测的文件添加到训练集、进行标记并再次训练。

如果要分析的文档不同于训练中使用的文档，则报告的平均准确度、置信度分数和实际准确性可能会不一致。 请记住，用户查看某些文档时，虽然看起来很相似，但在 AI 模型中却有明显的区别。 例如，你可以使用具有两种变体的表单类型进行训练，其中训练集由 20% 变体 A 和 80% 变体 B 组成。在预测过程中，变体 A 文档的置信度分数可能会较低。

## <a name="save-a-project-and-resume-later"></a>保存项目并在稍后恢复

若要在其他时间或在另一个浏览器中恢复项目，需要保存项目的安全令牌，稍后再重新输入。 

### <a name="get-project-credentials"></a>获取项目凭据
转到项目设置页（滑块图标），并记下安全令牌名称。 然后，转到应用程序设置（齿轮图标），其中显示了当前浏览器实例中的所有安全令牌。 查找项目的安全令牌，并将其名称和密钥值复制到一个安全位置。

### <a name="restore-project-credentials"></a>还原项目凭据
若要恢复项目，首先需要创建到同一 blob 存储容器的连接。 请照此处的步骤进行操作。 然后，转到应用程序设置页（齿轮图标），查看项目的安全令牌是否存在。 如果没有，请添加一个新的安全令牌，并复制上一步中的令牌名称和密钥。 然后单击“保存设置”。 

### <a name="resume-a-project"></a>恢复项目
最后，转到主页（房子图标），然后单击“打开云项目”。 然后选择 blob 存储连接，并选择项目的 .vott 文件  。 应用程序将加载项目的设置，因为它具有安全令牌。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用表单识别器示例标记工具通过手动标记的数据来训练模型。 如果想要将标记工具集成到自己的应用程序中，请使用处理标记数据训练的 REST API。

> [!div class="nextstepaction"]
> [使用 Python 通过标签进行训练](./python-labeled-data.md)
