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
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 18b92fe090895c3aa08c3c931dfa8bd12db0f2d3
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406455"
---
# <a name="collect-data-for-models-in-production"></a>为生产环境中的模型收集数据
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> This SDK is retiring soon. This SDK is still appropriate for developers monitoring data drift in models but most developers should use the simplified [data monitoring with Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

In this article, you can learn how to collect input model data from Azure Machine Learning you've deployed into Azure Kubernetes Cluster (AKS) into an Azure Blob storage. 

启用后，收集的这些数据可帮助你：
* [Monitor data drifts](how-to-monitor-data-drift.md) as production data enters your model

* 更好地决定何时重新训练或优化模型

* 使用收集的数据重新训练模型

## <a name="what-is-collected-and-where-does-it-go"></a>收集了哪些数据以及将其存储在何处？

可以收集以下数据：
* 来自 Azure Kubernetes 群集 (AKS) 中部署的 Web 服务的模型输入数据（不收集语音、图像和视频） 
  
* Model predictions using production input data

> [!Note]
> 目前，此数据的预聚合或预计算不是该服务的一部分。   

输出将保存在 Azure Blob 中。 由于数据已添加到 Azure Blob 中，因此，可以选择自己喜欢的工具来运行分析。 

Blob 中输出数据的路径遵循以下语法：

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> In versions of the SDK prior to `0.1.0a16` the `designation` argument was named `identifier`. If your code was developed with an earlier version, you will need to update accordingly.

## <a name="prerequisites"></a>必备组件

- 如果还没有 Azure 订阅，可以在开始前创建一个免费帐户。 立即试用 [Azure 机器学习的免费版或付费版](https://aka.ms/AMLFree)

- 已安装 Azure 机器学习工作区、一个包含脚本的本地目录以及用于 Python 的 Azure 机器学习 SDK。 Learn how to get these prerequisites using the [How to configure a development environment](how-to-configure-environment.md) document

- 要部署到 Azure Kubernetes 服务 (AKS) 的经过训练的机器学习模型。 If you don't have one, see the [train image classification model](tutorial-train-models-with-aml.md) tutorial

- 一个 Azure Kubernetes 服务群集。 For information on how to create and deploy to one, see the [How to deploy and where](how-to-deploy-and-where.md) document

- [Set up your environment](how-to-configure-environment.md) and install the [Monitoring SDK](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>启用数据收集
Data collection can be enabled regardless of the model being deployed through Azure Machine Learning or other tools. 

若要启用数据收集，需要执行以下操作：

1. Open the scoring file

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
    
    *Identifier* is later used for building the folder structure in your Blob, it can be used to divide “raw” data versus “processed”

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

5. To create a new image and deploy the service, see the [How to deploy and where](how-to-deploy-and-where.md) document


如果已在环境文件和评分文件中安装了带有依赖项的服务，请通过以下方式启用数据收集：

1. Go to [Azure Machine Learning studio](https://ml.azure.com)

1. Open your workspace

1. Go to **Deployments** -> **Select service** -> **Edit**

   ![编辑服务](media/how-to-enable-data-collection/EditService.PNG)

1. In **Advanced Settings**, select **Enable Model data collection**

    [![选中“数据收集”](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   In this window, you can also choose to "Enable Appinsights diagnostics" to track the health of your service

1. Select **Update** to apply the change


## <a name="disable-data-collection"></a>禁用数据收集
可以随时停止收集数据。 Use Python code or Azure Machine Learning studio to disable data collection.

+ Option 1 - Disable in Azure Machine Learning studio: 
  1. Sign in to [Azure Machine Learning studio](https://ml.azure.com)

  1. Open your workspace

  1. Go to **Deployments** -> **Select service** -> **Edit**

     [![编辑选项](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. In **Advanced Settings**, deselect **Enable Model data collection**

     [![取消选中“数据收集”](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Select **Update** to apply the change

  You can also access these settings in your workspace in [Azure Machine Learning studio](https://ml.azure.com).

+ 选项 2 - 使用 Python 禁用数据收集：

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>验证数据并对其进行分析
可以选择任何偏好的工具来分析收集到 Azure Blob 中的数据。

快速访问 Blob 中的数据：

1. Sign in to [Azure Machine Learning studio](https://ml.azure.com)

1. Open your workspace
1. Click on **Storage**

    [![存储](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Blob 中输出数据的路径遵循以下语法：

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>通过 Power BI 分析模型数据

1. Download and Open [Power BI Desktop](https://www.powerbi.com)

1. Select **Get Data** and click on [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources)

    [![PBI Blob 设置](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. 添加存储帐户名称并输入存储密钥。 You can find this information in your blob's **Settings** >> Access keys

1. Select the container **modeldata** and click on **Edit**

    [![PBI Navigator](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 在查询编辑器中，单击“名称”列的下面，并添加存储帐户 1。 在筛选器中为路径建模。 注意：如果只想查看特定年份或月份的文件，则只需展开筛选器路径即可。 For example, just look into March data: /modeldata/subscriptionid>/resourcegroupname>/workspacename>/webservicename>/modelname>/modelversion>/designation>/year>/3

1. 基于“名称”筛选相关的数据。 If you stored **predictions** and **inputs**, you'll need to create a query for each

1. Click on the double arrow aside the **Content** column to combine the files

    [![PBI 内容](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Click OK and the data will preload

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. You can now click **Close and Apply**

1.  If you added inputs and predictions, your tables will automatically correlate by **RequestId**

1. Start building your custom reports on your model data


### <a name="analyzing-model-data-using-databricks"></a>使用 Databricks 分析模型数据

1. Create a [Databricks workspace](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. Go to your Databricks workspace

1. In your databricks workspace, select **Upload Data**

    [![DB 上传](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Create New Table and select **Other Data Sources** -> Azure Blob Storage -> Create Table in Notebook

    [![DB 表](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 更新数据的位置。 下面是一个示例：

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Follow the steps on the template in order to view and analyze your data

## <a name="example-notebook"></a>示例笔记本

[how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) 笔记本演示了本文中的概念。  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
