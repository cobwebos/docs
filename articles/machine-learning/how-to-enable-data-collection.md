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
ms.openlocfilehash: 3c481a2e12d83e865025cd90e59e0eba572ad9a5
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771387"
---
# <a name="collect-data-for-models-in-production"></a>为生产环境中的模型收集数据

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Azure 机器学习监视 SDK 即将停用。 SDK 仍适用于当前使用 SDK 监视模型中数据偏移的开发人员。 但对于新客户，建议对 Application Insights 使用简化的[数据监视](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)。

本文介绍如何从 Azure 机器学习收集输入模型数据。 它还演示了如何将输入数据部署到 Azure Kubernetes Service （AKS）群集并将输出数据存储在 Azure Blob 存储中。

启用集合后，收集的数据可帮助你：

* [监视数据偏离](how-to-monitor-data-drift.md)，因为生产数据进入你的模型。

* 更好地决定何时重新训练或优化模型。

* 用收集的数据重新训练模型。

## <a name="what-is-collected-and-where-it-goes"></a>收集的内容及其目标位置

可以收集以下数据：

* 从部署在 AKS 群集中的 web 服务对输入数据进行建模。 *不*收集语音音频、图像和视频。
  
* 使用生产输入数据进行模型预测。

>[!NOTE]
> 此数据上的 Preaggregation 和 precalculations 当前不是收集服务的一部分。

输出保存在 Blob 存储中。 由于数据已添加到 Blob 存储中，因此可以选择你喜欢的工具来运行分析。

Blob 中输出数据的路径遵循以下语法：

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> 在版本0.1.0 答16之前的 Python Azure 机器学习 SDK 版本中，`designation` 参数命名为 "`identifier`"。 如果使用早期版本开发代码，则需要相应地对其进行更新。

## <a name="prerequisites"></a>必备组件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://aka.ms/AMLFree)。

- 必须安装 AzureMachine 学习工作区、包含脚本的本地目录和用于 Python 的 Azure 机器学习 SDK。 若要了解如何安装它们，请参阅[如何配置开发环境](how-to-configure-environment.md)。

- 需要将定型的机器学习模型部署到 AKS。 如果没有模型，请参阅[训练图像分类模型](tutorial-train-models-with-aml.md)教程。

- 需要 AKS 群集。 有关如何创建和部署的信息，请参阅[如何部署和位置](how-to-deploy-and-where.md)。

