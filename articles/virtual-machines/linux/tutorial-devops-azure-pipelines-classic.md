---
title: 教程 - Azure 上用于 IaaS 和 PaaS 的集成 DevOps
description: 本教程介绍如何使用 Azure Pipelines 设置目标为 Azure VM 的应用持续集成 (CI) 和持续部署 (CD)。
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 4159bf27c39087926d982552c49c606f3484de77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885825"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>教程：Azure 上用于 IaaS 和 PaaS 的集成 DevOps

借助 Azure 上的端到端解决方案，团队可以将 DevOps 做法实施到每个应用程序生命周期阶段：规划、开发、交付和运营。 

下面是一些可简化云工作负载的 Azure 服务，可以将其结合使用以实现功能极为强大的方案。
这些技术与人员和流程相结合，使团队能够不断为客户提供价值。 

- Azure： https://portal.azure.com - 用于生成云工作负载的门户。 管理和监视一切内容，从简单的 Web 应用到复杂的云应用程序，无所不包。 
- Azure DevOps： https://dev.azure.com - 规划方式更智能，协作方式更好，并使用一组新式开发服务来加快交付速度 
- Azure 机器学习工作室： https://ml.azure.com - 准备数据，以及训练并部署机器学习模型 
 

Azure DevOps 是一项内置的 Azure 服务，可以通过适用于任何 Azure 资源的持续集成和持续交付来自动执行 DevOps 过程的每个部分。
不管应用使用的是虚拟机、Web 应用、Kubernetes 还是任何其他资源，都可以通过 Azure 和 Azure DevOps 实现基础结构即代码、持续集成、持续测试、持续交付以及持续监视。  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - 配置 CI/CD 
Azure Pipelines 提供了一组完整且功能完备的 CI/CD 自动化工具，用于将内容部署到虚拟机。 可以直接通过 Azure 门户为 Azure VM 配置持续交付管道。 本文档包含的步骤涉及如何通过 Azure 门户设置用于多计算机部署的 CI/CD 管道。 


**在虚拟机上配置 CI/CD**

虚拟机可作为目标添加到[部署组](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)，并可作为多计算机更新的目标。 根据你的需要，你可以选择任一现成部署策略：“滚动”、“Canary”、“蓝绿”部署，或者进一步自定义这些策略。    部署后，部署组中的部署历史记录视图将提供从 VM 到管道再到提交的可追溯性。 
 
**滚动部署**：进行滚动部署时，每次迭代都会将旧版应用程序的实例替换为固定的计算机集（滚动集）中新版应用程序的实例。 让我们演练一下如何配置虚拟机的滚动更新。  
可以使用持续交付选项在 Azure 门户中配置对“虚拟机”的滚动更新。  

下面是分步演练。 
1. 登录到 Azure 门户，导航到虚拟机。 
2. 在 VM 左窗格中，导航到“ **持续交付**”菜单。  然后，单击“ **配置**”。 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 在配置面板中单击“Azure DevOps 组织”，选择现有帐户或创建一个帐户。 然后，选择要在其下配置管道的项目。  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. 部署组是代表物理环境的部署目标计算机的逻辑集，例如“开发”、“测试”、“UAT”和“生产”。 可以创建新的部署组，也可以选择现有的部署组。 
5. 选择生成管道，用于发布要部署到虚拟机的包。 请注意，发布的包应在其根目录下的 deployscripts 文件夹中包含部署脚本 deploy.ps1 或 deploy.sh。    在运行时，此部署脚本将由 Azure DevOps 管道执行。
6. 选择所需的部署策略。 在本例中，让我们选择“滚动”。
7. （可选）可以使用角色来标记计算机。 例如，“web”、“db”等。这有助于将目标限定为具有特定角色的 VM。
8. 单击对话框中的“确定”  ，配置持续交付管道。 
9. 完成后，你将拥有一个已配置为部署到虚拟机的持续交付管道。  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. 你会看到目标为虚拟机的部署正在进行中。 单击链接即可导航到该管道。 单击“Release-1”  即可查看部署。 也可单击“编辑”  ，修改发布管道定义。 
11. 如果你有多个要配置的 VM，请重复步骤 2-4，以将其他 VM 添加到部署组。 请注意，如果选择一个已存在管道运行的部署组，则只会将 VM 添加到该部署组，而不会创建任何新管道。 
12. 完成后，单击管道定义，导航到 Azure DevOps 组织，并单击“编辑”  以编辑发布管道。 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. 单击“开发”阶段中的“1 个作业、1 个任务”链接。   单击“部署”  阶段。
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. 在右侧的配置窗格中，可以指定要在每次迭代中并行部署的计算机数。 如果你希望一次部署到多个计算机，可以使用滑块按百分比指定计算机数。  

