---
title: 教程 - 为 Azure Linux 虚拟机配置滚动部署
description: 本教程将介绍如何设置持续部署 (CD) 管道，该管道使用滚动部署策略来增量更新一组 Azure Linux 虚拟机
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113471"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>教程 - 为 Azure Linux 虚拟机配置滚动部署策略

Azure DevOps 是一项内置的 Azure 服务，可以通过适用于任何 Azure 资源的持续集成和持续交付来自动执行 DevOps 过程的每个部分。
不管应用使用的是虚拟机、Web 应用、Kubernetes 还是任何其他资源，都可以通过 Azure 和 Azure DevOps 实现基础结构即代码、持续集成、持续测试、持续交付以及持续监视。  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS - 配置 CI/CD 
Azure Pipelines 提供了一组完整且功能完备的 CI/CD 自动化工具，用于将内容部署到虚拟机。 可以直接通过 Azure 门户为 Azure VM 配置持续交付管道。 本文档包含的步骤涉及如何通过 Azure 门户设置用于多计算机滚动部署的 CI/CD 管道。 还可以查看 Azure 门户直接支持的其他策略（例如 [Canary](https://aka.ms/AA7jdrz) 策略和[蓝绿](https://aka.ms/AA83fwu)策略）。 


**在虚拟机上配置 CI/CD**

虚拟机可作为目标添加到[部署组](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)，并可作为多计算机更新的目标。 部署后，即可通过部署组中的“部署历史记录”  进行跟踪，从 VM 追溯到管道再追溯到提交。 
 

**滚动部署**：进行滚动部署时，每次迭代都会将旧版应用程序的实例替换为固定的计算机集（滚动集）中新版应用程序的实例。 让我们演练一下如何配置虚拟机的滚动更新。  
可以使用持续交付选项在 Azure 门户中配置对“虚拟机”的滚动更新。  

下面是分步演练。 
1. 登录到 Azure 门户，导航到虚拟机。 
2. 在 VM 左窗格中，导航到“ **持续交付**”菜单。  然后，单击“ **配置**”。 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 在配置面板中单击“Azure DevOps 组织”，选择现有帐户或创建一个帐户。 然后，选择要在其下配置管道的项目。  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. 部署组是代表物理环境的部署目标计算机的逻辑集，例如“开发”、“测试”、“UAT”和“生产”。 可以创建新的部署组，也可以选择现有的部署组。 
5. 选择生成管道，该管道用于发布要部署到虚拟机的包。 请注意，发布的包应在其根目录下的 `deployscripts` 文件夹中包含部署脚本 deploy.ps1 或 deploy.sh。   此部署脚本将在运行时由 Azure DevOps 管道运行。
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

15. 默认情况下，“执行部署脚本”任务将执行部署脚本 deploy.ps1 或 deploy.sh，该脚本位于已发布包的根目录下的“deployscripts”文件夹中。    
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>其他部署策略

- [配置 Canary 部署策略](https://aka.ms/AA7jdrz)
- [配置蓝绿部署策略](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Azure DevOps 项目 
Azure 入门比以往更轻松。
 
使用 DevOps Projects，只需下述三个步骤即可在任何 Azure 服务上运行应用程序：选择应用程序语言、选择运行时，以及选择 Azure 服务。
 
[了解详细信息](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>其他资源 
- [使用 DevOps Projects 将应用部署到 Azure 虚拟机](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implement continuous deployment of your app to an Azure Virtual Machine Scale Set](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)（将应用持续部署到 Azure 虚拟机规模集）
