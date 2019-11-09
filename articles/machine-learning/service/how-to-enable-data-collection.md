---
title: 在生产模型上收集数据
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure Blob 存储中收集 Azure 机器学习输入模型数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 20bc148e392900aecb63ad393ec6e90cda65585a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839101"
---
# <a name="collect-data-for-models-in-production"></a>为生产环境中的模型收集数据
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> 此 SDK 即将停用。 此 SDK 仍适用于监视模型中数据偏移的开发人员，但大多数开发人员应将简化的[数据监视与 Application Insights 一起](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights)使用。 

在本文中，可以了解如何从已部署到 azure Kubernetes 群集（AKS）的 Azure 机器学习收集输入模型数据到 Azure Blob 存储。 

启用后，收集的这些数据可帮助你：
* [监视数据偏离](how-to-monitor-data-drift.md)，因为生产数据进入你的模型

* 更好地决定何时重新训练或优化模型

* 使用收集的数据重新训练模型

## <a name="what-is-collected-and-where-does-it-go"></a>收集了哪些数据以及将其存储在何处？

可以收集以下数据：
* 来自 Azure Kubernetes 群集 (AKS) 中部署的 Web 服务的模型输入数据（不收集语音、图像和视频） 
  
* 使用生产输入数据进行模型预测

> [!Note]
> 目前，此数据的预聚合或预计算不是该服务的一部分。   

输出将保存在 Azure Blob 中。 由于数据已添加到 Azure Blob 中，因此，可以选择自己喜欢的工具来运行分析。 

Blob 中输出数据的路径遵循以下语法：

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> 在之前的 SDK 版本中 `0.1.0a16` `designation` 参数命名为 `identifier`。 如果你的代码是使用早期版本开发的，则需要相应地进行更新。

## <a name="prerequisites"></a>先决条件

- 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用[Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)

- 已安装 Azure 机器学习工作区、一个包含脚本的本地目录以及用于 Python 的 Azure 机器学习 SDK。 了解如何使用[如何配置开发环境](how-to-configure-environment.md)文档来获取这些先决条件

- 要部署到 Azure Kubernetes 服务 (AKS) 的经过训练的机器学习模型。 如果没有，请参阅[训练图像分类模型](tutorial-train-models-with-aml.md)教程

- 一个 Azure Kubernetes 服务群集。 有关如何创建和部署到其中的信息，请参阅[如何部署和 where](how-to-deploy-and-where.md)文档

- [设置你的环境](how-to-configure-environment.md)并安装[监视 SDK](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>启用数据收集
无论通过 Azure 机器学习或其他工具部署的模型是什么，都可以启用数据收集。 

若要启用数据收集，需要执行以下操作：

1. 打开评分文件

1. 在该文件的顶部添加[以下代码](https://aka.ms/aml-monitoring-sdk)：

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. 在 `init()` 函数中声明数据集合变量：

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    CorrelationId 是一个可选参数，如果你的模型不需要该参数，则无需进行设置。 拥有 correlationId 确实可以帮助你更轻松地与其他数据进行映射。 （示例包括：LoanNumber、CustomerId 等）
    
    *标识符*以后用于构建 Blob 中的文件夹结构，它可用于划分 "原始" 数据与 "处理"

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

5. 若要创建新映像并部署服务，请参阅[如何部署和 where](how-to-deploy-and-where.md)文档


如果已在环境文件和评分文件中安装了带有依赖项的服务，请通过以下方式启用数据收集：

1. 中转到[Azure 机器学习 studio](https://ml.azure.com)

1. 打开工作区

1. 请参阅**部署** -> **选择服务** -> **编辑**

   ![编辑服务](media/how-to-enable-data-collection/EditService.PNG)

1. 在 "**高级设置**" 中，选择 "**启用模型数据收集**"

    [![选中“数据收集”](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   在此窗口中，还可以选择 "启用 Appinsights 诊断" 以跟踪服务的运行状况

1. 选择 "**更新**" 以应用更改


## <a name="disable-data-collection"></a>禁用数据收集
可以随时停止收集数据。 使用 Python 代码或 Azure 机器学习 studio 禁用数据收集。

+ 选项 1-在 Azure 机器学习 studio 中禁用： 
  1. 登录到[Azure 机器学习 studio](https://ml.azure.com)

  1. 打开工作区

  1. 请参阅**部署** -> **选择服务** -> **编辑**

     [![编辑选项](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. 在 "**高级设置**" 中，取消选中 "**启用模型数据收集**"

     [![取消选中“数据收集”](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. 选择 "**更新**" 以应用更改

  你还可以在[Azure 机器学习 studio](https://ml.azure.com)中的工作区中访问这些设置。

+ 选项 2 - 使用 Python 禁用数据收集：

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>验证数据并对其进行分析
可以选择任何偏好的工具来分析收集到 Azure Blob 中的数据。

快速访问 Blob 中的数据：

1. 登录到[Azure 机器学习 studio](https://ml.azure.com)

1. 打开工作区
1. 单击**存储**

    [![存储](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Blob 中输出数据的路径遵循以下语法：

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>通过 Power BI 分析模型数据

1. 下载并打开[Power BI Desktop](https://www.powerbi.com)

1. 选择 "**获取数据**"，然后单击 " [**Azure Blob 存储**](https://docs.microsoft.com/power-bi/desktop-data-sources)"

    [![PBI Blob 设置](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. 添加存储帐户名称并输入存储密钥。 可以在 blob 的**设置**中找到此信息 > > 访问密钥

1. 选择容器**modeldata** ，并单击 "**编辑**"

    [![PBI Navigator](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 在查询编辑器中，单击“名称”列的下面，并添加存储帐户 1。 在筛选器中为路径建模。 注意：如果只想查看特定年份或月份的文件，则只需展开筛选器路径即可。 例如，只需查看三月 data：/modeldata/subscriptionid >/resourcegroupname >/workspacename >/webservicename >/modelname >/modelversion >/designation >/年 >/3

1. 基于“名称”筛选相关的数据。 如果存储了**预测**和**输入**，则需要为每个

1. 单击 "**内容**" 列旁的双箭头以合并文件

    [![PBI 内容](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. 单击 "确定"，数据将预加载

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. 现在可以单击 "**关闭并应用**"

1.  如果添加了输入和预测，则表将自动与**RequestId**关联

1. 开始生成您的模型数据的自定义报表


### <a name="analyzing-model-data-using-databricks"></a>使用 Databricks 分析模型数据

1. 创建[Databricks 工作区](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. 中转到你的 Databricks 工作区

1. 在 databricks 工作区中，选择 "**上传数据**"

    [![DB 上传](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. 创建新表并选择**其他数据源**-> Azure Blob 存储-> 在笔记本中创建表

    [![DB 表](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 更新数据的位置。 下面是一个示例：

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. 按照模板上的步骤进行操作，以便查看和分析数据

## <a name="example-notebook"></a>示例笔记本

[how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) 笔记本演示了本文中的概念。  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
