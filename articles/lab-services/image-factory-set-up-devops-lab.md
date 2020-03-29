---
title: 在 Azure 开发人员测试实验室中从 Azure DevOps 运行映像工厂
description: 本文介绍从 Azure DevOps（以前的可视化工作室团队服务）运行映像工厂所需的所有准备工作。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bb67f765684c77ed5f8527226bef578e450579e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758676"
---
# <a name="run-an-image-factory-from-azure-devops"></a>从 Azure DevOps 运行映像工厂
本文介绍从 Azure DevOps（以前的可视化工作室团队服务）运行映像工厂所需的所有准备工作。

> [!NOTE]
> 任何业务流程引擎都将工作！ Azure DevOps 不是强制性的。 映像工厂使用 Azure PowerShell 脚本运行，因此可以使用 Windows 任务计划程序、其他 CI/CD 系统等手动运行映像。

## <a name="create-a-lab-for-the-image-factory"></a>为映像工厂创建实验室
设置映像工厂的第一步是在 Azure 开发人员测试实验室中创建实验室。 本实验室是映像工厂实验室，我们在此创建虚拟机并保存自定义映像。 本实验被视为整个图像工厂过程的一部分。 创建实验室后，请确保保存名称，因为稍后需要它。

## <a name="scripts-and-templates"></a>脚本和模板
为您的团队采用映像工厂的下一步是了解可用的功能。 映像工厂脚本和模板在[开发人员测试实验室 GitHub 存储库中](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)公开提供。 以下是部分的概述：

- 图像工厂。 它是根文件夹。
    - 配置。 映像工厂的输入
        - 黄金图像。 此文件夹包含表示自定义图像定义的 JSON 文件。
        - 实验室.json. 文件团队注册以接收特定的自定义映像。
- 脚本。 映像工厂的发动机。

本节中的文章提供了有关这些脚本和模板的更多详细信息。

## <a name="create-an-azure-devops-team-project"></a>创建 Azure DevOps 团队项目
Azure DevOps 允许您存储源代码，在一个位置运行 Azure PowerShell。 您可以安排定期运行，使映像保持最新。 记录结果以诊断任何问题有良好的功能。  但是，使用 Azure DevOps 不是一项要求，您可以使用任何可以连接到 Azure 并可以运行 Azure PowerShell 的线束/引擎。

如果您有要使用的现有 DevOps 帐户或项目，请跳过此步骤。

要开始，请在 Azure DevOps 中创建一个免费帐户。 访问https://www.visualstudio.com/并选择**Azure DevOps（** 以前的 VSTS）下的**免费入门**。 您需要选择唯一的帐户名称，并确保选择使用 Git 管理代码。 创建此选项后，将 URL 保存到团队项目中。 下面是一个示例 URL： `https://<accountname>.visualstudio.com/MyFirstProject`。

## <a name="check-in-the-image-factory-to-git"></a>签入映像工厂到 Git
映像工厂的所有 PowerShell、模板和配置都位于[公共 DevTest Labs GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)中。 将代码导入新团队项目的最快方法是导入存储库。 这将提取整个 DevTest Labs 存储库（因此您将获得额外的文档和示例）。

1. 访问您在上一步中创建的 Azure DevOps 项目（URL 看起来像**https：\//\<帐户名称>.visualstudio.com/MyFirstProject）。**
2. 选择 **"导入存储库**"。
3. 输入 DevTest 实验室回购的**克隆 URL：** `https://github.com/Azure/azure-devtestlab`。
4. 选择“导入”****。

    ![导入 Git 存储库](./media/set-up-devops-lab/import-git-repo.png)

如果您决定只签入所需的内容（映像工厂文件），请按照[此处](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs)的步骤克隆 Git 存储库，并仅推送**脚本/ImageFactory**目录中的文件。

## <a name="create-a-build-and-connect-to-azure"></a>创建生成并连接到 Azure
此时，在 Azure DevOps 中的 Git 存储库中存储源文件。 现在，您需要设置管道以运行 Azure PowerShell。 执行这些步骤有很多选项。 在本文中，使用生成定义是为了简单起见，但它适用于 DevOps 生成、DevOps 发布（单个或多个环境）、其他执行引擎（如 Windows 任务计划程序）或任何其他可以执行 Azure PowerShell 的工具。

> [!NOTE]
> 要记住的一个要点是，当要创建大量 （10+） 自定义映像时，某些 PowerShell 文件需要很长时间才能运行。 免费托管 DevOps 生成/发布代理的超时为 30 分钟，因此，一旦开始构建许多映像，您就无法使用免费托管代理。 此超时挑战适用于您决定使用的任何工具，最好预先验证是否可以延长长时间运行 Azure PowerShell 脚本的典型超时。 在 Azure DevOps 中，可以使用付费托管代理或使用自己的生成代理。

