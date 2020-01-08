---
title: 在 Azure 开发测试实验室中从 Azure DevOps 运行映像工厂 |Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中创建自定义映像工厂。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 101ed792f091a5074b42e3d06eed27d606d3d2a7
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638946"
---
# <a name="run-an-image-factory-from-azure-devops"></a>从 Azure DevOps 运行映像工厂
本文介绍了从 Azure DevOps （以前称为 Visual Studio Team Services）运行映像工厂所需的所有准备工作。

> [!NOTE]
> 所有业务流程引擎都适用！ Azure DevOps 不是必需的。 映像工厂使用 Azure PowerShell 脚本运行，因此它可以使用 Windows 任务计划程序、其他 CI/CD 系统等进行手动运行。

## <a name="create-a-lab-for-the-image-factory"></a>为映像工厂创建实验室
设置映像工厂的第一步是在 Azure 开发测试实验室中创建实验室。 此实验室是映像工厂实验室，我们在其中创建虚拟机并保存自定义映像。 此实验室被视为总体映像工厂过程的一部分。 创建实验室后，请确保保存该名称，因为稍后需要用到它。

## <a name="scripts-and-templates"></a>脚本和模板
采用你的团队的映像工厂的下一步是了解可用的内容。 映像工厂脚本和模板在[开发测试 Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)存储库中公开提供。 下面是部分的概述：

- 映像工厂。 它是根文件夹。
    - 配置。 映像工厂的输入
        - GoldenImages. 此文件夹包含表示自定义映像定义的 JSON 文件。
        - Labs。 团队注册以接收特定自定义映像的文件。
- 稿本. 映像工厂的引擎。

本部分中的文章提供了有关这些脚本和模板的更多详细信息。

## <a name="create-an-azure-devops-team-project"></a>创建 Azure DevOps 团队项目
Azure DevOps 可让你存储源代码，在一个位置运行 Azure PowerShell。 您可以计划定期运行以使图像保持最新状态。 有很好的工具可用于记录结果以诊断任何问题。  不过，使用 Azure DevOps 不是一种要求，你可以使用任何可连接到 Azure 并可以运行 Azure PowerShell 的工具/引擎。

如果要改为使用现有的 DevOps 帐户或项目，请跳过此步骤。

若要开始，请在 Azure DevOps 中创建一个免费帐户。 请访问 https://www.visualstudio.com/ ，并选择**Azure DevOps** （以前称为 VSTS）下**免费试用**版。 需要选择唯一的帐户名称，并确保选择使用 Git 管理代码。 创建此后，将 URL 保存到团队项目。 下面是示例 URL： `https://<accountname>.visualstudio.com/MyFirstProject`。

## <a name="check-in-the-image-factory-to-git"></a>将映像工厂签入到 Git
映像工厂的所有 PowerShell、模板和配置都位于[公共开发测试 Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)存储库中。 将代码引入新团队项目的最快方法是导入存储库。 这会纳入整个开发测试实验室存储库（因此，你将获得额外的文档和示例）。

1. 访问在上一步中创建的 Azure DevOps 项目（URL 类似于**https：\//\<accountname >. visualstudio/MyFirstProject**）。
2. 选择 "**导入存储库**"。
3. 输入开发测试实验室存储库的**克隆 URL** ： `https://github.com/Azure/azure-devtestlab`。
4. 选择“导入”。

    ![导入 Git 存储库](./media/set-up-devops-lab/import-git-repo.png)

如果你决定仅查看所需的内容（映像工厂文件），请按照[此处](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs)的步骤克隆 Git 存储库并仅推送位于**scripts/ImageFactory**目录中的文件。

## <a name="create-a-build-and-connect-to-azure"></a>创建生成并连接到 Azure
此时，你的源文件存储在 Azure DevOps 中的 Git 存储库中。 现在，需要设置管道以运行 Azure PowerShell。 有很多选项可用于执行这些步骤。 在本文中，将使用生成定义简化，但它适用于 DevOps Build、DevOps Release （单个或多个环境）、其他执行引擎（如 Windows 任务计划程序）或任何其他可执行 Azure PowerShell 的工具。

> [!NOTE]
> 需要注意的一个重要一点是，如果要创建很多（10 +）个自定义映像，则某些 PowerShell 文件需要较长时间才能运行。 免费托管的 DevOps 生成/发布代理的超时时间为30分钟，因此在开始构建多个映像后，不能使用免费的托管代理。 此超时质询适用于你决定使用的任何工具，最好先验证是否可以扩展长时间运行 Azure PowerShell 脚本的典型超时。 对于 Azure DevOps，可以使用付费托管代理，也可以使用自己的生成代理。

