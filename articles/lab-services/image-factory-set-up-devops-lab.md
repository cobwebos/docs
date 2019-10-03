---
title: 从 Azure 开发测试实验室中的 Azure DevOps 运行映像工厂 |Microsoft Docs
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
ms.openlocfilehash: abb85d568e26e4b6f85b960a2560aae570daf201
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61320179"
---
# <a name="run-an-image-factory-from-azure-devops"></a>从 Azure DevOps 运行映像工厂
本文介绍如何从 Azure DevOps (以前称为 Visual Studio Team Services) 中运行映像工厂所需的所有准备工作。

> [!NOTE]
> 任何业务流程引擎将工作 ！ Azure DevOps 不是必需的。 映像工厂是运行使用 Azure PowerShell 脚本，因此它可能是手动运行，通过使用 Windows 任务计划程序，其他 CI/CD 系统中，依次类推。

## <a name="create-a-lab-for-the-image-factory"></a>实验室中创建的映像工厂
设置映像工厂的第一步是在 Azure 开发测试实验室中创建实验室。 此实验室是映像工厂实验室中，我们创建虚拟机并保存自定义映像。 此实验室都被视作整个图像工厂过程的一部分。 一旦创建实验室，请务必保存该名称，因为稍后需要用它更高版本。

## <a name="scripts-and-templates"></a>脚本和模板
采用你的团队的图像工厂的下一步是了解有哪些可用。 映像工厂脚本和模板是可公开推出[开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)。 下面是各个部分的概述：

- 映像工厂。 它是根文件夹。
    - 配置。 对映像工厂的输入
        - GoldenImages。 此文件夹包含 JSON 文件表示的自定义映像定义。
        - Labs.json。 其中团队注册以接收特定自定义映像的文件。
- 脚本。 映像工厂引擎。

在本部分中的文章提供有关这些脚本和模板的更多详细信息。

## <a name="create-an-azure-devops-team-project"></a>创建 Azure DevOps 团队项目
Azure DevOps 让你存储的源代码，请在一个位置中运行 Azure PowerShell。 您可以计划定期运行，使映像保持最新。 没有为日志记录结果的好工具诊断任何问题。  使用 Azure DevOps 但是不是必需的可以使用任何工具/引擎，可以连接到 Azure，可以运行 Azure PowerShell。

如果你有现有的 DevOps 帐户或你想要改为使用的项目，请跳过此步骤。

若要开始，请在 Azure DevOps 中创建一个免费帐户。 请访问 https://www.visualstudio.com/ ，然后选择**免费试用**下**Azure DevOps** (以前称为 VSTS)。 你将需要选择一个唯一的帐户的名称，请确保选择来管理使用 Git 的代码。 这创建后，将 URL 保存到你的团队项目。 下面是示例 URL: `https://<accountname>.visualstudio.com/MyFirstProject`。

## <a name="check-in-the-image-factory-to-git"></a>签入到 Git 的图像工厂
所有 PowerShell、 模板和映像工厂的配置中的都位置[公共开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)。 获取代码到新的团队项目的最快方法是导入存储库。 这将拉在整个开发测试实验室存储库中 （所以您会收到额外的文档和示例）。

