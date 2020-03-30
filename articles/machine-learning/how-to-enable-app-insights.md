---
title: 监视机器学习 Web 服务终结点以及从中收集数据
titleSuffix: Azure Machine Learning
description: 使用 Azure Application Insights 监视通过 Azure 机器学习部署的 Web 服务
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 03/12/2020
ms.openlocfilehash: 464ec1fcf0986dc04bd92bbe9e31b5675e5822d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136187"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>监视机器学习 Web 服务终结点以及从中收集数据
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您将了解如何通过通过启用 Azure 应用程序见解，从 Azure Kubernetes 服务 （AKS） 或 Azure 容器实例 （ACI） 中部署到 Web 服务终结点的模型收集数据并监视模型 
* [Azure 机器学习 Python SDK](#python)
* [Azure 机器学习工作室](#studio)https://ml.azure.com

除了收集终结点的输出数据和响应外，您还可以监视：

* 请求速率、响应时间和失败率
* 依赖项速率、响应时间和失败率
* 异常

[了解有关 Azure 应用程序见解 的更多详细信息](../azure-monitor/app/app-insights-overview.md)。 


## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费或付费版本的 Azure 机器学习](https://aka.ms/AMLFree)

* 已安装 Azure 机器学习工作区、一个包含脚本的本地目录以及用于 Python 的 Azure 机器学习 SDK。 要了解如何获取这些先决条件，请参阅[如何配置开发环境](how-to-configure-environment.md)

* 要部署到 Azure Kubernetes 服务 (AKS) 或 Azure 容器实例 (ACI) 的经过训练的机器学习模型。 如果没有，请参阅[训练图像分类模型](tutorial-train-models-with-aml.md)教程

## <a name="web-service-metadata-and-response-data"></a>Web 服务元数据和响应数据

>[!Important]
> Azure 应用程序见解仅记录高达 64kb 的有效负载。 如果达到此限制，则只会记录模型的最新输出。 

元数据和对服务的响应（对应于 Web 服务元数据和模型的预测）记录到消息 `"model_data_collection"` 下的 Azure Application Insights 跟踪中。 你可以直接查询 Azure Application Insights 来访问此数据，或者设置到存储帐户的[连续导出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)以保留更长时间或进一步进行处理。 然后，可以在 Azure 机器学习中使用模型数据来设置标签、重新训练、可解释性、数据分析或其他用途。 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>使用 Python SDK 进行配置 

### <a name="update-a-deployed-service"></a>更新已部署的服务

1. 在工作区中标识该服务。 `ws` 的值是工作区的名称

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. 更新服务并启用 Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>在服务中记录自定义跟踪

如果要记录自定义跟踪，请遵循[部署方式和部署位置](how-to-deploy-and-where.md)文档中适用于 AKS 或 ACI 的标准部署过程。 然后，使用以下步骤：

1. 通过添加 print 语句更新评分文件
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. 更新服务配置
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. 生成一个映像并将它部署到 [AKS 或 ACI](how-to-deploy-and-where.md) 上。

### <a name="disable-tracking-in-python"></a>在 Python 中禁用跟踪

若要禁用 Azure Application Insights，请使用以下代码：

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>使用 Azure 机器学习工作室配置

当您准备好使用这些步骤部署模型时，还可以从 Azure 机器学习工作室启用 Azure 应用程序见解。

1. 登录到工作区，https://ml.azure.com/
1. 转到 **"模型"** 并选择要部署的模型
1. 选择 **+部署**
1. 填充**部署模型**窗体
1. 展开 **"高级"** 菜单

    ![部署窗体](./media/how-to-enable-app-insights/deploy-form.png)
1. 选择**启用应用程序见解诊断和数据收集**

    ![启用应用见解](./media/how-to-enable-app-insights/enable-app-insights.png)
## <a name="evaluate-data"></a>评估数据
服务的数据将存储在 Azure Application Insights 帐户中，此帐户与 Azure 机器学习位于同一资源组。
查看数据：

1. 转到[Azure 门户](https://ms.portal.azure.com/)中的 Azure 机器学习工作区，然后单击应用程序见解链接

    [![应用洞察Loc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. 选择 **"概述"** 选项卡可查看服务的基本指标集

   [![概述](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. 若要深入查看你的 Web 服务请求元数据和响应，请选择“日志（分析）”**** 部分中的“请求”**** 表，并选择“运行”**** 来查看请求

   [![模型数据](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. 要查看自定义跟踪，请选择 **"分析"**
4. 在架构部分，选择“跟踪”。**** 然后选择“运行”以运行查询。**** 数据应以表格格式显示，并且应映射到评分文件中的自定义调用

   [![自定义跟踪](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

若要详细了解如何使用 Azure Application Insights，请参阅[什么是 Application Insights？](../azure-monitor/app/app-insights-overview.md)。

## <a name="export-data-for-further-processing-and-longer-retention"></a>导出数据以便进一步处理并保留更长时间

>[!Important]
> Azure Application Insights 仅支持导出到 Blob 存储。 [从 App Insights 中导出遥测数据](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration)列出了此导出功能的其他限制。

可以使用 Azure Application Insights 的[连续导出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)将消息发送到受支持的存储帐户，可以在其中设置更长的保留期。 `"model_data_collection"` 消息以 JSON 格式存储，可以轻松对其进行分析来提取模型数据。 

可以根据需要使用 Azure 数据工厂、Azure ML Pipelines 或其他数据处理工具来转换数据。 转换数据后，可以在 Azure 机器学习工作区中将其注册为数据集。 若要执行此操作，请参阅[如何创建和注册数据集](how-to-create-register-datasets.md)。

   [![连续出口](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>示例笔记本

[enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) 笔记本演示了本文所述的概念。 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

* 参阅[如何将模型部署到 Azure Kubernetes 服务群集](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service)或[如何将模型部署到 Azure 容器实例](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance)来将模型部署到 Web 服务终结点，并启用 Azure Application Insights 来利用数据收集和终结点监视
* 请参阅[MOPS：使用 Azure 机器学习管理、部署和监视模型](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment)，了解有关利用从生产中模型收集的数据详细信息。 此类数据有助于持续改进你的机器学习流程