1. 要启动，请选择在 DevOps 项目的主页上**设置"生成**"：

    ![设置生成按钮](./media/set-up-devops-lab/setup-build-button.png)
2. 指定生成**的名称**（例如：生成映像并将图像传递到 DevTest 实验室）。
3. 选择**空**生成定义，然后选择 **"应用"** 以创建生成。
4. 在此阶段，您可以选择生成代理的 **"托管**"。
5. **保存**生成定义。

    ![生成定义](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>配置生成变量
要简化命令行参数，封装将映像工厂驱动到一组生成变量的键值。 选择 **"变量"** 选项卡，您将看到多个默认变量的列表。 下面是要输入到 Azure DevOps 的变量列表：


| 变量名 | “值” | 说明 |
| ------------- | ----- | ----- |
| 配置位置 | /脚本/映像工厂/配置 | 这是存储库中到 **"配置"** 文件夹的完整路径。 如果上述导入了整个回购，则左侧的值是正确的。 否则更新以指向"配置"位置。 |
| 开发测试实验室名称 | 我的图像工厂 | Azure 开发人员测试实验室中实验室的名称用作生成映像的工厂。 如果没有，请创建一个。 确保实验室与服务终结点有权访问的订阅相同。 |
| 图像保留 | 1 | 要保存每种类型的图像数。 将默认值设置为 1。 |
| MachinePassword | ******* | 虚拟机的内置管理员帐户密码。 这是一个瞬态帐户，因此请确保它是安全的。 选择右侧的小锁图标，以确保它是一个安全的字符串。 |
| 机器用户名 | 图像工厂用户 | 虚拟机的内置管理员帐户用户名。 这是一个瞬态帐户。 |
| 标准超时分钟数 | 30 | 超时，我们应该等待常规的 Azure 操作。 |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | 实验室存在且服务终结点有权访问的订阅的 ID。 |
| VMSize | Standard_A3 | 要用于**创建**步骤的虚拟机的大小。 创建的 VM 是瞬态的。 大小必须是[为实验室启用的](devtest-lab-set-lab-policy.md)。 确认有足够的[订阅核心配额](../azure-resource-manager/management/azure-subscription-service-limits.md)。

![生成变量](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>连接到 Azure
下一步是设置服务主体。 这是 Azure 活动目录中的标识，使 DevOps 生成代理能够代表用户在 Azure 中操作。 要设置它，首先添加第一个 Azure PowerShell 生成步骤。

1. 选择 **"添加任务**"。
2. 搜索**Azure 电源外壳**。
3. 找到它后，选择 **"添加"** 以将任务添加到生成。 执行此操作时，您将看到任务在左侧显示为添加。

![设置 PowerShell 步骤](./media/set-up-devops-lab/set-up-powershell-step.png)

设置服务主体的最快方法是让 Azure DevOps 为我们执行此操作。

1. 选择您刚刚添加**的任务**。
2. 对于**Azure 连接类型**，请选择**Azure 资源管理器**。
3. 选择 **"管理**"链接以设置服务主体。

有关详细信息，请参阅此[博客文章](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)。 选择 **"管理"** 链接时，您将在 DevOps（博客文章中的第二个屏幕截图）中的位置放置，以设置与 Azure 的连接。 设置 Azure**资源管理器服务终结点**时，请确保选择该终结点。

## <a name="complete-the-build-task"></a>完成生成任务
如果选择生成任务，您将看到应填写的右侧窗格中的所有详细信息。

1. 首先，命名生成任务：**创建虚拟机**。
2. 选择通过选择**Azure 资源管理器**创建的**服务主体**
3. 选择**服务终结点**。
4. 对于**脚本路径**，请选择 **...（省略）** 在右边。
5. 导航到**制作 GoldenImageVM.ps1**脚本。
6. 脚本参数应如下所示：`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![完成生成定义](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>对生成进行排队
让我们通过排队新生成来验证所有内容都已正确设置。 在生成运行时，切换到[Azure 门户](https://portal.azure.com)，并在映像工厂实验室**中的所有虚拟机**上选择，以确认一切工作正常。 您应该会看到在实验室中创建三个虚拟机。

![实验室中的 VM](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>后续步骤
基于 Azure 开发人员测试实验室设置映像工厂的第一步已完成。 在本系列的下一篇文章中，您可以对这些 VM 进行泛化并保存到自定义映像。 然后，将它们分发到所有其他实验室。 请参阅本系列的下一篇文章：[保存自定义映像并分发到多个实验室](image-factory-save-distribute-custom-images.md)。