1. 若要开始，请选择 DevOps 项目主页上的 "**设置生成**"：

    ![安装程序生成按钮](./media/set-up-devops-lab/setup-build-button.png)
2. 指定生成的**名称**（例如：生成映像并将其传送到开发测试实验室）。
3. 选择一个**空**的生成定义，然后选择 "**应用**" 创建生成。
4. 在此阶段，你可以选择 "为生成代理**托管**"。
5. **保存**生成定义。

    ![生成定义](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>配置生成变量
若要简化命令行参数，请将用于驱动映像工厂的密钥值封装到一组生成变量中。 选择 "**变量**" 选项卡，您将看到多个默认变量的列表。 下面是要在 Azure DevOps 中输入的变量的列表：


| 变量名 | 值 | 说明 |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | 这是存储库中指向**配置**文件夹的完整路径。 如果导入了上面的整个存储库，左侧的值是正确的。 否则，将更新为指向配置位置。 |
| DevTestLabName | MyImageFactory | Azure 开发测试实验室中的实验室名称，用作生成映像的工厂。 如果没有，请创建一个。 确保实验室与服务终结点有权访问的订阅相同。 |
| ImageRetention | 第 | 要保存的每种类型的映像数。 将默认值设置为1。 |
| MachinePassword | ******* | 虚拟机的内置管理员帐户密码。 这是暂时性的帐户，因此请确保它是安全的。 选择右侧的 "小锁定" 图标以确保它是安全字符串。 |
| MachineUserName | ImageFactoryUser | 虚拟机的内置管理员帐户用户名。 这是一个暂时性的帐户。 |
| StandardTimeoutMinutes | 30 | 等待常规 Azure 操作的超时时间。 |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | 实验室存在并且服务终结点有权访问的订阅的 ID。 |
| VMSize | Standard_A3 | 用于**创建**步骤的虚拟机的大小。 创建的 Vm 是暂时性的。 该大小必须是[为实验室启用](devtest-lab-set-lab-policy.md)的大小。 确认有足够的[订阅核心配额](../azure-resource-manager/management/azure-subscription-service-limits.md)。

![生成变量](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>连接到 Azure
下一步是设置服务主体。 这是 Azure Active Directory 中的标识，它允许 DevOps 生成代理代表用户在 Azure 中运行。 若要进行设置，请首先添加 Azure PowerShell 生成步骤。

1. 选择 "**添加任务**"。
2. 搜索**Azure PowerShell**。
3. 找到后，选择 "**添加**" 将任务添加到生成中。 当你执行此操作时，你会看到任务在左侧显示为 "已添加"。

![设置 PowerShell 步骤](./media/set-up-devops-lab/set-up-powershell-step.png)

若要设置服务主体，最快的方法是让 Azure DevOps 为我们执行此操作。

1. 选择刚刚添加的**任务**。
2. 对于 " **Azure 连接类型**"，请选择 " **azure 资源管理器**"。
3. 选择 "**管理**" 链接以设置服务主体。

有关详细信息，请参阅此[博客文章](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)。 当你选择 "**管理**" 链接时，你将在 DevOps （博客文章中的第二个屏幕截图）中的适当位置，设置到 Azure 的连接。 进行设置时，请确保选择 " **Azure 资源管理器服务终结点**"。

## <a name="complete-the-build-task"></a>完成生成任务
如果你选择 "生成" 任务，你将看到右窗格中应填写的所有详细信息。

1. 首先，命名生成任务： "**创建虚拟机**"。
2. 选择通过选择 " **Azure 资源管理器**创建的**服务主体**
3. 选择**服务终结点**。
4. 对于 "**脚本路径**"，选择 **。（省略号）** 右侧。
5. 导航到**MakeGoldenImageVMs**脚本。
6. 脚本参数应该如下所示： `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![完成生成定义](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>对生成进行排队
让我们验证是否已通过排队新版本来正确设置所有内容。 生成运行时，切换到 " [Azure 门户](https://portal.azure.com)"，并选择 "映像工厂实验室" 中的**所有虚拟机**，以确认所有内容是否正常工作。 应会看到在实验室中创建了三个虚拟机。

![实验室中的 Vm](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>后续步骤
基于 Azure 开发测试实验室设置映像工厂的第一步是完成。 在本系列的下一篇文章中，你将获得通用化并保存到自定义映像的 Vm。 然后，将其分发给其他实验室。 请参阅本系列文章中的下一篇文章：[保存自定义映像并将其分发到多个实验室](image-factory-save-distribute-custom-images.md)。
