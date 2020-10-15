---
title: 教程 - 为 Azure Linux 虚拟机配置 Canary 部署
description: 本教程介绍如何设置持续部署 (CD) 管道。 此管道使用 Canary 部署策略更新一组 Azure Linux 虚拟机。
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
ms.openlocfilehash: fafe2ba9ae65f95d8a2258ead5715cc0630202ba
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963476"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>教程 - 为 Azure Linux 虚拟机配置 Canary 部署策略

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>基础结构即服务 (IaaS) - 配置 CI/CD

Azure Pipelines 提供了一组功能完备的 CI/CD 自动化工具，用于目标为虚拟机的部署。 可以通过 Azure 门户为 Azure VM 配置持续交付管道。

本文介绍如何设置一个使用 Canary 策略进行多计算机部署的 CI/CD 管道。 Azure 门户还支持[滚动](./tutorial-devops-azure-pipelines-classic.md)和[蓝绿](./tutorial-azure-devops-blue-green-strategy.md)等其他策略。

### <a name="configure-cicd-on-virtual-machines"></a>在虚拟机上配置 CI/CD

可以将虚拟机作为目标添加到[部署组](/azure/devops/pipelines/release/deployment-groups)。 然后，可以将它们作为目标进行多计算机更新。 部署到计算机后，请查看部署组中的**部署历史记录**。 使用此视图可以从 VM 跟踪到管道，然后再跟踪到提交。

### <a name="canary-deployments"></a>Canary 部署

Canary 部署通过缓慢地向一小部分用户推出更改来降低风险。 当你对新版本满怀信心时，你可以将其发布到基础结构中的更多服务器，并向更多用户提供该版本。

使用持续交付选项，可以通过 Azure 门户配置目标为虚拟机的 Canary 部署。 下面是分步演练：

1. 登录到 Azure 门户，导航到虚拟机。
1. 在 VM 设置的最左侧窗格中，选择“持续交付”。 然后选择“配置”。

   ![带有“配置”按钮的“持续交付”窗格](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. 在配置面板中，选择“Azure DevOps 组织”以选择现有帐户或新建一个帐户。 然后，选择要配置管道的项目。  

   ![“持续交付”面板](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. 部署组是按逻辑分组到一起的部署目标计算机，用于表示物理环境。 例如“开发”、“测试”、“UAT”和“生产”。 可以创建新的部署组，也可以选择现有的部署组。
1. 选择生成管道，该管道用于发布要部署到虚拟机的包。 发布的包应在其根目录下的 deployscripts 文件夹中包含名为 deploy.ps1 或 deploy.sh 的部署脚本。 管道将运行此部署脚本。
1. 在“部署策略”中选择“Canary”。 
1. 向要成为 Canary 部署的一部分的 VM 添加“canary”标记。 向属于 Canary 部署成功后进行的部署的 VM 添加“prod”标记。 可以借助标记将目标限定为具有特定角色的 VM。

   ![“持续交付”面板，其中已选择“Canary”作为“部署策略”值](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. 选择“确定”可配置用于向虚拟机进行部署的持续交付管道。

   ![Canary 管道](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. 此时将显示虚拟机的部署详细信息。 可以选择该链接，以转到 Azure DevOps 中的发布管道。 在发布管道中，选择“编辑”查看管道配置。 管道分为以下三个阶段：

   1. 此阶段是部署组阶段。 应用程序将部署到标记为“canary”的 VM。
   1. 在此阶段中，管道将暂停，并等待手动干预来恢复运行。
   1. 这又是一个部署组阶段。 现在，该更新会部署到标记为“prod”的 VM。

      ![“部署 Canary”任务的“部署组”窗格](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. 在恢复管道运行之前，请确保至少有一个 VM 标记为“prod”。 在管道的第三个阶段，应用程序将仅部署到那些带“prod”标记的 VM。

1. “执行部署脚本”任务在默认情况下会运行部署脚本 deploy.ps1 或 deploy.sh。该脚本位于已发布包的根文件夹中的 deployscripts 文件夹下。 请确保所选生成管道将该部署发布到包的根文件夹中。

   ![“项目”窗格，其中显示了 deployscripts 文件夹中的 deploy.sh](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>其他部署策略
- [配置滚动部署策略](./tutorial-devops-azure-pipelines-classic.md)
- [配置蓝绿部署策略](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

可以轻松开始使用 Azure。 使用 Azure DevOps Projects，只需分三步选择以下各项，即可开始在任何 Azure 服务上运行应用程序：

- 应用程序语言
- 运行时
- Azure 服务

[了解详细信息](https://azure.microsoft.com/features/devops-projects/)。

## <a name="additional-resources"></a>其他资源

- [使用 Azure DevOps Projects 部署到 Azure 虚拟机](../../devops-project/azure-devops-project-vms.md)
- [Implement continuous deployment of your app to an Azure virtual machine scale set](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)（实现将应用持续部署到 Azure 虚拟机规模集）