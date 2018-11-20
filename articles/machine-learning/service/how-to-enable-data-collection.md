---
title: 为生产环境中的模型启用数据收集 - Azure 机器学习
description: 了解如何在 Azure Blob 存储中收集 Azure 机器学习输入模型数据。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 09/24/2018
ms.openlocfilehash: 70c023fc8fe996060d3eff3d5a700b5f910097b4
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113625"
---
# <a name="collect-data-for-models-in-production"></a>为生产环境中的模型收集数据

在本文中，你可以了解如何将已部署到 Azure Kubernetes 群集 (AKS) 的 Azure 机器学习服务中的输入模型数据收集到 Azure Blob 存储中。 

启用后，收集的这些数据可帮助你：
* 监视生产数据进入模型时的数据偏移

* 更好地决定何时重新训练或优化模型

* 使用收集的数据重新训练模型

## <a name="what-is-collected-and-where-does-it-go"></a>收集了哪些数据以及将其存储在何处？

可以收集以下数据：
* 来自 Azure Kubernetes 群集 (AKS) 中部署的 Web 服务的模型输入数据（不收集语音、图像和视频） 
  
* 使用生产输入数据进行模型预测。

> [!Note]
> 目前，此数据的预聚合或预计算不是该服务的一部分。   

输出将保存在 Azure Blob 中。 由于数据已添加到 Azure Blob 中，因此，可以选择自己喜欢的工具来运行分析。 

Blob 中输出数据的路径遵循以下语法：

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 一个 Azure 机器学习服务工作区、一个包含脚本的本地目录，以及所安装的用于 Python 的 Azure 机器学习 SDK。 了解如何通过[如何配置开发环境](how-to-configure-environment.md)一文满足这些先决条件。

- 要部署到 Azure Kubernetes 服务 (AKS) 的经过训练的机器学习模型。 如果没有模型，请参阅[训练图像分类模型](tutorial-train-models-with-aml.md)教程。

- [AKS 群集](how-to-deploy-to-aks.md)。

- 在[环境](how-to-configure-environment.md)中安装了以下依赖项和模块：
  + 在 Linux 上：
    ```shell
    sudo apt-get install libxml++2.6-2v5
    pip install azureml-monitoring
    ```

  + 在 Windows 上：
    ```shell
    pip install azureml-monitoring
    ```

## <a name="enable-data-collection"></a>启用数据收集
无论通过 Azure 机器学习服务或其他工具部署的模型如何，都可以启用数据收集。 

若要启用数据收集，需要执行以下操作：

1. 打开评分文件。 

1. 在该文件顶部，添加以下代码：

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. 在 `init()` 函数中声明数据集合变量：

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    CorrelationId 是一个可选参数，如果你的模型不需要该参数，则无需进行设置。 拥有 correlationId 确实可以帮助你更轻松地与其他数据进行映射。 （示例包括：LoanNumber、CustomerId 等）
    
    标识符稍后用于在 Blob 中生成文件夹结构，它可用于划分“原始”数据和“已处理”数据。

3.  将以下代码行添加到 `run(input_df)` 函数：

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. 在 AKS 中部署服务时，不会自动将数据收集设置为 true，因此，必须更新配置文件，例如： 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    还可以通过更改此配置来打开用于服务监视的 AppInsights：
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. [创建新图像并部署服务。](how-to-deploy-to-aks.md) 


如果已在环境文件和评分文件中安装了带有依赖项的服务，请通过以下方式启用数据收集：

1. 转到 [Azure 门户](https://portal.azure.com)。

1. 打开你的工作区。

1. 转到“部署” -> “选择服务” -> “编辑”。

   ![编辑服务](media/how-to-enable-data-collection/EditService.png)

1. 在“高级设置”中，取消选中“启用模型数据收集”。 

   ![取消选中“数据收集”](media/how-to-enable-data-collection/CheckDataCollection.png)

   在此窗口中，还可以选择“启用 Appinsights 诊断”以跟踪服务的运行状况。  

1. 选择“更新”以应用更改。


## <a name="disable-data-collection"></a>禁用数据收集
可以随时停止收集数据。 使用 Python 代码或 Azure 门户禁用数据收集。

+ 选项 1 - 在 Azure 门户中禁用： 
  1. 登录到 [Azure 门户](https://portal.azure.com)。

  1. 打开你的工作区。

  1. 转到“部署” -> “选择服务” -> “编辑”。

     ![编辑服务](media/how-to-enable-data-collection/EditService.png)

  1. 在“高级设置”中，取消选中“启用模型数据收集”。 

     ![取消选中“数据收集”](media/how-to-enable-data-collection/UncheckDataCollection.png) 

  1. 选择“更新”以应用更改。

* 选项 2 - 使用 Python 禁用数据收集：

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="example-notebook"></a>示例笔记本

[00.Getting Started/12.enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/12.enable-data-collection-for-models-in-aks) 笔记本演示了本文所述的概念。  

获取此笔记本：
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]