1. 请访问上一步中创建的 Azure DevOps 项目 (URL 如下所示**https:\//\<accountname >.visualstudio.com/MyFirstProject**)。
2. 选择**导入存储库**。
3. 输入**克隆 URL**开发测试实验室存储库： `https://github.com/Azure/azure-devtestlab`。
4. 选择“导入”  。

    ![导入 Git 存储库](./media/set-up-devops-lab/import-git-repo.png)

如果您决定仅签入对完全需要 （图像工厂文件），请执行步骤[这里](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs)克隆 Git 存储库并推送仅位于中的文件**脚本/ImageFactory**目录。

## <a name="create-a-build-and-connect-to-azure"></a>创建版本并连接到 Azure
此时，可以存储在 Azure DevOps 中的 Git 存储库中的源文件。 现在，你需要设置管道以运行 Azure PowerShell。 有许多选项来执行这些步骤。 在本文中，为简单起见，使用生成定义但工作原理与 DevOps 生成，DevOps 版本 （单个或多个环境）、 Windows 任务计划程序等其他执行引擎或任何其他可以执行 Azure PowerShell 的工具。

> [!NOTE]
> 重要的一点需要注意的一些 PowerShell 文件需要很长时间才能有大量 （10 +） 自定义映像以创建时运行。 免费托管的 DevOps 生成/发布代理有 30 分钟的超时，因此不能使用免费的托管的代理，一旦您开始生成多个映像。 此超时质询应用到你决定使用任何工具，最好提前验证您可以扩展长时间运行的 Azure PowerShell 脚本的典型超时。 对于 Azure DevOps，可使用付费的托管的代理，或使用你自己的生成代理。

1. 若要开始，请选择**设置生成**DevOps 项目的主页上：

    ![安装程序生成按钮](./media/set-up-devops-lab/setup-build-button.png)
2. 指定**名称**生成 (例如：生成并将映像交付给开发测试实验室）。
3. 选择**空**生成定义，然后选择**应用**若要创建自己的版本。
4. 在此阶段，你可以选择**托管**的生成代理。
5. **保存**生成定义。

    ![生成定义](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>配置生成变量
若要简化的命令行参数，封装到一组生成变量驱动器映像工厂的键值。 选择**变量**选项卡，将看到几个默认变量的列表。 下面是将输入到 Azure DevOps 中的变量的列表：


| 变量名 | 值 | 说明 |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | 这是到存储库中的完整路径**配置**文件夹。 如果您导入更高版本的整个存储库，左侧的值正确。 否则，更新以指向配置位置。 |
| DevTestLabName | MyImageFactory | 实验室中 Azure 开发测试实验室作为工厂用于生成映像的名称。 如果你没有帐户，创建一个。 请确保实验室服务终结点有权访问位于同一订阅中。 |
| ImageRetention | 第 | 你想要保存的每种类型的映像数量。 设置默认值为 1。 |
| MachinePassword | ******* | 虚拟机内置管理员帐户密码。 这是暂时性的帐户，因此请确保它是安全。 选择位于右侧，以确保它是一个安全字符串的小锁定图标。 |
| MachineUserName | ImageFactoryUser | 内置管理员帐户的用户名为虚拟机。 这是暂时性的帐户。 |
| StandardTimeoutMinutes | 30 | 我们应等待常规 Azure 操作超时。 |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | 其中存在实验室和服务终结点具有访问权限的订阅的 ID。 |
| VMSize | Standard_A3 | 要用于虚拟机的大小**创建**步骤。 创建的 Vm 都是暂时性的。 大小必须是这[启用实验室](devtest-lab-set-lab-policy.md)。 确认是否有足够[订阅核心配额](../azure-subscription-service-limits.md)。

![生成变量](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>连接到 Azure
下一步是设置服务主体。 这是使 DevOps 生成代理能够代表该用户在 Azure 中运行的 Azure Active Directory 中的标识。 若要设置它，开始添加第一个 Azure PowerShell 生成步骤。

1. 选择**将任务添加**。
2. 搜索**Azure PowerShell**。
3. 一旦找到它，选择**添加**若要将任务添加到生成。 执行此操作时，您将看到显示在左侧，随着添加的任务。

![设置 PowerShell 步骤](./media/set-up-devops-lab/set-up-powershell-step.png)

设置服务主体的最快方法是让 Azure 为我们的 DevOps。

1. 选择**任务**您刚刚添加。
2. 有关**Azure 连接类型**，选择**Azure 资源管理器**。
3. 选择**管理**链接以设置服务主体。

有关详细信息，请参阅此[博客文章](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)。 当选择**管理**将进入在 DevOps 中正确的位置的链接，（博客文章中的第二个屏幕截图） 若要设置到 Azure 的连接。 请务必选择**Azure 资源管理器服务终结点**时此设置。

## <a name="complete-the-build-task"></a>完成生成任务
如果你选择生成任务，您将看到所有的详细信息，在右窗格中应填充。

1. 首先，命名生成任务：**创建虚拟机**。
2. 选择**服务主体**通过选择创建**Azure 资源管理器**
3. 选择**服务终结点**。
4. 有关**脚本路径**，选择 **...（省略号）** 在右侧。
5. 导航到**MakeGoldenImageVMs.ps1**脚本。
6. 脚本参数应如下所示： `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![完成的生成定义](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>对生成进行排队
让我们验证必须正确设置通过队列对新生成的所有内容。 在生成运行时，切换到[Azure 门户](https://portal.azure.com)，然后选择上**的所有虚拟机**中图像工厂实验室，以确认一切正常运行。 您应看到在实验室中创建三个虚拟机。

![在实验室中的 Vm](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>后续步骤
设置基于 Azure 开发测试实验室的图像工厂的第一步已完成。 在该系列的下一篇文章，获取这些 Vm 通用化并保存到自定义映像。 然后，您将它们都分发到所有其他实验室。 请参阅该系列的下一篇文章：[保存自定义映像并将分发到多个实验室](image-factory-save-distribute-custom-images.md)。
