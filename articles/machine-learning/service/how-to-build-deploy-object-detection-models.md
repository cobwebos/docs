---
title: 使用用于计算机视觉的 Azure 机器学习包生成和部署对象检测模型。
description: 了解如何使用用于计算机视觉的 Azure 机器学习包来生成、训练、测试和部署计算机视觉对象检测模型。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ms.openlocfilehash: 44059de5a0ef0667b4268d9cdc2997162bab474a
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295194"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>使用 Azure 机器学习生成和部署对象检测模型

本文介绍如何使用**用于计算机视觉的 Azure 机器学习包**来训练、测试和部署 [Faster R-CNN](https://arxiv.org/abs/1506.01497) 对象检测模型。 

使用对象检测可以解决计算机视觉领域中的大量问题。 这些问题包括生成模型来查找图像中存在的不同数目的对象。 

使用此包生成和部署此模型时，需要经历以下步骤：
1.  数据集创建
2.  深度神经网络 (DNN) 模型定义
3.  模型训练
4.  计算和可视化
5.  Web 服务部署
6.  Web 服务负载测试

在此示例中，TensorFlow 用作深度学习框架，训练在配备 GPU 的计算机（例如[深度学习数据科学 VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)）本地进行，部署使用 Azure ML 操作化 CLI。

有关每个模块和类的详细参考，请查阅[包参考文档](https://aka.ms/aml-packages/vision)。

## <a name="prerequisites"></a>先决条件

1. 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

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
> [获取 Jupyter Notebook](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>加载示例数据

为了进行此演示，我们提供了一个由冰箱中的各种食品组成的数据集，其中包含 30 张图像，一共 8 个类（盒装蛋、酸奶、番茄酱、蘑菇、芥末、橙子、南瓜、水）。 每个 jpg 图像都有一个名称类似的注释 XML 文件。 

下图显示了建议的文件夹结构。 

![文件夹结构](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>图像注释

若要训练和评估对象检测器，需提供带注释的对象位置。 [LabelImg](https://tzutalin.github.io/labelImg) 是一种开源注释工具，可以用来注释图像。 LabelImg 以 Pascal-VOC 格式为每个图像编写一个可以通过此包读取的 XML 文件。 


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.evaluation import DetectionEvaluation
from cvtk.evaluation.evaluation_utils import graph_error_counts
from cvtk.utils import detection_utils

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

from matplotlib import pyplot as plt
# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>创建数据集

创建一个 CVTK 数据集，其中包含的一组图像带有相应的边界框注释。 在此示例中，使用的是“../sample_data/foods/training”文件夹中提供的冰箱图像。 仅支持 JPEG 图像。


```python
image_folder = "detection/sample_data/foods/train"
data_train = ObjectDetectionDataset.create_from_dir(dataset_name='training_dataset', data_dir=image_folder,
                                                    annotations_dir="Annotations", image_subdirectory='JPEGImages')

# Show some statistics of the training image, and also give one example of the ground truth rectangle annotations
data_train.print_info()
_ = data_train.images[2].visualize_bounding_boxes(image_size = (10,10))
```

    F1 2018-05-25 23:12:21,727 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-05-25 23:12:21,733 INFO azureml.vision:dataset creating dataset for scenario=detection 
    Dataset name: training_dataset
    Total classes: 8, total images: 25
    Label-wise object counts:
        Label eggBox: 20 objects
        Label joghurt: 20 objects
        Label ketchup: 20 objects
        Label mushroom: 20 objects
        Label mustard: 20 objects
        Label orange: 20 objects
        Label squash: 40 objects
        Label water: 20 objects
    Bounding box width and height distribution:
        Bounding box widths  0/5/25/50/75/95/100-percentile: 54/61/79/117/133/165/311 pixels
        Bounding box heights 0/5/25/50/75/95/100-percentile: 48/58/75/124/142/170/212 pixels
    


![png](media/how-to-build-deploy-object-detection-models/output_6_1.JPG)


## <a name="define-a-model"></a>定义模型

在此示例中，使用的是 Faster R-CNN 模型。 定义此模型时，可以提供各种参数。 有关这些参数以及用于训练的参数（见下一部分）的含义，可查看 CVTK 的 API 文档或 [Tensorflow 对象检测网站](https://github.com/tensorflow/models/tree/master/research/object_detection)。 有关 Faster R-CNN 模型的详细信息，可访问[此链接](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details)。 此模型基于 Fast R-CNN。有关它的详细信息，可参阅[此文档](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details)。


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>训练模型

进行训练时，可以从通过 ResNet50 完成的 COCO 训练的 Faster R-CNN 模型着手。 

若要训练检测器，可将代码中的训练步骤数设置为 350，使训练更快速地运行（配备 GPU 时，训练时间约为 5 分钟）。 在实践中，请将其设置为训练集中图像数的至少 10 倍。

在此示例中，检测器训练步骤数设置为 350 是为了加快训练速度。 但在实践中，最好是将步骤数设置为训练集中图像数的至少 10 倍。

训练的两个关键参数是：
- 训练模型的步骤数，用 num_seps 参数表示。 每个步骤在训练模型时，采用批大小为 1 的迷你批。
- 学习速度，可以通过 initial_learning_rate 设置

```python
print("tensorboard --logdir={}".format(my_detector.train_dir))

# to get good results, use a larger value for num_steps, e.g., 5000.
num_steps = 350
learning_rate = 0.001 # learning rate

start_train = time.time()
my_detector.train(dataset=data_train, num_steps=num_steps, 
                  initial_learning_rate=learning_rate)
end_train = time.time()
print(end_train-start_train)
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\train
    F1 2018-05-25 23:12:22,764 INFO azureml.vision:Fit starting in experiment  1125722225 
    F1 2018-05-25 23:12:22,767 INFO azureml.vision:model starting trainging for scenario=detection 
    Using existing checkpoint file that's saved at 'C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\models\detection\faster_rcnn_resnet50_coco_2018_01_28\model.ckpt.index'.
    TFRecords creation started.
    F1 2018-05-25 23:12:22,773 INFO On image 0 of 25
    TFRecords creation completed.
    Training started.
    Training progressing: step 0 ...
    Training progressing: step 100 ...
    Training progressing: step 200 ...
    Training progressing: step 300 ...
    F1 2018-05-25 23:18:02,730 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:18:10,722 INFO 2953 ops in the final graph.
    F1 2018-05-25 23:18:24,244 INFO azureml.vision:Fit finished in experiment  1125722225 
    Training completed.
    361.604615688324
    

可以使用 TensorBoard 来可视化训练进度。 TensorBoard 事件位于模型对象的 train_dir 属性所指定的文件夹中。 若要查看 TensorBoard，请执行以下步骤：
1. 将以“tensorboard --logdir”开头的打印输出复制到命令行并运行。 
2. 将从命令行返回的 URL 复制到 Web 浏览器，以便查看 TensorBoard。 

TensorBoard 应如以下屏幕截图所示。 填充训练文件夹需要一定的时间。 因此，如果 TensorBoard 第一次没有正确显示，请尝试重复步骤 1-2。  

![tensorboard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>评估模型

“evaluate”方法用于评估模型。 此函数需要使用 ObjectDetectionDataset 对象作为输入。 创建评估数据集时，可以使用创建训练数据集时使用的函数。 支持的指标是为 [PASCAL VOC 质询](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf)定义的“平均精度”。  


```python
image_folder = "detection/sample_data/foods/test"
data_val = ObjectDetectionDataset.create_from_dir(dataset_name='val_dataset', data_dir=image_folder)
eval_result = my_detector.evaluate(dataset=data_val)
```

    F1 2018-05-25 23:18:24,253 INFO azureml.vision:dataset creating dataset for scenario=detection 
    F1 2018-05-25 23:18:24,286 INFO On image 0 of 5
    F1 2018-05-25 23:18:29,300 INFO Starting evaluation at 2018-05-26-03:18:29
    F1 2018-05-25 23:18:32,403 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:33,158 INFO Detection visualizations written to summary with tag image-0.
    F1 2018-05-25 23:18:33,518 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:34,342 INFO Detection visualizations written to summary with tag image-1.
    F1 2018-05-25 23:18:34,714 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:35,470 INFO Detection visualizations written to summary with tag image-2.
    F1 2018-05-25 23:18:35,835 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:36,654 INFO Detection visualizations written to summary with tag image-3.
    F1 2018-05-25 23:18:37,010 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:37,798 INFO Detection visualizations written to summary with tag image-4.
    F1 2018-05-25 23:18:37,804 INFO Running eval batches done.
    F1 2018-05-25 23:18:37,805 INFO # success: 5
    F1 2018-05-25 23:18:37,806 INFO # skipped: 0
    F1 2018-05-25 23:18:38,119 INFO Writing metrics to tf summary.
    F1 2018-05-25 23:18:38,121 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/eggBox: 1.000000
    F1 2018-05-25 23:18:38,205 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/joghurt: 0.942857
    F1 2018-05-25 23:18:38,206 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/ketchup: 1.000000
    F1 2018-05-25 23:18:38,207 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mushroom: 1.000000
    F1 2018-05-25 23:18:38,208 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mustard: 1.000000
    F1 2018-05-25 23:18:38,209 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/orange: 1.000000
    F1 2018-05-25 23:18:38,210 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/squash: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/water: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/Precision/mAP@0.5IOU: 0.992857
    F1 2018-05-25 23:18:38,253 INFO Metrics written to tf summary.
    F1 2018-05-25 23:18:38,254 INFO Finished evaluation!
    

评估结果可以干净格式打印出来。


```python
# print out the performance metric values
for label_obj in data_train.labels:
    label = label_obj.name
    key = 'PASCAL/PerformanceByCategory/AP@0.5IOU/' + label
    print('{0: <15}: {1: <3}'.format(label, round(eval_result[key], 2)))
print('{0: <15}: {1: <3}'.format("overall:", round(eval_result['PASCAL/Precision/mAP@0.5IOU'], 2))) 
```

    joghurt        : 0.94
    squash         : 1.0
    mushroom       : 1.0
    eggBox         : 1.0
    ketchup        : 1.0
    mustard        : 1.0
    water          : 1.0
    orange         : 1.0
    overall:       : 0.99
    

同样，可以在训练集上计算模型的准确性。 这样做可确保将训练聚合成好的解决方案。 成功训练以后，训练集上的准确性通常接近 100%。

评估结果也可通过 TensorBoard 查看，其中包括 mAP 值以及带有预测的边界框的图像。 将以下代码中的打印输出复制到命令行窗口中，以便启动 TensorBoard 客户端。 在这里使用了端口值 8008，目的是避免与默认值 6006 冲突，后者用于查看训练状态。


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>为图像评分

对训练的模型的表现满意以后，即可使用模型对象的“score”函数为新图像评分。 返回的分数可以通过“visualize”函数可视化。 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = "./scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![png](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>保存模型

训练的模型可以保存到磁盘并加载回内存中，如以下代码示例所示。


```python
save_model_path = "./frozen_model/faster_rcnn.model"
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>加载保存的模型进行评分

若要使用保存的模型，请使用“load”函数将模型加载到内存中。 只需加载一次。 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

模型在加载后，可以用来为一个或一系列图像评分。 将为单个图像返回一个字典，其中包含“detection_boxes”、“detection_scores”、“num_detections”等项。 如果输入为图像列表，则会返回字典列表，一个字典对应于一个图像。 


```python
detections_dict = my_detector_loaded.score(image_path)
```

检测到的分数高于 0.5 的对象（包括标签、分数和坐标）可以打印出来。


```python
look_up = dict((v,k) for k,v in my_detector.class_map.items())
n_obj = 0
for i in range(detections_dict['num_detections']):
    if detections_dict['detection_scores'][i] > 0.5:
        n_obj += 1
        print("Object {}: label={:11}, score={:.2f}, location=(top: {:.2f}, left: {:.2f}, bottom: {:.2f}, right: {:.2f})".format(
            i, look_up[detections_dict['detection_classes'][i]], 
            detections_dict['detection_scores'][i], 
            detections_dict['detection_boxes'][i][0],
            detections_dict['detection_boxes'][i][1], 
            detections_dict['detection_boxes'][i][2],
            detections_dict['detection_boxes'][i][3]))    
        
print("\nFound {} objects in image {}.".format(n_obj, image_path))           
```

    Object 0: label=squash     , score=0.99, location=(top: 0.74, left: 0.30, bottom: 0.84, right: 0.42)
    Object 1: label=squash     , score=0.98, location=(top: 0.27, left: 0.21, bottom: 0.37, right: 0.33)
    Object 2: label=orange     , score=0.98, location=(top: 0.31, left: 0.39, bottom: 0.37, right: 0.48)
    Object 3: label=joghurt    , score=0.98, location=(top: 0.57, left: 0.29, bottom: 0.67, right: 0.39)
    Object 4: label=eggBox     , score=0.97, location=(top: 0.41, left: 0.53, bottom: 0.49, right: 0.69)
    Object 5: label=water      , score=0.95, location=(top: 0.23, left: 0.51, bottom: 0.37, right: 0.57)
    Object 6: label=mustard    , score=0.88, location=(top: 0.61, left: 0.47, bottom: 0.66, right: 0.57)
    Object 7: label=ketchup    , score=0.80, location=(top: 0.62, left: 0.62, bottom: 0.68, right: 0.72)
    
    Found 8 objects in image ../sample_data/foods/test\JPEGImages\10.jpg.
    

像之前一样将分数可视化。


```python
path_save = "./scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![png](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

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

**创建和设置群集部署环境**

只需设置部署环境一次。 如果没有部署环境，请遵照[这些说明](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup)立即设置部署环境。 

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
# ##### OPTIONAL - Interactive CLI setup helper ###### 
# # Interactive CLI setup helper, including model management account and deployment environment.
# # If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
# # UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT

# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Create deployment object
# It will use the current deployment environment (you can check it with CLI command "az ml env show").
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=my_detector, replicas=1)

# Alternatively, you can provide azure machine learning deployment cluster name (environment name) and resource group name
# to deploy your model. It will use the provided cluster to deploy. To do that, please uncomment the following lines to create 
# the deployment object.

# azureml_rscgroup = "<resource group>"
# cluster_name = "<cluster name>"
# deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=my_detector,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# create the webservice
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>使用 Web 服务

创建 Web 服务以后，即可使用部署的 Web 服务为图像打分。 有几种选项：

   - 可以使用部署对象直接对 Web 服务打分，即：deploy_obj.score_image(image_path_or_url) 
   - 也可使用服务终结点 URL 和服务密钥（本地部署的值为“None”），即：AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
   - 直接构造 HTTP 请求，为 Web 服务终结点评分（适合高级用户）。

### <a name="score-with-existing-deployment-object"></a>使用现有部署对象进行评分
```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = data_train.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])

```


```python
# Time image scoring
import timeit

num_images = 3
for img_index, img_obj in enumerate(data_train.images[:num_images]):
    print("Calling API for image {} of {}: {}...".format(img_index, num_images, img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>使用服务终结点 URL 和服务密钥进行评分
```
    AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
```


```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "http://xxx" # please replace with your own service url
service_key = "xxx" # please replace with your own service key

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])
```

### <a name="score-endpoint-with-http-request-directly"></a>直接使用 HTTP 请求为终结点评分
以下是一些示例代码，可以直接以 Python 构造 HTTP 请求。 可以使用其他编程语言执行此操作。


```python
def score_image_with_http(image, service_endpoint_url, service_key=None, parameters={}):
    """Score local image with http request

    Args:
        image (str): Image file path
        service_endpoint_url(str): web service endpoint url
        service_key(str): Service key. None for local deployment.
        parameters (dict): Additional request paramters in dictionary. Default is {}.


    Returns:
        str: serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    import json

    if service_key is None:
        headers = {'Content-Type': 'application/json'}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key)}
    payload = []
    encoded = None
    
    # Read image
    with open(image,'rb') as f:
        image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        
    # Convert your image to base64 string
    # image_in_base64 : "b'{base64}'"
    encoded = base64.b64encode(image_buffer.getvalue())
    image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
    payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
        json.loads(result[0])
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result[0]

```

### <a name="parse-serialized-result-from-webservice"></a>分析来自 Web 服务的序列化结果
来自 Web 服务的结果位于可以分析的 json 字符串中。


```python
image_path_or_url = image_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])
```


```python
# Parse result from json string
import numpy as np
parsed_result = TFFasterRCNN.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
```


```python
ax = detection_utils.visualize(image_path, parsed_result)
path_save = "./scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>后续步骤

请通过以下文章详细了解用于计算机视觉的 Azure 机器学习包：

+ 阅读[包的概述并了解其安装方法](https://aka.ms/aml-packages/vision)。

+ 浏览此包的[参考文档](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision)。

+ 了解 [Azure 机器学习的其他 Python 包](reference-python-package-overview.md)。
