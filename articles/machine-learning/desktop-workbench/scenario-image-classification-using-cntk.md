---
title: 使用 Azure 机器学习 Workbench 中的 CNTK 进行图像分类 | Microsoft 文档
description: 使用 Azure ML Workbench 训练、计算和部署自定义图像分类。
services: machine-learning
documentationcenter: ''
author: PatrickBue
ms.author: pabuehle
manager: mwinkle
ms.reviewer: marhamil, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 48c21638fe5756e6527288ed0fdc73dd9e331afd
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2018
ms.locfileid: "35631911"
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>使用 Azure 机器学习 Workbench 进行图像分类

图像分类方法可用于解决大量的计算机视觉问题。
其中包括构建模型，用于回答诸如以下问题：“图像中是否存在对象？”，其中对象可能是（例如）狗、汽车或船。 或者，回答更为复杂的问题，例如“此患者的视网膜扫描显示眼疾严重程度属于哪一级别？”。

本教程将介绍如何解决此类问题。 我们接下来会介绍如何使用 [Microsoft 认知工具包 (CNTK) ](https://docs.microsoft.com/cognitive-toolkit/) 来训练、计算和部署你自己的图像分类模型，以进行深度学习。
我们提供了示例图像，但读者也可以使用自己的数据集并训练自己的自定义模型。

计算机视觉解决方案通常需要专业知识来手动进行标识和实现所谓的*特征*，用于在图像中突出显示所需的信息。
在 2012 年，随着著名的 [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] 深度学习论文的发表，此手动方法也发生了变化，现在使用深度神经网络 (DNN) 可自动查找这些特征。
DNN 不仅使得图像分类领域取得了巨大进步，而且在其他计算机视觉问题（例如，对象检测和图像相似性）方面也得到了很大的发展。


## <a name="link-to-the-gallery-github-repository"></a>库 GitHub 存储库的链接
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>概述

本教程分为三个部分：

- 第 1 部分介绍如何使用预先训练的 DNN 作为特征器并在其输出中训练 SVM 来训练、计算和部署图像分类系统。
- 接着，第 2 部分介绍如何通过（例如）优化 DNN 而不是用它作为一个固定特征器来改进准确度。
- 第 3 部分介绍如何使用自己的数据集（而不是提供的示例图像），以及如何通过从网络上抓取图像来生成自己的数据集（如果需要）。

尽管不需要之前有机器学习和 CNTK 的经验，但这些都将有助于理解基本原理。 本教程中报告的准确性数字、训练时间等仅供参考，运行代码时的实际值几乎都是不同的。


## <a name="prerequisites"></a>先决条件

运行此示例的先决条件如下所示：

1. [Azure 帐户](https://azure.microsoft.com/free/)（有免费试用版可用）。
2. [Azure 机器学习 Workbench](../service/overview-what-is-azure-ml.md) 遵循[安装指南快速入门](../service/quickstart-installation.md)来安装程序并创建工作区。  
3. Windows 计算机。 必须使用 Windows 操作系统，因为 Workbench 仅支持 Windows 和 MacOS，而 Microsoft 认知工具包（我们用作深度学习库）仅支持 Windows 和 Linux。
4. 在第 1 部分中，执行 SVM 训练不需要专用的 GPU，但第 2 部分所述的优化 DNN 将需要使用。 如果你没有强大的 GPU，又希望在多个 GPU 上进行训练，又或者没有 Windows 计算机，请考虑使用具有 Windows 操作系统的 Azure 深度学习虚拟机。 有关“一键式”部署指南，请参阅[此处](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)。 部署完成后，请通过远程桌面连接来连接 VM，在其中安装 Workbench，然后从 VM 本地执行代码。
5. 需要安装 OpenCV 等多种 Python 库。 在 Workbench 上的“文件”菜单中单击“打开命令提示符”，运行以下命令安装这些依赖项：  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl` 从 http://www.lfd.uci.edu/~gohlke/pythonlibs/ 下载 OpenCV 滚轮之后（准确的文件名和版本可能会有变化）
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`
    - `jupyter nbextension enable --py widgetsnbextension`

### <a name="troubleshooting--known-bugs"></a>疑难解答/已知问题
- 第 2 部分需要使用 GPU，否则在尝试优化 DNN 时会引发错误：“尚未实现在 CPU 上进行批量规范化训练”。
- 通过减小 Minibatch（迷你批量）的大小（`PARAMETERS.py` 中的变量 `cntk_mb_size`），可以避免在 DNN 训练期间发生内存不足的错误。
- 代码使用 CNTK 2.2 进行了测试，并应在不做任何更改（或只有少量更改）的情况下在旧版本（最高至 v2.0）和更高版本上运行。
- 撰写本文时，Azure 机器学习 Workbench 尚无法显示大于 5 MB 的笔记本。 如果保存笔记本时显示所有单元格输出，则可能会生成这种较大的笔记本。 如果发生此错误，请从 Workbench 中的“文件”菜单打开命令提示符，执行 `jupyter notebook`，打开笔记本，清除所有输出，然后保存笔记本。 执行以上步骤后，笔记本将在 Azure 机器学习 Workbench 中再次正常打开。
- 此示例中所提供的全部脚本均需在本地执行，而不适用于 Docker 远程环境等场合。 所有笔记本都需使用名为“PROJECTNAME local”（例如，“myImgClassUsingCNTK local”）的本地项目内核的内核集来执行。

    
## <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

要使用本示例作为模板创建新项目，请执行以下操作：
1.  打开 Azure Machine Learning Workbench。
2.  在“项目”页上单击 + 号，然后选择“新建项目”。
3.  在“新建项目”窗格中，填写新项目的信息。
4.  在“搜索项目模板”搜索框中，键入“图像分类”并选择模板。
5.  单击“创建”。

执行这些步骤将创建如下所示的项目结构。 项目目录限制为小于 25 MB，因为 Azure 机器学习 Workbench 会在每次运行之后创建此文件夹的副本（以启用运行历史记录）。 因此，所有图像和临时文件都将保存到目录 ~/Desktop/imgClassificationUsingCntk_data 下（本文档中称为 DATA_DIR）。

  文件夹| 说明
  ---|---
  aml_config/|                           包含 Azure 机器学习 Workbench 配置文件的目录
  libraries/|                              包含所有 Python 和 Jupyter 帮助程序函数的目录
  notebooks/|                              包含所有笔记本的目录
  resources/|                              包含所有资源的目录（例如，时尚图像的 URL）
  scripts/|                              包含所有脚本的目录
  PARAMETERS.py|                       指定所有参数的 Python 脚本
  readme.md|                           本自述文档


## <a name="data-description"></a>数据说明

本教程使用包含多达 428 个图像组成的上衣纹理数据集作为运行示例。 每个图像将批注为三种不同的纹理（圆点、条纹、豹纹）之一。 我们使用了较少数量的图像，以便本教程可以快速执行。 但是，此代码已经过良好测试，可用于成千上万或更多个图像。 所有图像均使用必应图像搜索进行抓取，并已按[第 3 部分](#using-a-custom-dataset)的说明进行了手动批注。 带有各自属性的图像 URL 列在 /resources/fashionTextureUrls.tsv 文件中。

脚本 `0_downloadData.py` 将所有图像下载到 DATA_DIR/images/fashionTexture/ 目录。 这 428 个 URL 中可能有部分已断开。 这并不是问题，只是意味着我们用于培训和测试的图像数量略有减少。 此示例中所提供的全部脚本均需在本地执行，而不适用于 Docker 远程环境等场合。

下图展示了属性“圆点”（左侧）、“条纹”（中间）和“豹纹”（右侧）的示例。 批注是根据上衣来完成的。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>第 1 部分 - 模型训练和计算

在本教程的第一部分，我们将训练一个使用（但不修改）预先培训的深度神经网络的系统。 此预先训练的 DNN 用作特征器，训练的线性 SVM 用于预测给定图像的属性（“圆点”、“条纹”或“豹纹”）。

现在，我们将分步详细描述此方法，并介绍需要执行的脚本。 我们建议在每个步骤完成后都要检查写入的文件以及文件写入位置。

在一个位置（`PARAMETERS.py` 文件中）指定所有重要参数，并提供简短说明。




### <a name="step-1-data-preparation"></a>步骤 1：数据准备
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

笔记本 `showImages.ipynb` 可用于可视化图像，并根据需要更正其批注。 若要运行笔记本，请在 Azure Machine Learning Workbench 中打开它，单击“启动笔记本服务器”（如果显示此选项），将本地项目内核的名称更改为“PROJECTNAME local”（例如“myImgClassUsingCNTK local”），然后在笔记本中执行所有单元格。 如果出现“笔记本太大，无法显示”的错误，请参阅本文档中的疑难解答部分。
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

现在执行名为 `1_prepareData.py` 的脚本，这会将所有图像分配到训练集或测试集。 此分配是互斥的 - 没有训练图像可同时用于测试，反之亦然。 默认情况下，会从每个属性类中随机抽取 75% 的图像分配给训练，剩下 25% 的图像分配给测试。 脚本生成的所有数据保存在 DATA_DIR/proc/fashionTexture/ 文件夹中。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>步骤 2：优化深度神经网络
`Script: 2_refineDNN.py`

如本教程第 1 部分所述，预先训练的 DNN 保持不变（即不优化）。 但是，第 1 部分仍会执行名为 `2_refineDNN.py` 的脚本，因为（例如）它会加载预先训练的 [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) [2] 模型并进行修改，以得到更高的输入图像分辨率。 此步骤很快（几秒），且不需要 GPU。

在本教程的第 2 部分，修改 PARAMETERS.py 文件会导致 `2_refineDNN.py` 脚本也优化预先训练的 DNN。 默认情况下，在优化期间我们将训练 45 次。

在这两种情况下，最终模型随后将写入文件 DATA_DIR/proc/fashionTexture/cntk_fixed.model 中。

### <a name="step-3-evaluate-dnn-for-all-images"></a>步骤 3：计算所有图像的 DNN
`Script: 3_runDNN.py`

我们现在可以使用上一步中（可能经过优化）的 DNN 来特征化我们的图像。 给定一个图像作为 DNN 的输入，输出是该模型倒数第二层的 512 浮点数向量。 此向量的维度比图像本身要小得多。 尽管如此，如果服装具有圆点、条纹或豹纹纹理，则它应包含（甚至突出显示）图像中与识别图像属性相关的所有信息。

所有 DNN 图像表示将保存到文件 DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle 中。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>步骤 4：支持矢量机训练
`Script: 4_trainSVM.py`

在最后一步中计算的 512 浮点数表示现在用于训练 SVM 分类器：给定一个图像作为输入，SVM 为每个要呈现的属性输出一个分数。 在我们的示例数据集中，这意味着“条纹”、“圆点”和“豹纹”将分别有一个分数。

脚本 `4_trainSVM.py` 加载训练图像，针对正则化 (slack) 参数 C 的不同值训练 SVM，并使 SVM 保持最高的准确性。 分类准确性在控制台上打印，并在 Workbench 中绘制。 对于提供的纹理数据，这些值应分别为约 100% 和 88%。 最后，训练的 SVM 写入文件 DATA_DIR/proc/fashionTexture/cntkFiles/svm.np 中。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>步骤 5：计算和可视化
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

可以使用脚本 `5_evaluate.py` 度量已训练图像分类器的准确性。 该脚本使用训练的 SVM 分类器为所有测试图像评分，为每个图像分配得分最高的属性，并将预测的属性与真实批注进行比较。

脚本 `5_evaluate.py` 的输出如下所示。 计算了各个类的分类准确性，以及完整测试集的准确性（“总体准确性”）和单个准确性的平均值（“总体类准确性平均值”）。 100% 对应于可能最佳的准确性，0% 对应于最差的准确性。 对属性数量随机猜测平均可得到类准确性平均值为 1：在我们的事例中，此准确性为 33.33%。 如果使用较高的输入分辨率（例如 `rf_inputResoluton = 1000`），则可显著提高这些结果，但 DNN 计算时间也会变长。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

除了准确性之外，还会根据相应曲线下面积绘制 ROC 曲线（左图），并显示混淆矩阵（右图）：

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

最后，提供笔记本 `showResults.py` 以浏览测试图像并可视化其各自分类得分。 如步骤 1 中所述，此示例中的每个笔记本都需使用名为“PROJECTNAME local”的本地项目内核：
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>步骤 6：部署
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

训练的系统现在可以作为 REST API 进行发布。 笔记本 `deploy.ipynb` 对部署作有说明，部署基于 Azure Machine Learning Workbench 中的功能（请记住将内核设置成名为“PROJECTNAME local”的本地项目内核）。 另请参阅 [IRIS 教程](tutorial-classifying-iris-part-3.md)的杰出部署部分，了解关于部署的详细信息。

部署完成后，即可使用脚本 `6_callWebservice.py` 调用 Web 服务。 请注意，需要先在脚本中设置 Web 服务的 IP 地址（本地或云中）。 笔记本 `deploy.ipynb` 中介绍了如何查找此 IP 地址。








## <a name="part-2---accuracy-improvements"></a>第 2 部分 - 准确性改进

在第 1 部分，我们介绍了如何通过训练线型支持矢量机根据深度神经网络的 512 浮点数输出对图像进行分类。 此 DNN 已在数百万图像中预先训练，并且返回的倒数第二层作为特征矢量。 因为该 DNN 是按原样使用的，所以此方法比较快捷，但通常结果也较好。

我们现在将介绍几种改进第 1 部分中模型准确性的方法。 最重要的一点，我们将优化 DNN，而不是使其保持不变。

### <a name="dnn-refinement"></a>DNN 优化

我们可以直接在神经网络（而不是 SVM）中进行分类。 实现方法是：在预先训练的 DNN 中新添加一个最后层，其采用倒数第二层的 512 浮点数作为输入。 在 DNN 中进行分类的优点是，现在可以使用反向传播重新训练整个网络。 与按原样使用预先训练的 DNN 相比，此方法的分类准确性通常会更高，但训练时间要长得多（即使用 GPU）。

可以通过将 `PARAMETERS.py` 中的变量 `classifier` 从 `svm` 更改为 `dnn` 来训练神经网络（而不是 SVM）。 然后，如第 1 部分所述，需要再次执行除数据准备（步骤 1）和 SVM 训练（步骤 4）之外的所有脚本。 DNN 优化需要使用 GPU。 如果找不到 GPU，或者如果 GPU 已锁定（例如，被之前的 CNTK 运行锁定），则脚本 `2_refineDNN.py` 会引发一个错误。 DNN 训练可能会在某些 GPU 上引发内存不足的错误，但可以通过减少小批量大小（`PARAMETERS.py` 中的变量 `cntk_mb_size`）来避免此错误。

训练完成后，将优化的模型保存到 DATA_DIR/proc/fashionTexture/cntk_refined.model，并绘制一个图表，显示在训练过程中训练和测试分类错误的变化方式。 请注意，在该绘图中，训练集上的错误远小于测试集上的错误。 可以通过（例如）对退出率 `rf_dropoutRate` 使用较高的值，减少这种所谓的过度配适行为。
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

如下图所示，对提供的数据集使用 DNN 优化得到的准确性为 92.35%，使用之前为 88.92%（第 1 部分）。 具体而言，“圆点”图像显著提高，优化后 ROC 曲线下面积为 0.98，优化之前为 0.94。 我们使用的数据集较小，因此，运行代码得到的实际准确性有所不同。 这种差异的原因是随机效应，例如，将图像随机拆分为训练集和测试集。
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>运行历史记录跟踪

Azure 机器训练 Workbench 将每次运行的历史记录存储在 Azure 上，以便比较两次或多次相隔甚至长达数周的运行结果。 [Iris 教程](tutorial-classifying-iris-part-2.md)中对此有详细介绍。 以下屏幕截图也对此进行了说明，在下图中，我们比较了两次运行脚本 `5_evaluate.py` 的结果，一次使用 DNN 优化 `classifier = "dnn"`（运行次数 148），一次使用 SVM 训练 `classifier = "svm"`（运行次数 150）。

在第一个屏幕截图中，对于所有类，DNN 优化得到的准确性优于 SVM 训练。 第二个屏幕截图显示了正在跟踪的所有指标，包括分类器是什么。 此跟踪在脚本 `5_evaluate.py` 中通过调用 Azure 机器学习 Workbench 记录器实现。 此外，该脚本还将 ROC 曲线和混淆矩阵保存到“输出”文件夹。 此“输出”文件夹很特殊，因为其内容也被 Workbench 历史记录功能跟踪，因此，可以随时访问此输出文件，无论本地副本是否被覆盖。

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>参数优化

与大多数机器学习项目一样，新数据集要获得较好的结果，就需要小心优化参数，以及计算不同的设计决策。 为了帮助完成这些任务，在一个位置（`PARAMETERS.py` 文件中）指定所有重要参数，并提供简短说明。

最理想的改进方法包括：

- 数据质量：确保训练和测试集具有较高的质量。 即，正确批注图像，删除不明确的图像（例如，同时有条纹和圆点的服装），属性之间互斥（即，选择后每个图像恰好属于一个属性）。

- 如果相关对象在图像中很小，则已知图像分类方法将不能很好的进行分类。 在这些情况下，请考虑使用该[教程](https://github.com/Azure/ObjectDetectionUsingCntk)中所述的对象检测方法。
- DNN 优化：最重要的参数可能是学习率 `rf_lrPerMb`。 如果训练集的准确性（第 2 部分的第一个图）不接近于 0-5%，最可能的原因是学习率错误。 以 `rf_` 开头的其他参数都不太重要。 通常情况下，训练错误应以指数递减，且训练后接近于 0%。

- 输入分辨率：默认图像分辨率为 224 x 224 像素。 使用更高的图像分辨率（参数：`rf_inputResoluton`）（例如，448 x 448 或 896 x 896 像素），通常可以显著提高准确性，但会降低 DNN 优化的速度。 使用更高的图像分辨率是几乎免费的午餐，并且几乎总是可以提高准确性。

- DNN 过度配适：避免 DNN 优化期间训练和测试准确性之间差异过大（第 2 部分的第一个图）。 可以使用退出率 `rf_dropoutRate` 0.5 或更大的值并增加正则化矩阵权重 `rf_l2RegWeight` 来减小此差异。 如果 DNN 输入图像分辨率较高，则使用高退出率会特别有用。

- 请尝试通过将 `rf_pretrainedModelFilename` 从 `ResNet_18.model` 更改为 `ResNet_34.model` 或 `ResNet_50.model`，使用更深层的 DNN。 Resnet-50 模型不仅更深层，而且其倒数第二层的输出大小为 2048 浮点数（而 ResNet-18 和 ResNet-34 模型为 512 浮点数）。 训练 SVM 分类器时，这个增加的维度非常有用。

## <a name="part-3---custom-dataset"></a>第 3 部分 - 自定义数据集

在第 1 和第 2 部分，我们使用提供的上衣纹理图像训练并计算了图像分类模型。 现在，我们将介绍如何改用自定义用户提供的数据集。 或者，如果这一方法不可用，如何使用必应图像搜索生成并批注这类数据集。

### <a name="using-a-custom-dataset"></a>使用自定义数据集

首先，让我们来看看服装纹理数据的文件夹结构。 请注意，具有不同属性的所有图像在 DATA_DIR/images/fashionTexture/ 中是如何划分到不同子文件夹“圆点”、“豹纹”和“条纹”中。 此外，还要注意图像文件夹名称如何出现在 `PARAMETERS.py` 文件中：
```python
datasetName = "fashionTexture"
```

使用自定义数据集只需重现此文件夹结构，将所有图像按照其属性放在不同子文件夹中，并将这些子文件夹复制到用户指定的新目录 DATA_DIR/images/newDataSetName/ 中。 代码唯一需要更改的地方是将 `datasetName` 变量设置为 newDataSetName 然后，可以按顺序执行脚本 1-5，且所有中间文件将写入 DATA_DIR/proc/newDataSetName/。 代码不需要做任何其他改动。

每个图像只能分配给一个属性，这点非常重要。 例如，同时具有属性“动物”和“豹纹”是错误的，因为“豹纹”图像还会属于“动物”。 此外，最好删除不明确的图像，因为这些图像很难进行批注。



### <a name="image-scraping-and-annotation"></a>图像抓取和批注

为训练和测试收集足够数量的带批注图像可能很困难。 解决此问题的一种方法是从 Internet 抓取图像。 例如，查询“条纹 T 恤”的必应图像搜索结果将如下所示。 正如所料，大部分图像确实为条纹 T 恤。 可以轻松识别并删除少数不正确或不明确的图像（例如，第 1 列第 1 行或第 3 列第 2 行）。
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

若要生成大型多样化数据集，应使用多个查询。 例如，可以使用服装 {blouse, hoodie, pullover, sweater, shirt, t-shirt, vest} 和属性 {striped, dotted, leopard} 的所有组合自动合成 7\*3 = 21 个查询。 每个查询下载前 50 个图像，将会得到最多 21*50=1050 个图像。

与从必应图像搜索手动下载图像相比，改用[认知服务必应图像搜索 API](https://www.microsoft.com/cognitive-services/bing-image-search-api) 要方便得多，该 API 可使用给定的查询字符串返回一组图像 URL。

部分下载的图像会完全或几乎重复（例如，只有图像分辨率或 jpg 伪影不同）。 应删除这些重复项，以便训练和测试拆分不包含相同的图像。 可以使用基于哈希的方法删除重复的图像，此方法包括两个步骤：(i) 首先，计算所有图像的哈希字符串；(ii) 第二次将鼠标悬停在图像上时，只保留哈希字符串从未见过的图像。 放弃所有其他图像。 我们在 Python 库 `imagehash` 中发现了 `dhash` 方法，并在此[博客](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html)中进行了介绍，在将参数 `hash_size` 设置为 16 时此方法效果较好。 错误地删除某些非重复图像也没有关系，只要删除了大部分真正的重复项即可。





## <a name="conclusion"></a>结束语

本示例的部分要点概括如下：
- 训练、计算和部署图像分类模型的代码。
- 提供了演示图像，但很容易改编（更改一行代码）以使用自己的图像数据集。
- 实现最新专家功能，以基于迁移学习训练高准确性的模型。
- 使用 Azure 机器学习 Workbench 和 Jupyter 笔记本的交互式模型开发。


## <a name="references"></a>参考

[1] Alex Krizhevsky, Ilya Sutskever, and Geoffrey E. Hinton, [_ImageNet Classification with Deep Convolutional Neural Networks_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf)（基于深度卷积神经网络的 ImageNet 分类）。 NIPS 2012.  
[2] Kaiming He, Xiangyu Zhang, Shaoqing Ren, and Jian Sun, [_Deep Residual Learning for Image Recognition_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf)（用于图像识别的深度残差学习）。 CVPR 2016.
