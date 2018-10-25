---
title: 在生产中启用 Azure 机器学习服务的 Application Insights
description: 了解如何为 Azure Kubernetes 服务中部署的 Azure 机器学习服务设置 Application Insights
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 45871ab515c7ffd9520b1d77d3fd1e77abcc29ef
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114560"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>使用 Application Insights 在生产中监视 Azure 机器学习模型

本文介绍如何为 Azure 机器学习服务设置 Application Insights。 启用后，可利用 Application Insights 监视：
* 请求速率、响应时间和失败率
* 依赖项速率、响应时间和失败率
* 例外

从[此处](../../application-insights/app-insights-overview.md)详细了解 Application Insights。 

## <a name="prerequisites"></a>先决条件
* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 已安装 Azure 机器学习工作区、一个包含脚本的本地目录以及用于 Python 的 Azure 机器学习 SDK。 了解如何使用[如何配置开发环境](how-to-configure-environment.md)文档来获得这些先决条件。
* 要部署到 Azure Kubernetes 服务 (AKS) 的经过训练的机器学习模型。 如果没有模型，请参阅[训练图像分类模型](tutorial-train-models-with-aml.md)教程。
* [AKS 群集](how-to-deploy-to-aks.md)。

## <a name="enable--disable-in-the-portal"></a>在门户中启用和禁用

可以在 Azure 门户中启用和禁用 Application Insights。

### <a name="enable"></a>启用

1. 在 [Azure 门户](https://portal.azure.com)中打开工作区。

1. 转到部署并选择要启用 Application Insights 的服务。

   [![部署](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. 单击“编辑”并转到“高级设置”。

   [![编辑](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. 在“高级设置”中，选择“启用 Application Insights 诊断”。

   [![编辑](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. 选择屏幕底部的“更新”以应用更改。 

### <a name="disable"></a>禁用
要在 Azure 门户中禁用 Application Insights，请执行以下步骤：

1. 在 https://portal.azure.com 登录 Azure 门户。
1. 转到你的工作区。
1. 选择“部署”，然后依次选择“选择服务”、“编辑”。

   [![编辑](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. 在“高级设置”中，取消选择“启用 AppInsights 诊断”选项。 

   [![取消选中](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. 选择屏幕底部的“更新”以应用更改。 

## <a name="enable--disable-from-the-sdk"></a>从 SDK 启用和禁用

### <a name="update-a-deployed-service"></a>更新已部署的服务
1. 标识工作区中的服务（ws = 工作区的名称）

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. 更新服务并启用 Application Insights。 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>在服务中记录自定义跟踪
如果要记录自定义跟踪，请遵循[适用于 AKS 的标准部署过程](how-to-deploy-to-aks.md)并执行以下操作：

1. 通过添加 print 语句更新评分文件。
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. 更新 aks 配置。
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [生成映像并部署。](how-to-deploy-to-aks.md)  

### <a name="disable-tracking-in-python"></a>在 Python 中禁用跟踪

要禁用 Application Insights，请使用以下代码：

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>评估数据
服务数据存储在 Application Insights 帐户中，此帐户与 Azure 机器学习服务工作区位于同一资源组。
查看数据：
1. 转到[Azure 门户](https://portal.azure.com)中的资源组，然后单击进入 Application Insights 资源。 
2. “概述”选项卡显示服务的基本指标集。

   [![概述](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. 要深入了解自定义跟踪，请单击“分析”。
4. 在架构部分中，单击“跟踪”，然后“运行”查询。 数据应以下表格式显示，并且应映射到评分文件中的自定义调用。 

   [![自定义追踪](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

单击[此处](../../application-insights/app-insights-overview.md)了解有关如何使用 Application Insights 的详细信息。
    

## <a name="example-notebook"></a>示例笔记本

[00.Getting Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) 笔记本演示了本文所述的概念。  获取此笔记本：
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤
还可以在生产环境中收集模型上的数据。 请阅读文章[在生产环境中收集模型数据](how-to-enable-data-collection.md) 
