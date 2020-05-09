---
title: 教程 - 为 Azure Linux 虚拟机配置 Canary 部署
description: 本教程介绍如何设置持续部署 (CD) 管道，用以使用蓝绿部署策略来更新一组 Azure 虚拟机
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
ms.openlocfilehash: b1a57245434bb188ffaab56a8891b4b0ee27f044
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120545"
---
# <a name="tutorial---configure-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>教程 - 为 Azure Linux 虚拟机配置蓝绿部署策略


## <a name="iaas---configure-cicd"></a>IaaS - 配置 CI/CD 
Azure Pipelines 提供了一组完整且功能完备的 CI/CD 自动化工具，用于将内容部署到虚拟机。 可以直接通过 Azure 门户为 Azure VM 配置持续交付管道。 本文档中的步骤介绍了如何设置 CI/CD 管道来将蓝绿策略用于多计算机部署。 还可以查看 Azure 门户直接支持的其他策略（例如[滚动](https://aka.ms/AA7jlh8)策略和 [Canary](https://aka.ms/AA7jdrz) 策略）。 

 
 **在虚拟机上配置 CI/CD**

虚拟机可作为目标添加到[部署组](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)，并可作为多计算机更新的目标。 部署后，在部署组中查看“部署历史记录”  即可进行跟踪，从 VM 追溯到管道再追溯到提交。 
 
  
**蓝绿部署**：蓝绿部署通过使用具有相同配置的备用环境来减少停机时间。 在任何时候，环境之一都处于活动状态。 为新版本做准备时，请在绿色环境中完成最后的测试阶段。 如果软件在绿色环境中正常运行，请切换流量，使所有传入请求进入绿色环境 - 蓝色环境现在处于空闲状态。
可以使用持续交付选项通过 Azure 门户配置目标为“虚拟机”的蓝绿部署。  

下面是分步演练。

1. 登录到 Azure 门户，导航到虚拟机 
2. 在 VM 左窗格中，转到“持续交付”。  然后，单击 **“配置”** 。 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 在配置面板中单击“Azure DevOps 组织”，选择现有帐户或创建一个帐户。  然后，选择要在其下配置管道的项目。  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. 部署组是代表物理环境的部署目标计算机的逻辑集，例如“开发”、“测试”、“UAT”和“生产”。 可以创建新的部署组，也可以选择现有的部署组。 
5. 选择生成管道，该管道用于发布要部署到虚拟机的包。 请注意，发布的包在其根目录下的 `deployscripts` 文件夹中应包含部署脚本 deploy.ps1 或 deploy.sh。   在运行时，此部署脚本将由 Azure DevOps 管道执行。
6. 选择所需的部署策略。 选择“蓝绿”。 
7. 将“blue”或“green”标记添加到属于蓝绿部署一部分的 VM。 如果 VM 充当备用角色，则应将其标记为“green”，否则将其标记为“blue”。
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. 单击“确定”  以配置持续交付管道。 现在，你将拥有一个配置为向虚拟机进行部署的持续交付管道。
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. 单击 Azure DevOps 中的发布管道对应的“编辑”，查看管道配置。  此管道包含三个阶段。 第一阶段是部署组阶段，部署到标记为“green”的 VM（备用 VM）。  第二阶段暂停管道，并等待通过手动干预来恢复运行。 用户对部署稳定性感到满意后，就可以将流量重定向到“green”VM，在恢复管道运行后交换 VM 中的“blue”和“green”标记。    这可以确保将采用旧应用程序版本的 VM 标记为 green，并在下一次管道运行中部署到这些 VM。 
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. 默认情况下，“执行部署脚本”任务将执行部署脚本 deploy.ps1 或 deploy.sh，该脚本位于已发布包的根目录下的 `deployscripts` 文件夹中。   请确保所选生成管道将其发布到包的根文件夹中。
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>其他部署策略
- [配置滚动部署策略](https://aka.ms/AA7jlh8)
- [配置 Canary 部署策略](https://aka.ms/AA7jdrz)

## <a name="azure-devops-project"></a>Azure DevOps 项目 
Azure 入门比以往更轻松。
 
使用 DevOps Projects，只需下述三个步骤即可在任何 Azure 服务上运行应用程序：选择应用程序语言、选择运行时，以及选择 Azure 服务。
 
[了解详细信息](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>其他资源 
- [使用 DevOps Projects 将应用部署到 Azure 虚拟机](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implement continuous deployment of your app to an Azure Virtual Machine Scale Set](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)（将应用持续部署到 Azure 虚拟机规模集）