15. 默认情况下，“执行部署脚本”任务将执行发布的包的根目录下的 deployscripts 文件夹中的部署脚本 deploy.ps1 或 deploy.sh。   
  
**Canary 部署**：Canary 部署通过慢慢地向一小部分用户推出更改来降低风险。 随着自己对新版本的信心越来越大，你可以开始将其发布到基础结构中的更多服务器，并将更多用户路由到该版本。 可以使用持续交付选项通过 Azure 门户配置目标为“虚拟机”的 Canary 部署。  下面是分步演练。 
1. 登录到 Azure 门户，导航到虚拟机 
2. 遵循“滚动部署”部分中的步骤 2-7，将多个 VM 添加到部署组。  在部署策略下拉列表中选择“Canary”。
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

3. 将“canary”标记添加到属于 Canary 部署一部分的 VM，将“prod”标记添加到 Canary 部署成功后属于部署一部分的 VM。
4. 完成后，你将拥有一个已配置为部署到虚拟机的持续交付管道。
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


5. 与在“滚动部署”部分中一样，可以单击 Azure DevOps 中发布管道对应的“编辑”来查看该管道的配置。   该管道包括 3 个阶段 - 第一阶段是 DG 阶段，并部署到标记为“canary”的 VM。  第二阶段暂停管道，并等待通过手动干预来恢复运行。 用户对 Canary 部署的稳定性感到满意后，可以恢复管道运行，然后运行第三阶段，即，部署到标记为“prod”的 VM。  ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)



**蓝绿部署**：蓝绿部署通过使用具有相同配置的备用环境来减少停机时间。 在任何时候，环境之一都处于活动状态。 为新版本做准备时，请在绿色环境中完成最后阶段的测试。 如果软件在绿色环境中正常运行，请切换流量，使所有传入请求进入绿色环境 - 蓝色环境现在处于空闲状态。
可以使用持续交付选项通过 Azure 门户配置目标为“虚拟机”的蓝绿部署。  

下面是分步演练。

1. 登录到 Azure 门户，导航到虚拟机 
2. 遵循“滚动部署”部分中的步骤 2-7，将多个 VM 添加到部署组。  将“blue”或“green”标记添加到属于蓝绿部署一部分的 VM。 如果 VM 充当备用角色，则应将其标记为“green”。
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

4. 完成后，你将拥有一个已配置为部署到虚拟机的持续交付管道。
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

5. 与在“滚动部署”部分中一样，可以单击 Azure DevOps 中发布管道对应的“编辑”来查看该管道的配置。   该管道包括 3 个阶段 - 第一阶段是 DG 阶段，部署到标记为“green”的 VM（备用 VM）。  第二阶段暂停管道，并等待通过手动干预来恢复运行。 用户对部署稳定性感到满意后，可将流量重定向到 green VM，并恢复管道运行，然后交换 VM 中的 blue 和 green 标记。    这可以确保将采用旧应用程序版本的 VM 标记为 green，并在下一次管道运行中部署到这些 VM。 
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

6. 请注意，此部署策略要求至少有一个标记为 blue 的 VM 和至少一个标记为 green 的 VM。 在手动干预步骤中恢复管道运行之前，请确保至少有一个 VM 标记为 blue。 





 
## <a name="azure-devops-project"></a>Azure DevOps 项目 
Azure 入门比以往更轻松。
 
使用 DevOps Projects，只需下述三个步骤即可在任何 Azure 服务上运行应用程序：选择应用程序语言、选择运行时，以及选择 Azure 服务。
 
[了解详细信息](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>其他资源 
- [使用 DevOps Projects 将应用部署到 Azure 虚拟机](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implement continuous deployment of your app to an Azure Virtual Machine Scale Set](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)（将应用持续部署到 Azure 虚拟机规模集）
