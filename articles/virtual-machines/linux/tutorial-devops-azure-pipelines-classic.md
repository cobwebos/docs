---
title: 教程 - 为 Azure Linux 虚拟机配置滚动部署
description: 本教程介绍如何设置持续部署 (CD) 管道。 此管道使用滚动部署策略以增量方式更新一组 Azure Linux 虚拟机。
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
ms.openlocfilehash: f77cc85bd62deb2cb2cb74c42cf245a409904b3a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978299"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>教程 - 为 Azure Linux 虚拟机配置滚动部署策略

Azure DevOps 是一项内置的 Azure 服务，可为任何 Azure 资源自动执行 DevOps 过程的每个部分。 不管应用使用的是虚拟机、Web 应用、Kubernetes 还是任何其他资源，都可以通过 Azure 和 Azure DevOps 实现基础结构即代码 (IaC)、持续集成、持续测试、持续交付以及持续监视。

![Azure 门户，其中已在“服务”下选择了“Azure DevOps”](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>基础结构即服务 (IaaS) - 配置 CI/CD

Azure Pipelines 提供了一组功能完备的 CI/CD 自动化工具，用于目标为虚拟机的部署。 可以通过 Azure 门户为 Azure VM 配置持续交付管道。

本文介绍如何通过 Azure 门户设置一个用于滚动多计算机部署的 CI/CD 管道。 Azure 门户还支持 [Canary](./tutorial-azure-devops-canary-strategy.md) 和[蓝绿](./tutorial-azure-devops-blue-green-strategy.md)等其他策略。

### <a name="configure-cicd-on-virtual-machines"></a>在虚拟机上配置 CI/CD

可以将虚拟机作为目标添加到[部署组](/azure/devops/pipelines/release/deployment-groups)。 然后，可以将它们作为目标进行多计算机更新。 部署到计算机后，请查看部署组中的**部署历史记录**。 使用此视图可以从 VM 跟踪到管道，然后再跟踪到提交。

### <a name="rolling-deployments"></a>滚动部署

在每次迭代中，滚动部署将替换应用程序以前版本的实例。 它在一组固定的计算机（滚动集）上将这些实例替换为新版本的实例。 以下演练演示如何配置对虚拟机的滚动更新。

使用持续交付选项，可以在 Azure 门户中配置对虚拟机的滚动更新。 下面是分步演练：

1. 登录到 Azure 门户，导航到虚拟机。
1. 在 VM 设置的最左侧窗格中，选择“持续交付”。 然后选择“配置”。

   ![带有“配置”按钮的“持续交付”窗格](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. 在配置面板中，选择“Azure DevOps 组织”以选择现有帐户或新建一个帐户。 然后，选择要配置管道的项目。  

   ![“持续交付”面板](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. 部署组是按逻辑分组到一起的部署目标计算机，用于表示物理环境。 例如“开发”、“测试”、“UAT”和“生产”。 可以创建新的部署组，也可以选择现有的部署组。
1. 选择生成管道，该管道用于发布要部署到虚拟机的包。 发布的包应在其根目录下的 deployscripts 文件夹中包含名为 deploy.ps1 或 deploy.sh 的部署脚本。 管道将运行此部署脚本。
1. 在“部署策略”中选择“滚动”。 
1. （可选）可以使用角色来标记每个计算机。 例如，可以使用标记“web”和“db”。 可以借助这些标记将目标限定为具有特定角色的 VM。
1. 选择“确定”以配置持续交付管道。
1. 完成配置后，你将拥有一个配置为向虚拟机进行部署的持续交付管道。  

   ![“持续交付”面板，其中显示了“部署”历史记录](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. 此时将显示虚拟机的部署详细信息。 可以选择该链接来转到管道 Release-1 查看部署，或选择“编辑”以修改 release-pipeline 定义。

1. 如果要配置多个 VM，请对要添加到部署组的其他 VM 重复步骤 2 到 4。 如果选择已具有管道运行的部署组，则 VM 只会添加到该部署组。 不会创建新的管道。
1. 配置完成后，选择管道定义，导航到 Azure DevOps 组织，并针对该发布管道选择“编辑”。

   ![编辑滚动管道](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. 选择“dev”阶段中的“1 个作业，1 个任务”。 选择“部署”阶段。

   ![滚动管道任务，其中已选择了“部署”任务](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. 在最右侧的配置窗格中，可以指定要在每次迭代中并行部署的计算机数。 如果你希望一次部署到多个计算机，可以使用滑块以百分比的形式指定计算机数。  

1. “执行部署脚本”任务在默认情况下会执行部署脚本 deploy.ps1 或 deploy.sh。该脚本位于已发布包的根文件夹中的 deployscripts 文件夹下。

   ![“项目”窗格，其中显示了 deployscripts 文件夹中的 deploy.sh](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>其他部署策略

- [配置 Canary 部署策略](./tutorial-azure-devops-canary-strategy.md)
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