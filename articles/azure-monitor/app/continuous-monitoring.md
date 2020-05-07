---
title: 使用 Azure Pipelines 和 Azure Application Insights 连续监视 DevOps 发布管道 | Microsoft Docs
description: 提供使用 Application Insights 快速设置连续监视的说明
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 0d47fb1eccdfcfc7b2719825575f06dc85e62452
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652754"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>向发布管道添加连续监视

Azure Pipelines 与 Azure Application Insights 集成，可连续监视整个软件开发生命周期内的 DevOps 发布管道。 

借助持续监视，发布管道可以结合来自 Application Insights 和其他 Azure 资源的监视数据。 当发布管道检测到 Application Insights 警报时，该管道可以限制或回滚部署，直到警报已解决。 如果所有检查都通过，可以从测试到生产全程自动执行部署，无需手动干预。 

## <a name="configure-continuous-monitoring"></a>配置连续监视

1. 在 [Azure DevOps](https://dev.azure.com) 中选择一个组织和项目。
   
1. 在项目页的左菜单中，选择“管道” > “发布”。   
   
1. 单击“新建”旁边的下拉箭头，然后选择“新建发布管道”。   如果你没有管道，请在显示的页面上选择“新建管道”。 
   
1. 在“选择模板”窗格中，搜索并选择“支持持续监视的 Azure 应用服务部署”，然后选择“应用”。    

   ![新建 Azure Pipelines 发布管道](media/continuous-monitoring/001.png)

1. 在“阶段 1”框中，选择“查看阶段任务”超链接。  

   ![查看阶段任务](media/continuous-monitoring/002.png)

1. 在“阶段 1”配置窗格中填写以下字段：  

    | 参数        | Value |
   | ------------- |:-----|
   | **阶段名称**      | 提供阶段名称，或将其保留为“阶段 1”。  |
   | **Azure 订阅** | 单击下拉箭头并选择要使用的 Azure 链接订阅。|
   | **应用类型** | 单击下拉箭头并选择应用类型。 |
   | **应用服务名称** | 输入 Azure 应用服务的名称。 |
   | **Application Insights 的资源组名称**    | 单击下拉箭头并选择要使用的资源组。 |
   | **Application Insights 资源名称** | 单击下拉箭头并选择所选资源组的 Application Insights 资源。

1. 若要使用默认的警报规则设置保存管道，请在“Azure DevOps”窗口的右上角选择“保存”。  输入描述性的注释，然后选择“确定”。 

## <a name="modify-alert-rules"></a>修改警报规则

现成的“支持持续监视的 Azure 应用服务部署”模板包含四个警报规则：  “可用性”、“失败的请求”、“服务器响应时间”和“服务器异常”。     可以添加更多规则，或根据服务级别需求更改规则设置。 

若要修改警报规则设置：

在发布管道页的左窗格中，选择“配置 Application Insights 警报”。 

四个默认警报规则是通过内联脚本创建的：

```bash
$subscription = az account show --query "id";$subscription.Trim("`"");$resource="/subscriptions/$subscription/resourcegroups/"+"$(Parameters.AppInsightsResourceGroupName)"+"/providers/microsoft.insights/components/" + "$(Parameters.ApplicationInsightsResourceName)";
az monitor metrics alert create -n 'Availability_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg availabilityResults/availabilityPercentage < 99' --description "created from Azure DevOps";
az monitor metrics alert create -n 'FailedRequests_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count requests/failed > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerResponseTime_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg requests/duration > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerExceptions_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count exceptions/server > 5' --description "created from Azure DevOps";
```

您可以修改脚本并添加其他警报规则、修改警报条件或删除不适合您的部署的警报规则。

## <a name="add-deployment-conditions"></a>添加部署条件

将部署门限添加到发布管道时，会出现一条指出超过了设置的阈值的警报，此警报旨在防止不需要的发布升级。 解决该警报后，部署可自动继续。

若要添加部署门限：

1. 在管道主页上的“阶段”下，根据哪个阶段需要持续监视门限，选择“部署前的条件”或“部署后的条件”符号。************
   
   ![部署前的条件](media/continuous-monitoring/004.png)
   
1. 在“部署前的条件”配置窗格中，将“门限”设置为“已启用”。************
   
1. 在“部署门限”的旁边，选择“添加”。********
   
1. 从下拉菜单中选择“查询 Azure Monitor 警报”。**** 使用此选项可以访问 Azure Monitor 和 Application Insights 警报。
   
   ![查询 Azure Monitor 警报](media/continuous-monitoring/005.png)
   
1. 在“评估选项”下，输入要对“重新评估门限的间隔时间”和“门限失败之前所要经过的超时”等设置使用的值。************ 

## <a name="view-release-logs"></a>查看发布日志

可以在发布日志中查看部署门限行为和其他发布步骤。 若要打开日志：

1. 从管道页的左菜单中选择“发布”。**** 
   
1. 选择任一发布活动。 
   
1. 在“阶段”下，选择任一阶段以查看发布摘要。**** 
   
1. 若要查看日志，请在发布摘要中选择“查看日志”，然后在任一阶段中选择“成功”或“失败”超链接，或者将鼠标悬停在任一阶段上并选择“日志”。**************** 
   
   ![查看发布日志](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>后续步骤

有关 Azure Pipelines 的详细信息，请参阅 [Azure Pipelines 文档](https://docs.microsoft.com/azure/devops/pipelines)。
