---
title: 使用 Application Insights 监视模型
titleSuffix: Azure Machine Learning service
description: 使用 Application Insights 监视通过 Azure 机器学习服务部署的 Web 服务
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 1/07/2019
ms.custom: seodec18
ms.openlocfilehash: 32dc55927f614a91c390a417595b7a00c16e9386
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57847943"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>使用 Application Insights 监视 Azure 机器学习模型

本文介绍如何设置适用于 Azure 机器学习服务的 Application Insights。 使用 Application Insights 可以监视：
* 请求速率、响应时间和失败率。
* 依赖项率、响应时间和失败率。
* 异常。

[详细了解 Application Insights](../../azure-monitor/app/app-insights-overview.md)。 


## <a name="prerequisites"></a>必备组件

* 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* 已安装 Azure 机器学习工作区、一个包含脚本的本地目录以及用于 Python 的 Azure 机器学习 SDK。 若要了解如何满足这些先决条件，请参阅[如何配置开发环境](how-to-configure-environment.md)。
* 要部署到 Azure Kubernetes 服务 (AKS) 或 Azure 容器实例 (ACI) 的经过训练的机器学习模型。 如果没有模型，请参阅[训练图像分类模型](tutorial-train-models-with-aml.md)教程。


## <a name="enable-and-disable-from-the-sdk"></a>从 SDK 启用和禁用

### <a name="update-a-deployed-service"></a>更新已部署的服务
1. 在工作区中标识该服务。 `ws` 的值是工作区的名称。

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. 更新服务并启用 Application Insights。 

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

要禁用 Application Insights，请使用以下代码：

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="enable-and-disable-in-the-portal"></a>在门户中启用和禁用

可以在 Azure 门户中启用和禁用 Application Insights。

1. 在 [Azure 门户](https://portal.azure.com)中打开你的工作区。

1. 在“部署”选项卡上，选择要启用 Application Insights 的服务。

   [![“部署”选项卡上的服务列表](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. 选择“编辑”。

   [![编辑按钮](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. 在“高级设置”中，选中“启用 AppInsights 诊断”复选框。

   [![已选中启用诊断的复选框](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. 选择屏幕底部的“更新”以应用更改。 

### <a name="disable"></a>禁用
1. 在 [Azure 门户](https://portal.azure.com)中打开你的工作区。
1. 依次选择“部署”、所需服务、“编辑”。

   [![使用编辑按钮](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. 在“高级设置”中，清除“启用 AppInsights 诊断”复选框。 

   [![已清除启用诊断的复选框](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. 选择屏幕底部的“更新”以应用更改。 
 

## <a name="evaluate-data"></a>评估数据
服务的数据将存储在 Application Insights 帐户中，此帐户与 Azure 机器学习服务位于同一资源组。
查看数据：
1. 转到 [Azure 门户](https://portal.azure.com)中的机器学习服务工作区，然后单击 Application Insights 链接。

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. 选择“概述”选项卡可查看服务的一组基本指标。

   [![概述](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. 若要深入查看自定义跟踪，请选择“分析”。
4. 在架构部分，选择“跟踪”。 然后选择“运行”以运行查询。 数据应以表格格式显示，并且应映射到评分文件中的自定义调用。 

   [![自定义追踪](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

若要详细了解 Application Insights，请参阅[什么是 Application Insights？](../../azure-monitor/app/app-insights-overview.md)。
    

## <a name="example-notebook"></a>示例笔记本

[How-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) 笔记本演示了本文中的概念。 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤
还可以在生产环境中收集模型上的数据。 阅读[在生产环境中收集模型数据](how-to-enable-data-collection.md)一文。 


## <a name="other-references"></a>其他参考资料
* [用于容器的 Azure Monitor](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json)
