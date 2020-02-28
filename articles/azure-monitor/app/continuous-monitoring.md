---
title: 利用 Azure Pipelines 和 Azure 应用程序 Insights 持续监视 DevOps release 管道 |Microsoft Docs
description: 提供有关快速设置持续监视 Application Insights
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: e565101218b975ef2bd29b8a32a4aa1bf4300b6d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655389"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>向发布管道添加连续监视

Azure Pipelines 与 Azure 应用程序 Insights 集成，以允许在整个软件开发生命周期内连续监视 DevOps release 管道。 

通过连续监视，release 管道可以合并来自 Application Insights 和其他 Azure 资源的监视数据。 当发布管道检测到 Application Insights 警报时，管道可以发出或回滚部署，直到警报解决。 如果所有检查都通过，则部署可以从测试中自动继续进行，而无需手动干预。 

## <a name="configure-continuous-monitoring"></a>配置连续监视

1. 在[Azure DevOps](https://dev.azure.com)中，选择组织和项目。
   
1. 在 "项目" 页的左侧菜单中，选择 "**管道**" > **版本**。 
   
1. 下拉到 "**新建**" 旁边的箭头，然后选择 "**新发布管道**"。 或者，如果还没有管道，请在显示的页面上选择 "**新建管道**"。
   
1. 在 "**选择模板**" 窗格上，搜索并选择 "**持续监视 Azure App Service 部署**"，然后选择 "**应用**"。 

   ![新 Azure Pipelines 发布管道](media/continuous-monitoring/001.png)

1. 在 "**阶段 1** " 框中，选择超链接以**查看阶段任务。**

   ![查看阶段任务](media/continuous-monitoring/002.png)

1. 在 "**阶段 1** " 配置窗格中，填写以下字段： 

    | 参数        | 值 |
   | ------------- |:-----|
   | **阶段名称**      | 提供阶段名称，或将其保留在第**1 阶段**。 |
   | **Azure 订阅** | 下拉，并选择要使用的链接的 Azure 订阅。|
   | **应用类型** | 下拉，并选择应用类型。 |
   | **应用服务名称** | 输入 Azure App Service 的名称。 |
   | **Application Insights 的资源组名称**    | 下拉，然后选择要使用的资源组。 |
   | **Application Insights 资源名称** | 下拉，并为所选资源组选择 "Application Insights 资源"。

1. 若要用默认的警报规则设置保存管道，请在 "Azure DevOps" 窗口的右上角选择 "**保存**"。 输入描述性注释，然后选择 **"确定"** 。

## <a name="modify-alert-rules"></a>修改警报规则

现成，**具有持续监视模板的 Azure App Service 部署**具有四个警报规则：**可用性**、**失败请求**、**服务器响应时间**和**服务器异常**。 你可以添加更多规则，或更改规则设置以满足你的服务级别需求。 

修改警报规则设置：

1. 在 "发布管道" 页的左窗格中，选择 "**配置 Application Insights 警报**"。

1. 在 " **Azure Monitor 警报**" 窗格中，选择 "**警报规则** **" 旁边的**省略号 "..."。
   
1. 在 "**警报规则**" 对话框中，选择警报规则旁边的下拉符号，如 "**可用性**"。 
   
1. 修改**阈值**和其他设置，以满足你的要求。
   
   ![修改警报](media/continuous-monitoring/003.png)
   
1. 选择 **"确定"** ，然后在 "Azure DevOps" 窗口的右上方选择 "**保存**"。 输入描述性注释，然后选择 **"确定"** 。

## <a name="add-deployment-conditions"></a>添加部署条件

将部署入口添加到发布管道时，超过设置阈值的警报会阻止不需要的发布升级。 解决警报后，就可以自动执行部署了。

添加部署入口：

1. 在 "主管道" 页的 "**阶段**" 下，选择 "**预先部署条件**" 或 "**后期部署条件**" 符号，具体取决于哪个阶段需要持续监视入口。
   
   ![预先部署条件](media/continuous-monitoring/004.png)
   
1. 在 "**预先部署条件**" 配置窗格中，将 "**入口**" 设置为 "**已启用**"。
   
1. 在 "**部署入口**" 旁边，选择 "**添加**"。
   
1. 从下拉菜单中选择 "**查询 Azure Monitor 警报**"。 使用此选项可以同时访问 Azure Monitor 和 Application Insights 警报。
   
   ![查询 Azure Monitor 警报](media/continuous-monitoring/005.png)
   
1. 在 "**评估选项**" 下，输入所需的值，例如**重新评估入口的间隔时间**和**入口失败后的超时**时间。 

## <a name="view-release-logs"></a>查看版本日志

可以在发布日志中查看部署入口行为和其他发布步骤。 若要打开日志：

1. 从 "管道" 页的左侧菜单中选择 "**发布**"。 
   
1. 选择任何版本。 
   
1. 在 "**阶段**" 下，选择 "任何阶段" 查看 "发布摘要"。 
   
1. 若要查看日志，请在 "发布摘要" 中选择 "**查看日志**"，在任何阶段中选择 "**成功**" 或 "**失败**" 超链接，或将鼠标悬停在任何阶段，**然后选择** 
   
   ![查看版本日志](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>后续步骤

有关 Azure Pipelines 的详细信息，请参阅[Azure Pipelines 文档](https://docs.microsoft.com/azure/devops/pipelines)。