- [设置你的环境](how-to-configure-environment.md)并安装[Azure 机器学习监视 SDK](https://aka.ms/aml-monitoring-sdk)。

## <a name="enable-data-collection"></a>启用数据收集

无论通过 Azure 机器学习或其他工具部署的模型是什么，都可以启用数据收集。

若要启用数据收集，需执行以下操作：

1. 打开评分文件。

1. 在该文件的顶部添加[以下代码](https://aka.ms/aml-monitoring-sdk)：

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. 在 `init` 函数中声明数据集合变量：

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId*是一个可选参数。 如果您的模型不需要它，则不需要使用此方法。 使用*CorrelationId*有助于更轻松地与其他数据（如*LoanNumber*或*CustomerId*）进行映射。
    
    稍后将使用*标识符*参数在 blob 中生成文件夹结构。 您可以使用它来区分处理的数据中的原始数据。

1. 将以下代码行添加到 `run(input_df)` 函数：

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. 在 AKS 中部署服务时，*不*会将数据收集自动设置为**true** 。 更新配置文件，如以下示例中所示：

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    你还可以通过更改此配置来启用服务监视 Application Insights：

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. 若要创建新映像并部署机器学习模型，请参阅[如何部署和位置](how-to-deploy-and-where.md)。

如果你的环境文件和评分文件中已安装了依赖项，请按以下步骤启用数据收集：

1. 请参阅[Azure 机器学习](https://ml.azure.com)。

1. 打开你的工作区。

1. 选择 "**部署**" > **选择 "服务** > "**编辑**"。

   ![编辑服务](././media/how-to-enable-data-collection/EditService.PNG)

1. 在 "**高级设置**" 中，选择 "**启用模型数据收集**"。

    [![选择数据收集](./media/how-to-enable-data-collection/CheckDataCollection.png)](././media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   还可以选择 "**启用 AppInsights 诊断**" 以跟踪服务的运行状况。

1. 选择 "**更新**" 以应用更改。

## <a name="disable-data-collection"></a>禁用数据收集

您可以随时停止收集数据。 使用 Python 代码或 Azure 机器学习禁用数据收集。

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>选项 1-禁用 Azure 机器学习中的数据收集

1. 登录到[Azure 机器学习](https://ml.azure.com)。

1. 打开你的工作区。

1. 选择 "**部署**" > **选择 "服务** > "**编辑**"。

   [![选择 "编辑" 选项](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. 在 "**高级设置**" 中，清除 "**启用模型数据收集**"。

    [![清除 "数据收集" 复选框](./media/how-to-enable-data-collection/UncheckDataCollection.png)](././media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. 选择“更新”以应用更改。

你还可以在[Azure 机器学习](https://ml.azure.com)的工作区中访问这些设置。

### <a name="option-2---use-python-to-disable-data-collection"></a>选项 2-使用 Python 禁用数据收集

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>验证和分析数据

您可以选择一个首选项的工具来分析在 Blob 存储中收集的数据。

### <a name="quickly-access-your-blob-data"></a>快速访问 blob 数据

1. 登录到[Azure 机器学习](https://ml.azure.com)。

1. 打开你的工作区。

1. 选择“存储”。

    [![选择存储选项](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. 通过以下语法跟踪 blob 输出数据的路径：

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>使用 Power BI 分析模型数据

1. 下载并打开[Power BI Desktop](https://www.powerbi.com)。

1. 选择 "**获取数据**" 并选择 " [**Azure Blob 存储**](https://docs.microsoft.com/power-bi/desktop-data-sources)"。

    [![Power BI blob 安装程序](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. 添加存储帐户名称并输入存储密钥。 可以通过在 blob 中选择 "**设置**" > "**访问密钥**" 来找到此信息。

1. 选择**模型数据**容器，然后选择 "**编辑**"。

    [![Power BI 导航器](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 在查询编辑器中，单击 "**名称**" 列下的 "添加存储帐户"。

1. 在筛选器中输入模型路径。 如果只想查看特定年份或月份的文件，只需展开筛选器路径即可。 例如，若要仅查看三月数据，请使用以下筛选器路径：

   /modeldata/\<subscriptionid >/\<resourcegroupname >/\<workspacename >/\<webservicename >/\<modelname >/\<modelversion >/\<>/\<>/3

1. 基于**名称**值筛选与你相关的数据。 如果存储了预测和输入，则需要为每个都创建一个查询。

1. 选择 "**内容**" 列标题旁边的向下双箭头以合并这些文件。

    [![Power BI 内容](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. 选择“确定”。 数据预加载。

    [合并文件 ![Power BI](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. 选择 "**关闭并应用**"。

1. 如果添加了输入和预测，则表会按**RequestId**值自动排序。

1. 开始基于模型数据生成自定义报表。

### <a name="analyze-model-data-using-azure-databricks"></a>使用 Azure Databricks 分析模型数据

1. 创建[Azure Databricks 工作区](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。

1. 转到该 Databricks 工作区。

1. 在 Databricks 工作区中，选择 "**上传数据**"。

    [![选择 Databricks 上传数据选项](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. 选择 "**新建表**"，并选择 " **Azure Blob 存储**" > "**其他数据源** **" > 笔记本中的 "创建表"** 。

    [![Databricks 表创建](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 更新数据的位置。 下面是一个示例：

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks 安装程序](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. 按照模板上的步骤来查看和分析数据。
