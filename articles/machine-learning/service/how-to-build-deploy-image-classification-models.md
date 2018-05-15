---
title: 使用用于计算机视觉的 Azure 机器学习包生成和部署图像分类模型。
description: 了解如何使用用于计算机视觉的 Azure 机器学习包来生成、训练、测试和部署计算机视觉图像分类模型。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: bd9f01e76c68fa41616818251b5b54553059cbcc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>使用 Azure 机器学习生成和部署图像分类模型

本文介绍如何使用**用于计算机视觉的 Azure 机器学习包** (AMLPCV) 来训练、测试和部署图像分类模型。 

使用图像分类可以解决计算机视觉领域中的大量问题。 这些问题包括生成可解答以下问题的模型：
+ 某个对象（例如，“狗”、“猫”、“船”等）是否在图像中存在？
+ 此患者的视网膜扫描显示眼疾严重程度属于哪一级别？

使用 AMLPCV 生成和部署此模型时，需要经历以下步骤：
1. 数据集创建
2. 图像可视化和批注
3. 图像增强
4. 深度神经网络 (DNN) 模型定义
5. 分类器训练
6. 计算和可视化
7. Web 服务部署
8. Web 服务负载测试

[CNTK](https://www.microsoft.com/cognitive-toolkit/) 用作深度学习框架，训练在配备 GPU 的计算机（例如 [深度学习数据科学 VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)）本地执行，部署使用 Azure 机器学习操作化 CLI。

有关每个模块和类的详细参考，请查阅[包参考文档](https://aka.ms/aml-packages/vision)。

## <a name="prerequisites"></a>先决条件

1. 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

1. 必须设置并安装以下帐户和应用程序：
   - 一个 Azure 机器学习试验帐户 
   - Azure 机器学习模型管理帐户
   - 已安装 Azure Machine Learning Workbench

   如果尚未创建或安装上述三项，请遵循 [Azure 机器学习快速入门和 Workbench 安装](../service/quickstart-installation.md)一文操作。 

1. 必须安装用于计算机视觉的 Azure 机器学习包。 在[此处](https://aka.ms/aml-packages/vision)了解如何安装此包。

## <a name="sample-data-and-notebook"></a>示例数据和 Notebook

### <a name="get-the-jupyter-notebook"></a>获取 Jupyter Notebook

自行下载 Notebook，以运行本文所述的示例。

> [!div class="nextstepaction"]
> [获取 Jupyter Notebook](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>加载示例数据

以下示例使用包含 63 张餐具图像的数据集。 每张图像标记为属于四种不同的类（碗、杯、刀叉、盘）中的一种。 此示例中的图像数量较少，以加速此示例的执行速度。 在实践中，应该为每个类至少提供 100 张图像。 所有图像位于 *"../sample_data/imgs_recycling/"* 中名为“bowl”、“cup”、“cutlery”和“plate”的子目录内。

![Azure 机器学习数据集](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="storage-context"></a>存储上下文

存储上下文用于确定各种输出文件（例如增强的图像或 DNN 模型文件）的存储位置。 有关存储上下文的详细信息，请参阅 [StorageContext 文档](https://review.docs.microsoft.com/en-us/python/api/cvtk.core.context.storagecontext?view=azure-python&branch=smoke-test)。 

一般情况下，不需要显式设置存储内容。 但是，为了避免达到 Azure Machine Learning Workbench 对项目大小施加的 25-MB 限制，请将用于计算机视觉的 Azure 机器学习包的输出目录设置为 Azure 机器学习项目外部的某个位置 ("../../../../cvtk_output")。 不再需要“cvtk_output”目录时，请务必将其删除。


```python
import warnings
warnings.filterwarnings("ignore")
import json, numpy as np, os, timeit 
from azureml.logging import get_azureml_logger
from imgaug import augmenters
from IPython.display import display
from sklearn import svm
from cvtk import ClassificationDataset, CNTKTLModel, Context, Splitter, StorageContext
from cvtk.augmentation import augment_dataset
from cvtk.core.classifier import ScikitClassifier
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix, basic_plot
import matplotlib.pyplot as plt
%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

# Set storage context.
out_root_path = "../../../cvtk_output"
Context.create(outputs_path=out_root_path, persistent_path=out_root_path, temp_path=out_root_path)
```




    {
        "storage": {
            "outputs_path": "../../../cvtk_output",
            "persistent_path": "../../../cvtk_output",
            "temp_path": "../../../cvtk_output"
        }
    }



## <a name="create-a-dataset"></a>创建数据集

导入依赖项并设置存储上下文后，可以创建数据集对象。

若要使用用于计算机视觉的 Azure 机器学习包创建该对象，请提供本地磁盘上图像的根目录。 此目录必须遵循与餐具数据集相同的常规结构，即，包含实际图像所在的子目录：
- root
    - label 1
    - label 2
    - ...
    - label n
  
若要训练不同数据集的图像分类模型，只需将以下代码中的根路径 `dataset_location` 更改为指向不同的图像。


```python
# Root image directory 
dataset_location = os.path.abspath(os.path.join(os.getcwd(), "../sample_data/imgs_recycling"))

dataset_name = 'recycling'
dataset = ClassificationDataset.create_from_dir(dataset_name, dataset_location)
print("Dataset consists of {} images with {} labels.".format(len(dataset.images), len(dataset.labels)))
print("Select information for image 2: name={}, label={}, unique id={}.".format(
    dataset.images[2].name, dataset.images[2]._labels[0].name, dataset.images[2]._storage_id))
```

    F1 2018-04-23 17:12:57,593 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-04-23 17:12:57,599 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Dataset consists of 63 images with 4 labels.
    Select information for image 2: name=msft-plastic-bowl20170725152154282.jpg, label=bowl, unique id=3.

数据集对象提供使用[必应图像搜索 API](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 下载图像的功能。 

支持两种类型的搜索查询： 
+ 常规文本查询
+ 图像 URL 查询

必须在 JSON 编码的文本文件中提供这些查询以及类标签。 例如：

```json
{
    "bowl": [
                    "plastic bowl",
                    "../imgs_recycling/bowl"
    ],
    "cup": [
                    "plastic cup",
                    "../imgs_recycling/cup",
                    "http://cdnimg2.webstaurantstore.com/images/products/main/123662/268841/dart-solo-ultra-clear-conex-tp12-12-oz-pet-plastic-cold-cup-1000-case.jpg"
    ],
    "cutlery": [
                    "plastic cutlery",
                    "../imgs_recycling/cutlery",
                    "http://img4.foodservicewarehouse.com/Prd/1900SQ/Fineline_2514-BO.jpg"
    ],
    "plate": [
                    "plastic plate",
                    "../imgs_recycling/plate"
    ]
}
```

此外，必须显式创建一个上下文对象来包含必应图像搜索 API 密钥。 这需要提供必应图像搜索 API 订阅。

## <a name="visualize-and-annotate-images"></a>可视化和批注图像

可使用以下小组件在数据集对象中可视化图像和正确的标签。 

如果遇到“未检测到小组件 Javascript”错误，请运行以下命令予以解决：
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
from ui_utils.ui_annotation import AnnotationUI
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Azure 机器学习数据集](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>增强图像

[`augmentation` 模块](https://docs.microsoft.com/en-us/python/api/cvtk.augmentation)提供使用 [imgaug](https://github.com/aleju/imgaug) 库中所述的所有转换来增强数据集对象的功能。 可将图像转换分组到单个管道中，这样，就会对每张图像同时应用该管道中的所有转换。 

如果想要单独应用或者以任何不同的方式应用不同的增强步骤，可以定义多个管道并将其传递给 *augment_dataset* 函数。 有关图像增强的详细信息和示例，请参阅 [imgaug 文档](https://github.com/aleju/imgaug)。

将增强的图像添加到训练集对于小型数据集而言特别有利。 由于 DNN 训练过程较慢（原因是训练图像的数量增多），我们建议在未增强的情况下开始试验。


```python
# Split the dataset into train and test  
train_set_orig, test_set = dataset.split(train_size = 0.66, stratify = "label")
print("Number of training images = {}, test images = {}.".format(train_set_orig.size(), test_set.size()))
```

    F1 2018-04-23 17:13:01,780 INFO azureml.vision:splitter splitting a dataset 
    F1 2018-04-23 17:13:01,805 INFO azureml.vision:dataset creating dataset for scenario=classification 
    F1 2018-04-23 17:13:01,809 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Number of training images = 41, test images = 22.
    


```python
augment_train_set = False

if augment_train_set:
    aug_sequence = augmenters.Sequential([
            augmenters.Fliplr(0.5),             # horizontally flip 50% of all images
            augmenters.Crop(percent=(0, 0.1)),  # crop images by 0-10% of their height/width
        ])
    train_set = augment_dataset(train_set_orig, [aug_sequence])
    print("Number of original training images = {}, with augmented images included = {}.".format(train_set_orig.size(), train_set.size()))
else:
    train_set = train_set_orig  
```

## <a name="define-dnn-models"></a>定义 DNN 模型

此包支持以下预先训练的深度神经网络模型： 
+ AlexNet
+ Resnet-18
+ Resnet-34
+ Resnet-50
+ Resnet-101
+ Resnet-152

可将这些 DNN 用作分类器或特征化器。 

[此文](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md)提供了有关网络的详细信息，[此文](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0)提供了“迁移学习”的基本简介。

此包的默认图像分类参数为 224x224 像素分辨率和 Resnet-18 DNN。 我们已选择这些参数，以便正常完成各种任务。 通常可以提高准确性，例如，可将图像分辨率增大到 500x500 像素，和/或选择更深度的模型 (Resnet-50)。 但是，更改参数也会导致训练时间显著增加。 请参阅有关[如何提高准确性](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models)的文章。


```python
# Default parameters (224 x 224 pixels resolution, Resnet-18)
lr_per_mb = [0.05]*7 + [0.005]*7 +  [0.0005]
mb_size = 32
input_resoluton = 224
base_model_name = "ResNet18_ImageNet_CNTK"

# Suggested parameters for 500 x 500 pixels resolution, Resnet-50
# (see in the Appendix "How to improve accuracy", last row in table)
# lr_per_mb   = [0.01] * 7 + [0.001] * 7 + [0.0001]
# mb_size    = 8
# input_resoluton = 500
# base_model_name = "ResNet50_ImageNet_CNTK"

# Initialize model
dnn_model = CNTKTLModel(train_set.labels,
                       base_model_name=base_model_name,
                       image_dims = (3, input_resoluton, input_resoluton))
```

    Successfully downloaded ResNet18_ImageNet_CNTK
    

## <a name="train-the-classifier"></a>训练分类器

可对预先训练的 DNN 选择以下方法之一。

  - **DNN 优化**：训练 DNN 来直接执行分类。 DNN 训练速度较慢时，通常可以获得最佳结果，因为在训练期间可以改善所有网络权重，从而提供最高的准确性。

  - **DNN 特征化**：按原样运行 DNN，以获取图像的低维表示形式（512、2048 或 4096 浮点）。 然后，将该表示形式用作输入来训练独立的分类器。 由于 DNN 保持不变，相比 DNN 优化，这种方法的速度要快得多，但准确性更差。 尽管如此，训练线性 SVM（如以下代码中所示）等外部分类器能够提供可靠的基线，并有助于了解问题的可能性。
  
可以使用 TensorBoard 来可视化训练进度。 激活 TensorBoard：
1. 按以下代码中所示添加参数 `tensorboard_logdir=PATH`
1. 在新控制台中使用命令 `tensorboard --logdir=PATH` 启动 TensorBoard 客户端。
1. 根据 TensorBoard 的指示打开 Web 浏览器（默认为 localhost:6006）。 


```python
# Train either the DNN or a SVM as classifier 
classifier_name = "dnn"

if classifier_name.lower() == "dnn":  
    dnn_model.train(train_set, lr_per_mb = lr_per_mb, mb_size = mb_size, eval_dataset=test_set) #, tensorboard_logdir=r"tensorboard"
    classifier = dnn_model
elif classifier_name.lower() == "svm":
    learner = svm.LinearSVC(C=1.0, class_weight='balanced', verbose=0)
    classifier = ScikitClassifier(dnn_model, learner = learner)
    classifier.train(train_set)
else:
    raise Exception("Classifier unknown: " + classifier)   
```

    F1 2018-04-23 17:13:28,238 INFO azureml.vision:Fit starting in experiment  1541466320 
    F1 2018-04-23 17:13:28,239 INFO azureml.vision:model starting training for scenario=classification 
    <class 'int'>
    1 worker
    Training transfer learning model for 15 epochs (epoch_size = 41).
    non-distributed mode
    Training 15741700 parameters in 53 parameter tensors.
    Training 15741700 parameters in 53 parameter tensors.
    Learning rate per minibatch: 0.05
    Momentum per minibatch: 0.9
    PROGRESS: 0.00%
    Finished Epoch[1 of 15]: [Training] loss = 2.820586 * 41, metric = 68.29% * 41 5.738s (  7.1 samples/s);
    Evaluation Set Error :: 29.27%
    Finished Epoch[2 of 15]: [Training] loss = 0.286728 * 41, metric = 9.76% * 41 0.752s ( 54.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[3 of 15]: [Training] loss = 0.206938 * 41, metric = 4.88% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 41.46%
    Finished Epoch[4 of 15]: [Training] loss = 0.098931 * 41, metric = 2.44% * 41 0.785s ( 52.2 samples/s);
    Evaluation Set Error :: 48.78%
    Finished Epoch[5 of 15]: [Training] loss = 0.046547 * 41, metric = 0.00% * 41 0.724s ( 56.6 samples/s);
    Evaluation Set Error :: 43.90%
    Finished Epoch[6 of 15]: [Training] loss = 0.059709 * 41, metric = 4.88% * 41 0.636s ( 64.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[7 of 15]: [Training] loss = 0.005817 * 41, metric = 0.00% * 41 0.710s ( 57.7 samples/s);
    Evaluation Set Error :: 14.63%
    Learning rate per minibatch: 0.005
    Finished Epoch[8 of 15]: [Training] loss = 0.014917 * 41, metric = 0.00% * 41 0.649s ( 63.2 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[9 of 15]: [Training] loss = 0.040539 * 41, metric = 2.44% * 41 0.777s ( 52.8 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[10 of 15]: [Training] loss = 0.024606 * 41, metric = 0.00% * 41 0.626s ( 65.5 samples/s);
    Evaluation Set Error :: 7.32%
    PROGRESS: 0.00%
    Finished Epoch[11 of 15]: [Training] loss = 0.004225 * 41, metric = 0.00% * 41 0.656s ( 62.5 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[12 of 15]: [Training] loss = 0.004364 * 41, metric = 0.00% * 41 0.702s ( 58.4 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[13 of 15]: [Training] loss = 0.007974 * 41, metric = 0.00% * 41 0.721s ( 56.9 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[14 of 15]: [Training] loss = 0.000655 * 41, metric = 0.00% * 41 0.711s ( 57.7 samples/s);
    Evaluation Set Error :: 4.88%
    Learning rate per minibatch: 0.0005
    Finished Epoch[15 of 15]: [Training] loss = 0.024865 * 41, metric = 0.00% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 4.88%
    Stored trained model at ../../../cvtk_output\model_trained\ImageClassification.model
    F1 2018-04-23 17:13:45,097 INFO azureml.vision:Fit finished in experiment  1541466320 
    


```python
# Plot how the training and test accuracy increases during gradient descent. 
if classifier_name == "dnn":
    [train_accs, test_accs, epoch_numbers] = classifier.train_eval_accs
    plt.xlabel("Number of training epochs") 
    plt.ylabel("Classification accuracy") 
    train_plot = plt.plot(epoch_numbers, train_accs, 'r-', label = "Training accuracy")
    test_plot = plt.plot(epoch_numbers, test_accs, 'b-.', label = "Test accuracy")
    plt.legend()
```

![png](media/how-to-build-deploy-image-classification-models/output_17_0.png)


## <a name="evaluate-and-visualize-model-performance"></a>评估和可视化模型性能

可以使用评估模块在独立的测试数据集中评估已训练模型的性能。 该模块计算的部分评估指标包括：
 
+ 准确性（默认为类平均）
+ PR 曲线
+ ROC 曲线
+ 曲线下面积
+ 混淆矩阵


```python
# Run the classifier on all test set images
ce = ClassificationEvaluation(classifier, test_set, minibatch_size = mb_size)

# Compute Accuracy and the confusion matrix
acc = ce.compute_accuracy()
print("Accuracy = {:2.2f}%".format(100*acc))
cm  = ce.compute_confusion_matrix()
print("Confusion matrix = \n{}".format(cm))

# Show PR curve, ROC curve, and confusion matrix
fig, ((ax1, ax2, ax3)) = plt.subplots(1,3)
fig.set_size_inches(18, 4)
graph_roc_curve(ce, ax=ax1)
graph_pr_curve(ce, ax=ax2)
graph_confusion_matrix(ce, ax=ax3)
plt.show()
```

    F1 2018-04-23 17:14:37,449 INFO azureml.vision:evaluation doing evaluation for scenario=classification 
    F1 2018-04-23 17:14:37,450 INFO azureml.vision:model scoring dataset for scenario=classification 
    Accuracy = 95.45%
    Confusion matrix = 
    [[ 0  1  0  1]
     [ 0  7  0  0]
     [ 0  0  2  0]
     [ 0  0  0 11]]
    


![png](media/how-to-build-deploy-image-classification-models/output_20_1.png)



```python
# Results viewer UI
labels = [l.name for l in dataset.labels] 
pred_scores = ce.scores #classification scores for all images and all classes
pred_labels = [labels[i] for i in np.argmax(pred_scores, axis=1)]

from ui_utils.ui_results_viewer import ResultsUI
results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Azure 机器学习数据集](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
from ui_utils.ui_precision_recall import PrecisionRecallUI
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Azure 机器学习数据集](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

## <a name="operationalization-deploy-and-consume"></a>操作化：部署和使用

操作化是指将模型和代码发布为 Web 服务，并使用这些服务生成业务结果的过程。 

训练模型后，可以使用 [Azure 机器学习 CLI](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning) 将该模型部署为 Web 服务以供使用。 可将模型部署到本地计算机或 Azure 容器服务 (ACS) 群集。 可以使用 ACS 手动缩放 Web 服务，也可以使用自动缩放功能进行缩放。

**使用 Azure CLI 登录**

运行以下 CLI 命令，使用具有有效订阅的 [Azure](https://azure.microsoft.com/) 帐户登录：
<br>`az login`

+ 若要切换到另一个 Azure 订阅，请使用命令：
<br>`az account set --subscription [your subscription name]`

+ 若要查看当前模型管理帐户，请使用命令：
  <br>`az ml account modelmanagement show`

**创建和设置部署环境**

只需设置部署环境一次。 如果没有部署环境，请遵照[这些说明](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup)立即设置部署环境。 

根据需要正确遵循本地或群集部署设置步骤。
+ Linux 和 Windows 10 计算机支持本地部署，但 Windows 数据科学 VM 或深度学习 VM 则不支持。 
+ Linux 和 Windows 支持群集环境部署。 

若要查看活动部署环境，请使用以下 CLI 命令：
<br>`az ml env show`
   
用于创建和设置部署环境的 Azure CLI 命令示例

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>管理 Web 服务和部署

使用以下 API 可将模型部署为 Web 服务、管理这些 Web 服务，以及管理部署。

|任务|API|
|----|----|
|创建部署对象|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|部署 Web 服务|`deploy_obj.deploy()`|
|为图像评分|`deploy_obj.score_image(local_image_path_or_image_url)`|
|删除 Web 服务|`deploy_obj.delete()`|
|生成不包含 Web 服务的 Docker 图像|`deploy_obj.build_docker_image()`|
|列出现有部署|`AMLDeployment.list_deployment()`|
|删除使用部署名称的服务|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**API 文档：** 有关每个模块和类的详细参考，请查阅[包参考文档](https://aka.ms/aml-packages/vision)。

**CLI 参考：** 有关部署的更高级操作，请参阅[模型管理 CLI 参考](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference)。

**Azure 门户中的部署管理**：可以在 [Azure 门户](https://ms.portal.azure.com/)中跟踪和管理部署。 在 Azure 门户中，使用机器学习模型管理帐户的名称找到其所在页面。 然后转到模型管理帐户页 >“模型管理”>“服务”。


```python
# ##### OPTIONAL###### 
# Interactive CLI setup helper, including model management account and deployment environment.
# If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
#
# UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT
#
# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
# # Optional. Persist your model on disk and reuse it later for deployment. 
# from cvtk import TFFasterRCNN, Context
# import os
# save_model_path = os.path.join(Context.get_global_context().storage.persistent_path, "saved_classifier.model")
# # Save model to disk
# dnn_model.serialize(save_model_path)
# # Load model from disk
# dnn_model = CNTKTLModel.deserialize(save_model_path)
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Optional Azure Machine Learning deployment cluster name (environment name) and resource group name
# If you don't provide here. It will use the current deployment environment (you can check with CLI command "az ml env show").
azureml_rscgroup = "<resource group>"
cluster_name = "<cluster name>"

# If you provide the cluster information, it will use the provided cluster to deploy. 
# Example: deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Create deployment object
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=dnn_model, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# Create the web service
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>使用 Web 服务 

将模型部署为 Web 服务后，可使用以下方法之一来为包含 Web 服务的图像评分：

- 使用 `deploy_obj.score_image(image_path_or_url)` 通过部署对象直接为 Web 服务评分

- 结合以下路径使用服务终结点 URL 和服务密钥（本地部署的值为“None”）：`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- 直接构造 HTTP 请求以便为 Web 服务终结点评分。 此选项适用于高级用户。

### <a name="score-with-existing-deployment-object"></a>使用现有部署对象进行评分

```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url with added paramters. Add softmax to score.
print("Score image url with added paramters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParamters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParamters.ADD_SOFTMAX:True})
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Time image scoring
import timeit

for img_index, img_obj in enumerate(test_set.images[:10]):
    print("Calling API for image {} of {}: {}...".format(img_index, len(test_set.images), img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
    print(return_json)
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>使用服务终结点 URL 和服务密钥进行评分

`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "" # please replace with your own service url
service_key = "" # please replace with your own service key
# score local image with file path
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key)
print("serialized_result_in_json:", serialized_result_in_json)

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```

### <a name="score-endpoint-with-http-request-directly"></a>直接使用 HTTP 请求为终结点评分

以下示例代码直接以 Python 构造 HTTP 请求。 但是，也可以使用其他编程语言执行此操作。


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional paramters in dictionary


    Returns:
        result (list): list of serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    routing_id = ""

    if service_key is None:
        headers = {'Content-Type': 'application/json',
                   'X-Marathon-App-Id': routing_id}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key), 'X-Marathon-App-Id': routing_id}
    payload = []
    for image in images:
        encoded = None
        # read image
        with open(image,'rb') as f:
            image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        # convert your image to base64 string
        encoded = base64.b64encode(image_buffer.getvalue())
        image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
        payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result

# Test with images
images = [test_set.images[0].storage_path, test_set.images[1].storage_path] # A list of local image files
score_image_list_with_http(images, service_endpoint_url, service_key)
```

### <a name="parse-serialized-result-from-web-service"></a>分析来自 Web 服务的序列化结果

Web 服务的输出是一个 JSON 字符串。 可以使用不同的 DNN 模型类来分析此 JSON 字符串。


```python
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Parse result from json string
import numpy as np
parsed_result = CNTKTLModel.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
# Map result to image class
class_index = np.argmax(np.array(parsed_result))
print("Class index:", class_index)
dnn_model.class_map
print("Class label:", dnn_model.class_map[class_index])
```


## <a name="next-steps"></a>后续步骤

请通过以下文章详细了解用于计算机视觉的 Azure 机器学习包：

+ 了解如何[提高此模型的准确性](how-to-improve-accuracy-for-computer-vision-models.md)。

+ 阅读[包的概述并了解其安装方法](https://aka.ms/aml-packages/vision)。

+ 浏览此包的[参考文档](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision)。

+ 了解 [Azure 机器学习的其他 Python 包](reference-python-package-overview.md)。
