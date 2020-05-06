---
title: 教程 - 为 Azure Linux 虚拟机配置 Canary 部署
description: 本教程介绍如何设置持续部署 (CD) 管道，用以使用 Canary 部署策略来更新一组 Azure Linux 虚拟机
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
ms.openlocfilehash: b51b4aed85f737e436565ce8ba1ab4a295714734
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120525"
---
# <a name="tutorial---configure-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>教程 - 为 Azure Linux 虚拟机配置 Canary 部署策略


## <a name="iaas---configure-cicd"></a>IaaS - 配置 CI/CD 
Azure Pipelines 提供了一组完整且功能完备的 CI/CD 自动化工具，用于将内容部署到虚拟机。 可以直接通过 Azure 门户为 Azure VM 配置持续交付管道。 本文档中的步骤介绍了如何设置 CI/CD 管道来将 Canary 策略用于多计算机部署。 还可以查看 Azure 门户直接支持的其他策略（例如[滚动](https://aka.ms/AA7jlh8)策略和[蓝绿](https://aka.ms/AA83fwu)策略）。 


**在虚拟机上配置 CI/CD**

虚拟机可作为目标添加到[部署组](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)，并可作为多计算机更新的目标。 部署后，可通过部署组中的“部署历史记录”  进行跟踪，从 VM 追溯到管道再追溯到提交。 
 
  
**Canary 部署**：Canary 部署通过慢慢地向一小部分用户推出更改来降低风险。 随着自己对新版本的信心越来越大，你可以开始将其发布到基础结构中的更多服务器，并将更多用户路由到该版本。 可以通过 Azure 门户使用持续交付选项配置以虚拟机为目标的 Canary 部署。 下面是分步演练。 
1. 登录到 Azure 门户，导航到虚拟机 
2. 在 VM 左窗格中，导航到“持续交付”菜单。 **** 单击 **“配置”** 。 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 在配置面板中单击“Azure DevOps 组织”，选择现有帐户或创建一个帐户。  然后，选择要在其下配置管道的项目。  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. 部署组是代表物理环境的部署目标计算机的逻辑集，例如“开发”、“测试”、“UAT”和“生产”。 可以创建新的部署组，也可以选择现有的部署组。 
5. 选择生成管道，该管道用于发布要部署到虚拟机的包。 请注意，发布的包在其根目录下的 `deployscripts` 文件夹中应包含部署脚本 deploy.ps1 或 deploy.sh。   在运行时，此部署脚本将由 Azure DevOps 管道执行。
6. 选择所需的部署策略。 选择“Canary”。 
7. 将“canary”标记添加到属于 Canary 部署一部分的 VM，将“prod”标记添加到 Canary 部署成功后属于部署一部分的 VM。 可以借助标记将目标限定为具有特定角色的 VM。
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

8. 单击对话框中的“确定”  ，配置持续交付管道。 现在，你将拥有一个配置为向虚拟机进行部署的持续交付管道。
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


9. 单击 Azure DevOps 中的发布管道对应的“编辑”，查看管道配置。  此管道包含三个阶段。 第一阶段是部署组阶段，部署到标记为“canary”的 VM。  第二阶段暂停管道，并等待通过手动干预来恢复运行。 用户对 Canary 部署的稳定性感到满意后，就可以恢复管道运行，这将运行第三阶段，即部署到标记为“prod”的 VM。  ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

10. 在继续管道运行之前，请确保至少有一个 VM 标记为“prod”。  在管道的第三个阶段，应用程序将仅部署到那些带“prod”  标记的 VM。

11. 默认情况下，“执行部署脚本”任务将执行部署脚本 deploy.ps1 或 deploy.sh，该脚本位于已发布包的根目录下的“deployscripts”文件夹中。   请确保所选生成管道将其发布到包的根文件夹中。 
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>其他部署策略
- [配置滚动部署策略](https://aka.ms/AA7jlh8)
- [配置蓝绿部署策略](https://aka.ms/AA83fwu)

## <a name="azure-devops-project"></a>Azure DevOps 项目 
Azure 入门比以往更轻松。
 
使用 DevOps Projects，只需下述三个步骤即可在任何 Azure 服务上运行应用程序：选择应用程序语言、选择运行时，以及选择 Azure 服务。
 
[了解详细信息](https://azure.microsoft.com/features/devops-projects/ )。
 
## <a name="additional-resources"></a>其他资源 
- [使用 DevOps Projects 将应用部署到 Azure 虚拟机](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implement continuous deployment of your app to an Azure Virtual Machine Scale Set](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)（将应用持续部署到 Azure 虚拟机规模集）
