---
title: 教程 - 为 Azure Linux 虚拟机配置 Canary 部署
description: 本教程介绍如何设置持续部署 (CD) 管道。 此管道使用蓝绿部署策略更新一组 Azure Linux 虚拟机。
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
ms.openlocfilehash: f349ff62fe211f0610341864a4c7528ee6bfe9c5
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961521"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>教程 - 为 Azure Linux 虚拟机配置蓝绿部署策略

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>基础结构即服务 (IaaS) - 配置 CI/CD

Azure Pipelines 提供了一组功能完备的 CI/CD 自动化工具，用于目标为虚拟机的部署。 可以通过 Azure 门户为 Azure VM 配置持续交付管道。

本文介绍如何设置一个使用蓝绿策略进行多计算机部署的 CI/CD 管道。 Azure 门户还支持[滚动](./tutorial-devops-azure-pipelines-classic.md)和 [Canary](./tutorial-azure-devops-canary-strategy.md) 等其他策略。

### <a name="configure-cicd-on-virtual-machines"></a>在虚拟机上配置 CI/CD

可以将虚拟机作为目标添加到[部署组](/azure/devops/pipelines/release/deployment-groups)。 然后，可以将它们作为目标进行多计算机更新。 部署到计算机后，请查看部署组中的**部署历史记录**。 使用此视图可以从 VM 跟踪到管道，然后再跟踪到提交。

### <a name="blue-green-deployments"></a>蓝绿部署

蓝绿部署通过使用具有相同配置的备用环境来减少中断服务的时间。 任何时候都只能有一个环境处于活动状态。

为新版本做准备时，请在绿色环境中完成最后的测试阶段。 软件在绿色环境中正常运行后，请切换流量，使所有传入请求进入绿色环境。 蓝色环境将处于空闲状态。

使用持续交付选项，可以通过 Azure 门户配置目标为虚拟机的蓝绿部署。 下面是分步演练：

1. 登录到 Azure 门户，导航到虚拟机。
1. 在 VM 设置的最左侧窗格中，选择“持续交付”。 然后选择“配置”。

   ![带有“配置”按钮的“持续交付”窗格](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. 在配置面板中，选择“Azure DevOps 组织”以选择现有帐户或新建一个帐户。 然后，选择要配置管道的项目。  

   ![“持续交付”面板](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. 部署组是按逻辑分组到一起的部署目标计算机，用于表示物理环境。 例如“开发”、“测试”、“UAT”和“生产”。 可以创建新的部署组，也可以选择现有的部署组。
1. 选择生成管道，该管道用于发布要部署到虚拟机的包。 发布的包应在其根目录下的 deployscripts 文件夹中包含名为 deploy.ps1 或 deploy.sh 的部署脚本。 管道将运行此部署脚本。
1. 在“部署策略”中选择“蓝绿”。 
1. 将“blue”或“green”标记添加到要成为蓝绿部署的一部分的 VM。 如果 VM 充当备用角色，请将其标记为“green”。 否则，请将其标记为“blue”。

   ![“持续交付”面板，其中已选择“蓝绿”作为“部署策略”值](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. 选择“确定”可配置用于向虚拟机进行部署的持续交付管道。

   ![蓝绿管道](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. 此时将显示虚拟机的部署详细信息。 可以选择该链接，以转到 Azure DevOps 中的发布管道。 在发布管道中，选择“编辑”查看管道配置。 管道分为以下三个阶段：

   1. 此阶段是部署组阶段。 应用程序将部署到已标记为“green”的备用 VM。
   1. 在此阶段中，管道将暂停，并等待手动干预来恢复运行。 当用户以手动方式确保了目标为已标记为“green”的 VM 的部署的稳定性后，便可以恢复管道运行。
   1. 此阶段将交换 VM 的“blue”和“green”标记。 这将确保具有较旧应用程序版本的 VM 现在会标记为“green”。 在下一次管道运行期间，应用程序将部署到这些 VM。

      ![“部署蓝绿”任务的“部署组”窗格](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. “执行部署脚本”任务在默认情况下会运行部署脚本 deploy.ps1 或 deploy.sh。该脚本位于已发布包的根文件夹中的 deployscripts 文件夹下。 请确保所选生成管道将该部署发布到包的根文件夹中。

   ![“项目”窗格，其中显示了 deployscripts 文件夹中的 deploy.sh](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>其他部署策略

- [配置滚动部署策略](./tutorial-devops-azure-pipelines-classic.md)
- [配置 Canary 部署策略](./tutorial-azure-devops-canary-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

可以轻松开始使用 Azure。 使用 Azure DevOps Projects，只需分三步选择以下各项，即可开始在任何 Azure 服务上运行应用程序：

- 应用程序语言
- 运行时
- Azure 服务

[了解详细信息](https://azure.microsoft.com/features/devops-projects/)。

## <a name="additional-resources"></a>其他资源

- [使用 Azure DevOps Projects 部署到 Azure 虚拟机](../../devops-project/azure-devops-project-vms.md)
- [Implement continuous deployment of your app to an Azure virtual machine scale set](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)（实现将应用持续部署到 Azure 虚拟机规模集）