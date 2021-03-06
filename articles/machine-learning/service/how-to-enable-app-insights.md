---
title: 监视和收集来自机器学习 web 服务终结点的数据
titleSuffix: Azure Machine Learning
description: 使用 Azure 应用程序 Insights 监视使用 Azure 机器学习部署的 web 服务
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: lostmygithubaccount
ms.date: 10/11/2019
ms.custom: seoapril2019
ms.openlocfilehash: c16b6d769aa191b0e8ac86768a7eafd35ccbc3b9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301033"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>监视和收集 ML web 服务终结点中的数据

本文介绍如何在 Azure Kubernetes Service （AKS）或 Azure 容器实例（ACI）中通过启用 Azure 应用程序 Insights 来收集和监视部署到 web 服务终结点的模型。 除了收集终结点的输入数据和响应外，还可以监视：
* 请求速率、响应时间和失败率。
* 依赖项率、响应时间和失败率。
* 异常。

[详细了解 Azure 应用程序 Insights](../../azure-monitor/app/app-insights-overview.md)。 


## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* 已安装 Azure 机器学习工作区、一个包含脚本的本地目录以及用于 Python 的 Azure 机器学习 SDK。 若要了解如何满足这些先决条件，请参阅[如何配置开发环境](how-to-configure-environment.md)。
* 要部署到 Azure Kubernetes 服务 (AKS) 或 Azure 容器实例 (ACI) 的经过训练的机器学习模型。 如果没有模型，请参阅[训练图像分类模型](tutorial-train-models-with-aml.md)教程。

## <a name="web-service-input-and-response-data"></a>Web 服务输入和响应数据

与 ML 模型的输入和其预测对应的服务的输入和响应将记录到消息 `"model_data_collection"` 下的 Azure 应用程序 Insights 跟踪。 可以直接查询 Azure 应用程序 Insights 以访问此数据，或设置[连续导出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)到存储帐户，以便进行更长的保留或进一步处理。 然后，可以在 Azure ML 服务中使用模型数据来设置标签、重新训练、explainability、数据分析或其他用途。 

## <a name="use-the-azure-portal-to-configure"></a>使用 Azure 门户配置

可以在 Azure 门户中启用和禁用 Azure 应用程序见解。 

1. 在 [Azure 门户](https://portal.azure.com)中打开你的工作区。

1. 在 "**部署**" 选项卡上，选择要在其中启用 Azure 应用程序 Insights 的服务。

   [![“部署”选项卡上的服务列表](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. 选择“编辑”。

   [![编辑按钮](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. 在“高级设置”中，选中“启用 AppInsights 诊断”复选框。

   [![已选中启用诊断的复选框](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. 选择屏幕底部的“更新”以应用更改。 

### <a name="disable"></a>禁用
1. 在 [Azure 门户](https://portal.azure.com)中打开你的工作区。
1. 选择 "**部署**"，选择服务，然后选择 "**编辑**"。

   [![使用编辑按钮](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. 在“高级设置”中，清除“启用 AppInsights 诊断”复选框。 

   [![已清除启用诊断的复选框](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. 选择屏幕底部的“更新”以应用更改。 
 
## <a name="use-python-sdk-to-configure"></a>使用 Python SDK 进行配置 

### <a name="update-a-deployed-service"></a>更新已部署的服务
1. 在工作区中标识该服务。 `ws` 的值是工作区的名称。

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. 更新服务并启用 Azure 应用程序 Insights。 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>在服务中记录自定义跟踪
如果要记录自定义跟踪，请遵循[部署方式和部署位置](how-to-deploy-and-where.md)文档中适用于 AKS 或 ACI 的标准部署过程。 然后，使用以下步骤：

1. 通过添加 print 语句更新评分文件。
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. 更新服务配置。
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. 生成一个映像并将它部署到 [AKS](how-to-deploy-to-aks.md) 或 [ACI](how-to-deploy-to-aci.md) 上。  

### <a name="disable-tracking-in-python"></a>在 Python 中禁用跟踪

若要禁用 Azure 应用程序 Insights，请使用以下代码：

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="evaluate-data"></a>评估数据
你的服务数据存储在与 Azure 机器学习相同的资源组中的 Azure 应用程序 Insights 帐户中。
查看数据：
1. 在[Azure 门户](https://portal.azure.com)中转到机器学习服务工作区。 单击 "Azure 应用程序 Insights" 链接。

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. 选择“概述”选项卡可查看服务的一组基本指标。

   [![概述](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. 若要查看 web 服务输入和响应负载，请选择 "**分析**"
1. 在 "架构" 部分中，选择 "**跟踪**" 并筛选出消息 `"model_data_collection"` 的跟踪。 在自定义维度中，可以看到输入、预测和其他相关详细信息。

   [![Model 数据](media/how-to-enable-app-insights/model-data-trace.png)](./media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. 若要深入查看自定义跟踪，请选择“分析”。
4. 在架构部分，选择“跟踪”。 然后选择“运行”以运行查询。 数据应以表格格式显示，并且应映射到评分文件中的自定义调用。 

   [![自定义追踪](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

若要了解有关如何使用 Azure 应用程序 Insights 的详细信息，请参阅[什么是 Application Insights？](../../azure-monitor/app/app-insights-overview.md)。

## <a name="export-data-for-further-processing-and-longer-retention"></a>导出数据以进行进一步的处理和更长的保留期

可以使用 Azure 应用程序 Insights 的[连续导出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)将消息发送到受支持的存储帐户，在该帐户中可以设置更长的保留期。 @No__t-0 消息以 JSON 格式存储，可以轻松地进行分析以提取模型数据。 可以根据需要使用 azure 数据工厂、Azure ML 管道或其他数据处理工具来转换数据。 转换数据后，可以将其作为数据集注册到 Azure 机器学习服务工作区。

   [![Continuous 导出](media/how-to-enable-app-insights/continuous-export-setup.png)](./media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>示例笔记本

[Ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb)笔记本展示了这篇文章中的概念的概念。 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

* 了解[如何将模型部署到 Azure Kubernetes Service 群集](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-kubernetes-service)，或者[如何将模型部署到 azure 容器实例](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-container-instance)，以将模型部署到 web 服务终结点，并启用 Azure 应用程序 Insights 来利用数据收集和终结点监视.
* 请参阅 [MLOps：Azure 机器学习 @ no__t 的管理、部署和监视模型，以详细了解如何利用生产中的模型收集的数据。 此类数据可帮助不断改进机器学习过程。 
