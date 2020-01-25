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
ms.date: 11/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 68c7f3082adbf10ee6f5b5e6b6fd0bf79232a7d0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722418"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>监视和收集 ML web 服务终结点中的数据
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何在 Azure Kubernetes Service （AKS）或 Azure 容器实例（ACI）中通过启用 Azure 应用程序 Insights 来收集和监视部署到 web 服务终结点的模型。 除了收集终结点的输入数据和响应外，还可以监视：

* 요청 속도, 응답 시간 및 실패율
* 종속성 비율, 응답 시간 및 실패율
* 예외

[详细了解 Azure 应用程序 Insights](../azure-monitor/app/app-insights-overview.md)。 


## <a name="prerequisites"></a>필수 조건

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* Azure Machine Learning 작업 영역, 스크립트가 포함된 로컬 디렉터리 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. 若要了解如何获取这些必备组件，请参阅[如何配置开发环境](how-to-configure-environment.md)
* AKS(Azure Kubernetes Service) 또는 ACI(Azure Container Instances)에 배포할 학습된 Machine Learning 모델. 如果没有，请参阅[训练图像分类模型](tutorial-train-models-with-aml.md)教程

## <a name="web-service-metadata-and-response-data"></a>Web 服务元数据和响应数据

>[!Important]
> Azure 应用程序 Insights 只记录最多64kb 的负载。 如果达到此限制，则只记录模型的最新输出。 

与 web 服务元数据和模型的预测对应的服务的元数据和响应记录到消息 `"model_data_collection"`下的 Azure 应用程序 Insights 跟踪。 可以直接查询 Azure 应用程序 Insights 以访问此数据，或设置[连续导出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)到存储帐户，以便进行更长的保留或进一步处理。 然后，可以在 Azure 机器学习中使用模型数据来设置标签、重新训练、explainability、数据分析或其他用途。 

## <a name="use-the-azure-portal-to-configure"></a>使用 Azure 门户配置

可以在 Azure 门户中启用和禁用 Azure 应用程序见解。 

1. 在[Azure 门户](https://portal.azure.com)中，打开工作区

1. 在 "**部署**" 选项卡上，选择要在其中启用 Azure 应用程序 Insights 的服务

   [![배포 탭의 서비스 목록](./media/how-to-enable-app-insights/Deployments.PNG)](././media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. 选择 "**编辑**"

   [![편집 단추](././media/how-to-enable-app-insights/Edit.PNG)](./././media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. 在 "**高级设置**" 中，选中 "**启用 AppInsights 诊断**" 复选框

   [![진단을 사용하도록 설정하기 위한 확인란을 선택함](./media/how-to-enable-app-insights/AdvancedSettings.png)](././media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. 选择屏幕底部的 "**更新**" 以应用更改

### <a name="disable"></a>사용 안 함

1. 在[Azure 门户](https://portal.azure.com)中，打开工作区
1. 选择 "**部署**"，选择服务，然后选择 "**编辑**"

   [![편집 단추 사용](././media/how-to-enable-app-insights/Edit.PNG)](./././media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. 在 "**高级设置**" 中，清除 "**启用 AppInsights 诊断**" 复选框

   [![진단을 사용하도록 설정하기 위한 확인란을 선택 취소함](./media/how-to-enable-app-insights/uncheck.png)](././media/how-to-enable-app-insights/uncheck.png#lightbox)

1. 选择屏幕底部的 "**更新**" 以应用更改
 
## <a name="use-python-sdk-to-configure"></a>使用 Python SDK 进行配置 

### <a name="update-a-deployed-service"></a>배포된 서비스 업데이트

1. 작업 영역에서 서비스를 식별합니다. `ws` 的值是你的工作区的名称

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. 更新服务并启用 Azure 应用程序 Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>서비스에서 사용자 지정 추적 로그

사용자 지정 추적을 기록하려는 경우 [배포 방법 및 위치](how-to-deploy-and-where.md) 문서에서 AKS 또는 ACI에 대한 표준 배포 프로세스를 수행합니다. 그런 후 다음 단계를 사용하세요.

1. 通过添加 print 语句更新计分文件
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. 更新服务配置
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. 生成一个映像并将其部署在[AKS 或 ACI](how-to-deploy-and-where.md)上。

### <a name="disable-tracking-in-python"></a>Python에서 추적을 사용하지 않도록 설정

若要禁用 Azure 应用程序 Insights，请使用以下代码：

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

## <a name="evaluate-data"></a>데이터 평가
你的服务数据存储在与 Azure 机器学习相同的资源组中的 Azure 应用程序 Insights 帐户中。
이 데이터를 보려면:

1. 在[Azure 机器学习 studio](https://ml.azure.com)中转到 Azure 机器学习工作区，然后单击 Application Insights 链接

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. 选择 "**概览**" 选项卡以查看服务的一组基本指标

   [![개요](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. 若要查看 web 服务请求元数据和响应，请在 "**日志（Analytics）** " 部分中选择 "**请求**" 表，并选择 "**运行**" 以查看请求

   [![模型数据](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. 若要查看自定义跟踪，请选择 "**分析**"
4. 스키마 섹션에서 **추적**을 선택합니다. 그런 후 **실행**을 선택하여 쿼리를 실행합니다. 数据应以表格格式显示，并应映射到计分文件中的自定义调用

   [![사용자 지정 추적](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

若要了解有关如何使用 Azure 应用程序 Insights 的详细信息，请参阅[什么是 Application Insights？](../azure-monitor/app/app-insights-overview.md)。

## <a name="export-data-for-further-processing-and-longer-retention"></a>导出数据以进行进一步的处理和更长的保留期

>[!Important]
> Azure 应用程序 Insights 仅支持导出到 blob 存储。 此导出功能的其他限制在[从 App Insights 导出遥测](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration)中列出。

可以使用 Azure 应用程序 Insights 的[连续导出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)将消息发送到受支持的存储帐户，在该帐户中可以设置更长的保留期。 `"model_data_collection"` 消息以 JSON 格式存储，并且可以轻松地进行分析以提取模型数据。 

可以根据需要使用 azure 数据工厂、Azure ML 管道或其他数据处理工具来转换数据。 转换数据后，可以将其作为数据集注册到 Azure 机器学习工作区。 为此，请参阅[如何创建和注册数据集](how-to-create-register-datasets.md)。

   [![连续导出](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>예제 Notebook

[Ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb)笔记本展示了这篇文章中的概念的概念。 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* 了解[如何将模型部署到 Azure Kubernetes Service 群集](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service)，或者[如何将模型部署到 azure 容器实例](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance)，以将模型部署到 web 服务终结点，并启用 Azure 应用程序 Insights 来利用数据收集和终结点监视
* 请参阅[MLOps： Azure 机器学习的管理、部署和监视模型](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment)，详细了解如何利用生产中的模型收集的数据。 此类数据可帮助不断改进机器学习过程
