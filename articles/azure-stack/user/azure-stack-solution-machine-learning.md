---
title: 创建边缘机器学习使用 Azure 和 Azure Stack 解决方案 |Microsoft Docs
description: 了解如何创建将 Python 与 Azure 和 Azure Stack 使用 edge 机器学习解决方案。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/26/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 057df4260da267254cb764e58fdac749bee3b842
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106747"
---
# <a name="tutorial-create-an-edge-machine-learning-solution-with-azure-and-azure-stack"></a>教程： 创建边缘机器学习使用 Azure 和 Azure Stack 解决方案

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何创建边缘机器学习使用 Azure 和 Azure Stack 解决方案。

在本教程中，我们将构建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 创建存储帐户和清理的数据驻留的容器。
> - 在 Azure 门户中创建 Ubuntu 数据科学虚拟机 (DSVM)。
> - 部署 Azure 机器学习服务在 Azure 中构建和训练模型。
> - 创建 Azure 机器学习服务帐户。
> - 部署和使用 Azure 容器注册表。
> - 将 Kubernetes 群集部署到 Azure Stack。
> - 创建数据的 Azure Stack 存储帐户和存储队列。
> - 创建一个新的 Azure Stack 函数来从 Azure Stack 的干净的数据移到 Azure。

**何时使用此解决方案**

 -  你的组织使用的是 DevOps 的方法，或具有一个计划在不久的将来。
 -  你想要在 Azure Stack 实现和公有云之间实现 CI/CD 做法。
 -  你想要将 CI/CD 管道合并跨云和本地环境。
 -  你想使用在本地或云服务开发应用程序的功能。
 -  你想要利用跨云和本地应用程序一致的开发人员技能。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 将云计算的灵活性和创新性带入你的本地环境，并支持唯一的混合云，以允许你在任何地方构建和部署混合应用。  
> 
> 白皮书 [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars)（混合应用程序的设计注意事项）回顾了设计、部署和运行混合应用程序所需的软件质量要素（位置、可伸缩性、可用性、复原能力、可管理性和安全性）。 这些设计注意事项有助于优化混合应用程序设计，从而最大限度地减少生产环境中的难题。

## <a name="prerequisites"></a>必备组件

几个组件生成此用例所需组件，可能需要一些时间来准备：

 -  Azure OEM/硬件合作伙伴可以在部署生产 Azure Stack 和所有用户可以都部署 ASDK

 -  Azure Stack 操作员还必须部署应用服务、 创建计划和产品/服务、 创建租户订阅，并添加 Windows Server 2016 映像

 -  混合网络和应用服务是必需的安装程序 (深入了解[应用与 Vnet 集成。](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet))

 -  私有[生成和发布代理](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts)的 （请确保使用任何现有组件是否符合要求，然后开始）。 在部署前必须设置 VSTS 集成

事先了解 Azure 和 Azure Stack 是必需的。 若要了解详情之后再继续，请使用以下主题着手：

 -  [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)

 -  [Azure Stack 的重要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

 -  [Azure Stack 混合 CI/CD 解决方案指南](/azure/azure-stack/user/azure-stack-solution-pipeline)

**Azure**

 -  Azure 订阅 (创建[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

 -  通过创建新的 Web 应用 URL [Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-overview)在 Azure 中

 -  部署[Azure 容器服务 (ACS) Kubernetes on Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

 -  Azure 机器学习服务部署[两部分构成的教程](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)


**Azure Stack**

 -  Azure Stack 集成系统或 Azure Stack 开发工具包部署。

    - 查找有关安装 Azure Stack 上的说明[安装 Azure Stack 开发工具包](../asdk/asdk-install.md)。
     - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) 此安装可能需要几个小时才能完成。

 -  部署[应用服务](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy)到 Azure Stack 的 PaaS 服务

 -  一个[产品/服务计划/](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) Azure Stack 环境中

 -  一个[租户订阅](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)Azure Stack 环境中

 -  Ubuntu Server VM 映像 (在可用[Azure Stack Marketplace](https://buildazure.com/2016/05/04/azure-marketplace-ubuntu-server-16-04-lts/)) 此 VM 将生成与专用生成代理，以及 Kubernetes Vm Azure Stack 上的租户订阅中。 如果此图像不可用，Azure Stack 操作员可以提供帮助，以确保这添加到环境。 （不使用 18.04 生成的 ubuntu，因为当前不支持。）

 -  Web 应用中的租户订阅 （请注意新的 Web 应用 URL，以供将来使用。）

 -  部署的 VSTS 基于 Linux 的专用生成代理虚拟机，租户订阅中

 -  部署[Azure 容器服务 (ACS) Kubernetes 在 Azure Stack 上](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

**开发人员工具**

 -  [VSTS 工作区](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)（注册过程将创建一个名为"MyFirstProject"项目）

 -  [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio)安装并[VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)单一登录

 -  [本地克隆](https://www.visualstudio.com/docs/git/gitquickstart)的项目

 -  [Linux 子系统适用于 Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) （适用于 BASH 和 SSH） 的安装

 -  [适用于 Windows 的 docker](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe)安装

 -  [Azure Machine Learning Workbench （预览）](https://aka.ms/azureml-wb-msi)安装

 -  [Python](https://www.python.org/ftp/python/3.7.0/python-3.7.0rc1-amd64.exe)环境安装

**VSTS**

 -  **VSTS 帐户。** 快速设置持续集成生成、 测试和部署。 有关 VSTS 帐户的详细信息，请参阅[创建 VSTS 帐户](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student?view=vsts)。

 -  **代码存储库。** 使用 GitHub、 BitBucket、 DropBox、 One Drive 和 TFS 中的现有代码存储库，VSTS 平台可以利用多个代码存储库来简化开发管道。 有关代码的详细信息请参阅存储库[开始使用 Git 和 VSTS](https://docs.microsoft.com/vsts/git/gitquickstart?view=vsts&tabs=visual-studio)教程。

 -  **服务连接。** 连接到外部和远程服务进行测试、 生成或部署的任务执行。 详细了解 VSTS 服务连接，请参阅[的生成和发布服务终结点](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints?view=vsts)。

 -  **生成定义。** 定义自动的生成过程，并编写一系列任务通过任务目录中。 定义有关生成详细信息请参阅[创建生成定义](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts)文档。

 -  **发布定义。** 定义一系列其中部署应用程序项目的环境的部署过程。 定义版本有关的详细信息请参阅[创建发布定义](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts)文档。

 -  **托管的 VSTS Linux 生成代理池。** 快速构建、 测试和部署应用程序使用 Microsoft 管理和维护托管的代理。 详细了解托管 VSTS 生成代理观看[托管代理](https://docs.microsoft.com/vsts/build-release/concepts/agents/hosted?view=vsts)文档。

## <a name="step-1-create-a-storage-account"></a>步骤 1：创建存储帐户

创建存储帐户和清理的数据驻留的容器。

1.  登录到[ *Azure 门户*](https://portal.azure.com/)。

2.  在 Azure 门户中，展开左侧和右侧以打开服务菜单，然后选择菜单**所有服务**。 向下滚动到“存储”，选择“存储帐户”。 在 * * 存储帐户 * * 窗口选择**添加**。

3.  输入存储帐户的名称。

    > [!Note]  
    > 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 存储帐户名称必须是在 Azure 中唯一。 Azure 门户将指出是否选定的存储帐户名称已在使用。

4.  指定要使用的部署模型：**资源管理器**。

5.  选择存储帐户的类型：**常规用途 V1**，然后指定性能层：**标准**。

6.  选择存储帐户的复制选项： **GRS**。

7.  选择新存储帐户订阅。

8.  指定新资源组或选择现有资源组。

9.  选择存储帐户的地理位置。

10. 选择“创建”以创建存储帐户。

    ![Alt text](/media/azure-stack-solution-machine-learning/image1.png)

11.  选择最近创建的存储帐户。

12.  选择上**Blob**。

    ![Alt text](media/azure-stack-solution-machine-learning/image2.png)

13.  选择上 **+ 容器**，然后选择上**容器**。

    ![Alt text](media/azure-stack-solution-machine-learning/image3.png)

14.  为容器名称**uploadeddata** ，然后选择访问类型**容器**。

15.  选择上**创建**。

    ![Alt text](media/azure-stack-solution-machine-learning/image4.png)

## <a name="step-2-create-a-data-science-virtual-machine"></a>步骤 2： 创建数据科学虚拟机

在 Azure 门户中创建 Ubuntu 数据科学虚拟机 (DSVM)。

1.  登录到 Azure 门户从 [https://portal.azure.com](https://portal.azure.com/)

2.  选择上 **+ 新建**链接，然后搜索"数据科学虚拟机适用于 Linux Ubuntu CSP

    ![Alt text](media/azure-stack-solution-machine-learning/image5.png)

1.  选择**适用于 Linux (Ubuntu) 的数据科学虚拟机**在列表中，按照屏幕说明创建 DSVM。

    ![Alt text](media/azure-stack-solution-machine-learning/image6.png)

> ![重要]  
> **选择**密码 * * 作为*身份验证类型*。

与新创建的存储帐户相同的资源组中置于新 DSVM。 在此资源组中的 Azure 中部署边缘机器学习的所有对象。

1.  在设置配置可选功能

    a.  选择**存储帐户**之前创建。

    b.  创建一个新**虚拟网络**，**子网**，并**公共 IP**它应默认情况下创建基于资源组名称的名称。

    c.  创建一个新**NSG**它应自动创建此已应用的正确规则。

    d.  有关**诊断存储帐户**，选择前面创建的存储帐户。

    e.  注意： 使用 AAD 启用并配置 Azure 订阅，为 Azure 资源管理的标识可以启用也。

2.  选择“确定”。

### <a name="connect-to-the-dsvm"></a>连接到 DSVM

一旦创建 DSVM 后，连接到 VM 从 Windows 子系统适用于 Linux。

```Bash  
    ssh <user>@<DSVM Public IP>
```

1.  当系统提示你确认 VM 连接，请键入是。

2.  输入创建 dsvm 的密码。

### <a name="update-the-dsvm"></a>更新 DSVM 

```Bash  
sudo su 
apt-get update 
apt-get -y upgrade 
apt-get -y dist-upgrade 
apt-get -y autoremove
```

## <a name="step-3-deploy-azure-machine-learning-services"></a>步骤 3： 部署 Azure 机器学习服务

部署 Azure 机器学习在 Azure 中的服务。

Azure 机器学习服务（预览版）是集成式的端到端数据科学和高级分析解决方案。 它可让专业数据科学家以云的规模准备数据、开发试验和部署模型。

本部分介绍：

> [!div class="checklist"]
> - 创建 Azure 机器学习服务的服务帐户
> - 安装并登录 Azure Machine Learning Workbench。
> - 在 Workbench 中创建项目
> - 在该项目中运行脚本
> - 访问命令行界面 (CLI)

作为 Microsoft Azure 产品组合的一部分，Azure 机器学习服务需要 Azure 订阅。 若要获取 Azure 订阅，创建[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

创建资产，例如资源组、 虚拟机和其他资产所需的足够权限。

可能在以下操作系统上安装 Azure Machine Learning Workbench 应用程序：

 -  Windows 10 或 Windows Server 2016
 -  macOS Sierra 或 High Sierra

## <a name="step-4-create-azure-machine-learning-services"></a>步骤 4： 创建 Azure 机器学习服务

创建 Azure 机器学习服务帐户。

使用 Azure 门户来预配 Azure 机器学习帐户：

1.  登录到[Azure 门户](https://portal.azure.com/)使用 Azure 订阅的凭据来使用。 若要获取 Azure 订阅，创建[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    ![Alt text](media/azure-stack-solution-machine-learning/image7.png)

1.  选择门户左上角的“创建资源”按钮 (+)。

    ![在 Azure 门户中创建资源](media/azure-stack-solution-machine-learning/image8.png)

1.  在搜索栏中输入“机器学习”。 选择名为“机器学习试验(预览版)”的搜索结果。

    ![Azure 机器学习搜索](media/azure-stack-solution-machine-learning/image9.png)

1.  在中**机器学习试验**窗格中，滚动到底部，选择**创建**，开始定义试验帐户。

    ![Azure 机器学习 - 创建试验帐户](media/azure-stack-solution-machine-learning/image10.png)

1.  在中**机器学习试验**窗格配置机器学习试验帐户。

    | 设置 | 针对教程建议的值 | 说明 |
    |---------------------------------------|----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 试验帐户名称 | 唯一名称 | 输入用于标识帐户的唯一名称。 使用最好地标识试验的部门或项目名称。 名称应介于 2 到 32 个字符之间， 只应包含字母数字字符和短划线 (-) 字符。 |
    | 订阅 | 订阅 | 选择要用于试验的 Azure 订阅。 如果存在多个订阅，请选择计费的资源所在的相应订阅。 |
    | 资源组 | 资源组 | 在订阅中，使用现有的资源组或输入一个名称来创建新的资源组对于此试验帐户。 |
    | 位置 | 离用户最近的区域 | 选择最靠近用户和数据资源的位置。 |
    | 席位数 | 2 | 输入席位数。 了解[席位如何影响定价](https://azure.microsoft.com/pricing/details/machine-learning/)。<br><br>对于本快速入门教程，需要使用只有两个席位。 可以根据需要在 Azure 门户中添加或删除席位。 |
    | 存储帐户 | 唯一名称 | 选择“新建”，然后提供一个名称，用于创建 [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal)。 名称应该 3-24 个字符长，只应包含字母数字字符。 或者，选择**使用现有**并从列表中选择现有存储帐户。 存储帐户是必需的，用于保存项目内容和运行历史记录数据。 |
    | 试验帐户的工作区 | IrisGarden<br>（在教程中使用的名称） | 提供此帐户的工作区的名称。 名称应介于 2 到 32 个字符之间， 只应包含字母数字字符和短划线 (-) 字符。 此工作区包含创建、 管理和发布试验所需的工具。 |
    | 分配工作区的所有者 | 该帐户 | 选择自己的帐户作为工作区所有者。 |
    | 创建模型管理帐户 | **勾选标记** | 创建模型管理帐户。 这将用于部署和管理模型作为实时 web 服务。 <br><br>尽管都是可选的则建议试验帐户时创建模型管理帐户。 |
    | 帐户名 | 唯一名称 | 选择用于标识模型管理帐户的唯一名称。 使用部门或项目最好地标识试验的名称。 名称应介于 2 到 32 个字符之间， 只应包含字母数字字符和短划线 (-) 字符。 |
    | 模型管理定价层 | **DEVTEST** | 选择**未选择定价层**以指定新的模型管理帐户的定价层。 为了节省成本，如果订阅 （有限可用性） 上选择开发测试定价层。 否则，请选择 S1 定价层。 选择选择要保存所选定价层。 |
    | 固定到仪表板 | 对号 | 选择**固定到仪表板**选项，可以轻松地跟踪在 Azure 门户的前端仪表板页上的机器学习试验帐户。 |

    ![机器学习试验帐户配置](media/azure-stack-solution-machine-learning/image11.png)

1.  选择“创建”，开始试验帐户和模型管理帐户的创建过程。

    ![机器学习试验帐户配置](media/azure-stack-solution-machine-learning/image12.png)

    可能需要一些时间来创建帐户。 通过选择 Azure 门户工具栏上的通知图标 （铃铛） 检查部署过程的状态。

    ![Azure 门户中的通知](media/azure-stack-solution-machine-learning/image13.png)

### <a name="install-and-log-in-to-workbench"></a>安装并登录到 workbench 

Azure Machine Learning Workbench 适用于 Windows 或 macOS。 请参阅[支持的平台](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation)的列表。

**警告：** 安装可能需要约一小时内完成。

1.  下载并启动最新的 Workbench 安装程序。

    > [!Important]  
    > 将安装程序整个下载到磁盘上，然后在磁盘中运行安装程序。 不要直接从浏览器的下载小组件运行它。<br>**在 Windows:<br>**  。 下载 [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi)。<br>b. 在文件资源管理器中双击下载的安装程序。

1.  请按照屏幕说明完成安装程序中的。

    * * 安装可能需要多达 30 分钟才能完成。 **
    
    `Windows: C:\\Users\\<user>\\AppData\\Local\\AmlWorkbench`
    
    安装程序将下载并设置所有必需的依赖项，例如 Python、 Miniconda 和其他相关库。 此安装还包括 Azure 跨平台命令行工具或 Azure CLI。

1.  启动 Workbench，方法是选择安装程序最后一个屏幕上的“启动 Workbench”按钮。

    如果安装程序已关闭，启动使用**Machine Learning Workbench**桌面快捷方式。

1.  选择**使用 Microsoft 登录**若要使用 Azure Machine Learning Workbench 进行身份验证。 使用 Azure 门户中用于创建试验和模型管理帐户的相同凭据。

    登录后，Workbench 会使用它查找中的 Azure 订阅，并显示所有工作区和项目与该帐户关联的第一个试验帐户。

    > [!Tip]  
    > 切换到另一试验帐户使用 Workbench 应用程序窗口的左下角中的图标。

### <a name="create-a-new-project-in-workbench"></a>在 workbench 中创建一个新项目

1.  打开 Azure Machine Learning Workbench 应用并根据需要登录。

    - 在 Windows 中上, 启动使用**Machine Learning Workbench**桌面快捷方式。

    - 在 macOS 上，请在 Launchpad 中选择“Azure ML Workbench”。

1.  在“项目”窗格中选择加号 (+)，然后选择“新建项目”。

    ![新建工作区](media/azure-stack-solution-machine-learning/image14.png)

1.  填充各窗体字段，然后选择“创建”按钮在 Workbench 中创建新项目。

    | 字段 | 针对教程建议的值 | 说明 |
    |-------------------------------------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 项目名称 | myIris | 输入用于标识帐户的唯一名称。 使用部门或项目最好地标识试验的名称。 名称应介于 2 到 32 个字符之间， 只应包含字母数字字符和短划线 (-) 字符。 |
    | 项目目录 | c:\Temp\ | 指定在其中创建项目的目录。 |
    | 项目说明 | 留空 | 用于描述项目的可选字段。 |
    | Visualstudio.com GIT 存储库 URL | 留空 | 可选字段。 将项目与 Azure 存储库上的 Git 存储库进行源代码管理和协作相关联。 |
    | 所选工作区 | IrisGarden（如果存在） | 选择在 Azure 门户中的试验帐户创建一个工作区。 <br>使用快速入门，列出了此工作区的 IrisGarden 的名称。 否则，使用工作区具有试验帐户的名称或首选的帐户名称。 |
    | 项目模板 | 鸢尾花分类 | 模板包含脚本和用于浏览产品数据。 此模板包含脚本和此文档站点中，对于本快速入门和其他教程所需数据。 |

    ![新建项目](media/azure-stack-solution-machine-learning/image15.png)

1.  此时会创建新的项目，并会打开包含该项目的项目仪表板。 浏览项目主页、 数据源、 笔记本和源代码文件。

    ![打开项目](media/azure-stack-solution-machine-learning/image16.png)

### <a name="attach-a-dsvm-compute-target"></a>附加 DSVM 计算目标

创建 DSVM 后，将其附加到 Azure 机器学习项目。

1.  从在 Azure ML Workbench 应用中，启动选择 Azure ML Workbench CLI**文件**->**打开 PowerShell**

    ![Alt text](media/azure-stack-solution-machine-learning/image17.png)

1.  一旦提示符打开的 PowerShell，请使用以下命令：

    ```PowerShell  
        az login
    ```

1.  将看到以下提示：

     ![Alt text](media/azure-stack-solution-machine-learning/image18.png)

1.  浏览到提示中所述的站点，然后输入提供的代码。

    ![Alt text](media/azure-stack-solution-machine-learning/image19.png)

1.  选择继续出现提示时，然后选择与关联的 Azure 机器学习试验帐户的 Azure 帐户。

    ![Alt text](media/azure-stack-solution-machine-learning/image20.png)

1.  Azure ML Workbench CLI 随后会发送以下提示：

    ![Alt text](media/azure-stack-solution-machine-learning/image21.png)

1.  当机器学习帐户和工作区登录显示为成功时，将附加 DSVM。

    ```PowerShell  
        az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password>
    ```

    将显示以下通知：

    ![Alt text](media/azure-stack-solution-machine-learning/image22.png)

    ```PowerShell  
        # prepare the Docker image on the DSVM 
        az ml experiment prepare -c <compute target name>
    ```

此过程将需要一些时间，并将生成大量的文本，因为它会拉取各种 docker 映像、 注册它们，，然后将应用所需的代码和应用程序到它们。

现在可以在此 DSVM 上运行试验。

### <a name="create-a-data-preparation-package"></a>创建数据准备包

接下来，开始准备 Azure Machine Learning Workbench 中的数据。 在 Workbench 中执行每个转换存储在本地数据准备包中的 JSON 格式 (\*.dprep 文件)。 此数据准备包是在 Workbench 中的数据准备工作的主要容器。

此数据准备包可以传递更高版本运行时，例如本地 C 到\#/CoreCLR、 Scala/Spark 或 Scala/HDI。

1.  选择文件夹图标来打开“文件”视图，然后选择 **iris.csv** 来打开该文件。

    该文件包含一个表，表中有 5 列和 50 行。 四列是数字特征列。 第五列是字符串目标列。 没有任何列有标头名称。

    ![iris.csv](media/azure-stack-solution-machine-learning/image23.png)

1.  在“数据视图”中选择加号 (+) ，添加新的数据源。 此时会打开“添加数据源”页。

    ![Azure Machine Learning Workbench 中的数据视图](media/azure-stack-solution-machine-learning/image24.png)

1.  选择**文本文件 (\*.csv \*.json， \*.txt。，...)**.

    ![在 Azure 机器学习 Workbench 中的数据源](media/azure-stack-solution-machine-learning/image25.png)

1.  选择“**下一步**”。

2.  浏览到的文件**iris.csv**，然后选择**完成**。 这将使用参数（例如分隔符和数据类型）的默认值。

    > [!Important]  
    > 选择**iris.csv**对于此练习的当前项目目录内文件。 否则，后续步骤可能会失败。

    ![选择 iris](media/azure-stack-solution-machine-learning/image26.png)

1.  名为的新文件`*iris-1.dsource`创建。 该文件唯一命名`-1`因为示例项目已附带了没有编号**iris.dsource**文件。

    此时会打开文件并显示数据。 一系列列标题，从**Column1**到**列 5**，会自动添加到此数据集。 滚动到底部，请注意，空数据集的最后一行。 由于 CSV 文件中额外的换行符是空的行。

    ![Iris 数据视图](media/azure-stack-solution-machine-learning/image27.png)

1.  选择“指标”按钮。 此时会生成并显示直方图。

    通过选择切换回数据视图**数据**按钮。

    ![Iris 数据视图](media/azure-stack-solution-machine-learning/image28.png)

1.  观察直方图。 已为每一列计算出一组完整的统计信息。

    ![Iris 数据视图](media/azure-stack-solution-machine-learning/image29.png)

1.  选择“准备”按钮，开始创建数据准备包。 此时会打开“准备”对话框。

    示例项目包含**iris.dprep**默认处于选中状态的数据准备文件。

    ![Iris 数据视图](media/azure-stack-solution-machine-learning/image30.png)

1.  通过选择创建新的数据准备包 **+ 新建数据准备包**菜单中。

    ![Iris 数据视图](media/azure-stack-solution-machine-learning/image31.png)

1.  输入一个新值作为包名称（使用 **iris-1**），然后选择“确定”。

    新的数据准备包名为**iris-1.dprep**创建并在数据准备编辑器中打开。

    ![Iris 数据视图](media/azure-stack-solution-machine-learning/image32.png)

    接下来，需要数据准备。

1.  选择每个列标题，使标题文本可编辑。 然后重命名每个列，如下所示：

    按顺序，输入**花萼长度**，**花萼宽度**，**花瓣长度**，**花瓣宽度**，并**物种**在五个列分别。

    ![将列重命名](media/azure-stack-solution-machine-learning/image33.png)

1.  对非重复值计数：

    1.  选择“物种”列

    2.  右键单击将其选中。

    3.  选择**值计数**菜单中。

        此时会打开数据下面的“检查器”窗格。 将显示包含四个条形的直方图。 目标列包含四个非重复值： **iris-virginica**， **iris-versicolor**，**iris-setosa**，和一个 **(null)** 值。

    ![选择“值计数”](media/azure-stack-solution-machine-learning/image34.png)

    ![值计数直方图](media/azure-stack-solution-machine-learning/image35.png)

1.  若要筛选掉 null 值，请选择“(null)”栏，然后选择减号 (**-**)。

    然后，(null) 行变成灰色，表示它已被筛选掉。

    ![筛选掉 null](media/azure-stack-solution-machine-learning/image36.png)

1.  注意“步骤”窗格中详述的各个数据准备步骤。 在列已重命名和筛选 null 值行，每个操作是作为数据准备步骤记录。 编辑各个步骤以调整其设置、 为步骤重新排序以及删除步骤。

    ![Alt text](media/azure-stack-solution-machine-learning/image37.png)

1.  关闭数据准备编辑器。 选择带图标的“iris-1”选项卡上的 x 图标，将该选项卡关闭。工作会自动保存到**iris-1.dprep**文件中所示**数据准备**标题。

    ![关闭](media/azure-stack-solution-machine-learning/image38.png)

### <a name="generate-python-code-to-invoke-a-data-preparation-package"></a>生成可调用数据准备包的 Python 代码

可以直接在 Python 或 Jupyter Notebook 中浏览数据准备包的输出。 可以跨多个运行时执行包，包括本地 Python、Spark（包含在 Docker 中），以及 HDInsight。

1.  在“数据准备”选项卡下找到 **iris-1.dprep** 文件。

2.  右键单击 **iris-1.dprep** 文件，然后从上下文菜单中选择“生成数据访问代码文件”。

    ![生成代码](media/azure-stack-solution-machine-learning/image39.png)

    名为的新文件**iris 1.py**打开包含以下用于调用以数据准备包形式创建的逻辑的代码行：

    ```Python
    # Use the Azure Machine Learning data preparation package
    from azureml.dataprep import package

    # Use the Azure Machine Learning data collector to log various metrics
    from azureml.logging import get_azureml_logger
    logger = get_azureml_logger()

    # This call will load the referenced package and return a DataFrame.
    # If run in a PySpark environment, this call returns a
    # Spark DataFrame. If not, it will return a Pandas DataFrame.
    df = package.run('iris-1.dprep', dataflow_idx=0)
    # Remove this line and add code that uses the DataFrame
    df.head(10)
    ```

    根据在其中运行此代码的上下文，drep 表示一种不同的数据帧：

    -  在 Python 运行时中执行时，使用 [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)。

    -  在 Spark 上下文中执行时，使用 [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html)。

### <a name="review-irissklearnpy-and-the-configuration-files"></a>查看 iris_sklearn.py 和配置文件

1.  在打开的项目中，选择**文件**上最左侧窗格中，打开项目文件夹中的文件列表的按钮 （文件夹图标）。

    ![打开 Azure Machine Learning Workbench 项目](media/azure-stack-solution-machine-learning/image40.png)

1.  选择 **iris_sklearn.py** Python 脚本文件。

    ![选择脚本](media/azure-stack-solution-machine-learning/image41.png)

    代码随即会在 Workbench 中的新文本编辑器选项卡中打开。

    > [!Note]  
    > 显示的代码可能不是完全与前面的代码相同因为此示例项目经常更新。

    ![打开文件](media/azure-stack-solution-machine-learning/image42.png)

1.  检查 Python 脚本代码，以便熟悉编码样式。

    脚本 **iris_sklearn.py** 执行以下任务：  

    -  加载默认数据准备包 iris.dprep 以创建 [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)。

    -   添加随机特征，使问题更难以解决。 随机性是必要的，因为 Iris 是一个小型数据集，能够以接近 100% 的准确率轻松分类。

    -  使用 thescikit learnmachine 学习库生成逻辑回归模型。 此库是 Azure Machine Learning Workbench 默认随附的。

    -  将 [pickle](https://docs.python.org/3/library/pickle.html) 库插入 `outputs` 文件夹中的某个文件，使模型序列化。

    -  加载序列化模型，然后将其反序列化回内存中。

    -  使用反序列化的模型基于新记录进行预测。

    -  绘制两个图，一个混淆矩阵，一个多类接收者操作特性 (ROC) 曲线，请使用[matplotlib](https://matplotlib.org/)库，然后将其保存在 theoutputsfolder 中。 如果不明显，请在环境中安装此库。

    -  自动绘制正则化率和模型准确性的运行历史记录中。 始终使用 `run_logger` 对象将正则化率和模型准确性记录到日志中。

### <a name="run-irissklearnpy-in-the-local-environment"></a>在本地环境中运行 iris_sklearn.py

1.  启动 Azure 机器学习命令行界面 (CLI)：

    1.  启动 Azure Machine Learning Workbench。

    2.  在“Workbench”菜单中，选择“文件”> “打开命令提示符”。

    在项目文件夹中启动 Azure 机器学习命令行接口 (CLI) 窗口`C:\Temp\\myIris\`在 Windows 上。 此项目是一个在本教程的第 1 部分中创建相同。

    > [!Important]  
    > 使用此 CLI 窗口来完成后续步骤。

1.  在 CLI 窗口中，安装 Python 绘制库**matplotlib**，如果尚未安装。

    **iris_sklearn.py** 脚本的依赖项依赖于两个 Python 包：**scikit-learn** 和 **matplotlib**。 **Scikit-了解**包通过 Azure Machine Learning Workbench 安装为方便起见。 尽管如此，安装**matplotlib**可能要求。

    如果继续操作而无需安装**matplotlib**，仍可成功运行此教程中的代码。 但是，代码将无法生成历史记录视觉对象中所示的混淆矩阵输出和多类 ROC 曲线绘图。

    ```CLI
    pip install matplotlib
    python -m pip install --upgrade pip
    ```

    此安装约需一分钟。

1.  返回到 Workbench 应用程序。

2.  找到名为 **iris_sklearn.py** 的选项卡。

    ![通过脚本查找选项卡](media/azure-stack-solution-machine-learning/image43.png)

1.  在该选项卡的工具栏中，选择**本地**作为执行环境，andiris_sklearn.pyas 要运行的脚本。 这些可能已被选中了。

    ![Alt text](media/azure-stack-solution-machine-learning/image44.png)

1.  将移动到右侧的工具栏和 enter0.01in**自变量**字段。

    此值对应于逻辑回归模型的正则化率。

    ![本地和脚本选择](media/azure-stack-solution-machine-learning/image45.png)

1.  选择“运行”按钮。 随后会立即计划一个作业。 该作业列在 Workbench 窗口右侧的“作业”窗格中。

    ![本地和脚本选择](media/azure-stack-solution-machine-learning/image46.png)

    从作业的状态转换几分钟后**正在提交**给**运行**，最后到**已完成**。

1.  在“作业”窗格的作业状态文本中选择“已完成”。

    ![运行 sklearn](media/azure-stack-solution-machine-learning/image47.png)

    弹出窗口将打开并显示运行的标准输出 (stdout) 文本。 若要关闭 stdout 文本，请选择**关闭**(**x**) 上的弹出窗口右上角的按钮。

    ![标准输出](media/azure-stack-solution-machine-learning/image48.png)

1.  在中的同一作业状态**作业**窗格中，选择蓝色文本 **iris_sklearn.py \[n\] **(* n * 为运行的编号) 正上方**完成**状态和开始时间。 此时会打开“运行属性”窗口，显示该特定运行的以下信息：

    -  运行属性信息

    -  **输出**

    -  **指标**

    -  可视化效果（如果有）

    -  **日志**

    完成运行后，弹出窗口会显示以下结果：

    > [!Note]  
    > 因为本教程引入了某种随机化前面在训练集中，确切的结果可能不同于此处显示的结果。

    ```Python  
        Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]

        Iris dataset shape: (150, 5)
        Regularization rate is 0.01
        LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
        Accuracy is 0.6792452830188679

        ==========================================
        Serialize and deserialize using the outputs folder.

        Export the model to model.pkl
        Import the model from model.pkl
        New sample: [[3.0, 3.6, 1.3, 0.25]]
        Predicted class is ['Iris-setosa']
        Plotting confusion matrix...
        Confusion matrix in text:
        [[50  0  0]
        [ 1 37 12]
        [ 0  4 46]]
        Confusion matrix plotted.
        Plotting ROC curve....

        ROC curve plotted.
        Confusion matrix and ROC curve plotted. See them in Run History details pane.

    ```

1.  关闭“运行属性”选项卡，然后返回到“iris_sklearn.py”选项卡。

1.  重复操作即可完成额外的运行。

在“参数”字段中输入从 `0.001` 到 `10` 的一系列值。 选择“运行”，再执行几次代码。 每次更改的参数值提供给逻辑回归模型在代码中，每次产生不同的结果。

### <a name="review-the-run-history-in-detail"></a>查看详细的运行历史记录

在 Azure Machine Learning Workbench 中每个脚本执行被捕获为一条运行的历史记录。 通过打开查看特定脚本的运行历史记录**运行**视图。

1.  若要打开“运行”列表，请选择左侧工具栏上的“运行”按钮（时钟图标）。 然后选择**iris_sklearn.py**以显示**运行仪表板**ofiris_sklearn.py。

    ![“运行”视图](media/azure-stack-solution-machine-learning/image49.png)

1.  此时会打开“运行仪表板”选项卡。

    查看在多个运行中捕获的统计信息。 图形呈现在选项卡的顶部。每个运行都有一个连续的数值，并在屏幕底部的表中列出了运行详细信息。

    ![“运行”仪表板](media/azure-stack-solution-machine-learning/image50.png)

1.  筛选该表，然后选择任意图形，查看每个运行的状态、持续时间、准确性和正则化率。

2.  选择“运行”表中两个或两个以上运行旁边的复选框。 选择“比较”按钮，打开详细的比较窗格。 查看并列比较。

3.  若要返回到“运行仪表板”，请选择“比较”窗格左上角的“运行列表”后退按钮。

    ![返回到“运行”列表](media/azure-stack-solution-machine-learning/image51.png)

1.  选择单个运行，查看运行详细信息视图。 请注意，所选运行的统计信息列在“运行属性”部分。 写入到输出文件夹中的文件中列出**输出**部分，并从该处下载文件。

    ![运行详细信息](media/azure-stack-solution-machine-learning/image52.png)

“视觉对象”部分呈现了两个绘图：混淆矩阵和多类 ROC 曲线。 还可以在“日志”部分中找到所有日志文件。

### <a name="run-scripts-in-the-azure-machine-learning-ml-workbench-cli-window"></a>在 Azure 机器学习 (ML) Workbench CLI 窗口中运行脚本

1.  启动 Azure 机器学习命令行界面 (CLI)：

    1.  启动 Azure Machine Learning Workbench。

    2.  在“Workbench”菜单中，选择“文件” > “打开命令提示符”。

    项目文件夹中的在 CLI 提示符启动`C:\\Temp\\myIris`在 Windows 上。 这是在本教程的第 1 部分中创建的项目。

    > [!Important]  
    > 使用此 CLI 窗口来完成后续步骤。

1.  在 CLI 窗口中登录到 Azure。 [详细了解 az 登录](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

    如果尚未登录，请跳过此步骤。

1.  在命令提示符处，输入：

    ```CLI
        az login
    ```

    此命令返回的代码可以在浏览器中使用[ https://aka.ms/devicelogin ](https://aka.ms/devicelogin)。

1.  转到[ https://aka.ms/devicelogin ](https://aka.ms/devicelogin)在浏览器中。 输入从接收到 CLI 中收到的代码。

    针对 Azure 资源进行身份验证时，Workbench 应用和 CLI 使用独立的凭据缓存。 身份验证不需要再次缓存的令牌过期之前。

1.  如果组织具有多个 Azure 订阅 （企业环境），设置要使用的订阅。 找到的订阅、 使用订阅 ID，将其设置，然后对其进行测试。

1.  列出每个访问的 Azure 订阅，使用以下命令：

    ```CLI
        az account list -o table 
    ```

    **Az 帐户列表**命令将返回到登录名可用的订阅列表。 如果有多个关系，标识使用的订阅的订阅 ID 值。

1.  设置用作默认帐户的 Azure 订阅：

    ```CLI
        az account set -s <the-subscription-id
    ```

    其中 <--i d > 是使用的订阅的 ID 值。 请勿包括大括号。

1.  通过请求当前订阅的详细信息来确认新的订阅设置。

    ```CLI
        az account show
    ```

1.  在 CLI 窗口中，将 **iris_sklearn.py** 脚本作为试验提交。

    执行 iris_sklearn.py 是对本地计算上下文运行。

1.  在 Windows 上：

    ```CLI
        az ml experiment submit -c local .\\iris_sklearn.py
    ```

1.  在 macOS 上：

    ```CLI
        az ml experiment submit -c local iris_sklearn.py
    ```

    输出应类似于： 

    ```
        RunId: myIris_1521077190506

    Executing user inputs .....
    ===========================

    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]

    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679

    ==========================================
    Serialize and deserialize using the outputs folder.

    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.

    Execution Details
    =================
    RunId: myIris_1521077190506

    ```

6.  查看输出。 Workbench 运行脚本时，所有应为相同的输出和与的结果。

7.  回到 Workbench，然后执行以下操作：

    选择左侧窗格中的文件夹图标，列出项目文件。  打开名为 **run.py** 的 Python 脚本。 此脚本可用于循环通过各种正则化率。 

    ![返回运行列表](media/azure-stack-solution-machine-learning/image53.png)

1.  使用这些正则化率多次运行试验。

    此脚本首先` aniris_sklearn.pyjob`与正则化率 o `10.0` （数字过大）。 该脚本然后剪切速率到一半人员位于以下运行，，依此类推，直到速率不小于`0.005`。 脚本包含以下代码：

    ![返回到“运行”列表](media/azure-stack-solution-machine-learning/image54.png)

1.  从命令行运行 **run.py** 脚本，如下所示：

    ```CLI
        python run.py
    ```
此命令将提交`iris_sklearn.py`多次使用不同的正则化率

当`run.py`完成之后，不同指标的图形显示在 workbench 中的运行历史记录列表视图中。

### <a name="run-scripts-in-an-ubuntu-based-data-science-virtual-machine-dsvm-on-azure"></a>在 Azure 上运行的脚本中将基于 Ubuntu 的数据科学虚拟机 (DSVM)

若要在远程 Linux 计算机上的 Docker 容器中执行该脚本，SSH 访问 （用户名和密码） 是运行需要该远程计算机。 此外，该计算机必须已安装并运行 Docker 引擎。

1.  编辑生成的<your DSVM Name>.runconfigfile underaml_configand 将框架从默认 valuePySparktoPython 更改：

    ```yaml  
    Framework: Python
    ```
1.  发出相同的命令像以前一样在 CLI 窗口中，使用目标*<DSVM>* 此时若要在远程 Docker 容器中执行 iris_sklearn.py: (替换<DSVM>数据科学 VM 名称，不使用括号)。

    ```CLI
        az ml experiment submit -c <DSVM> iris_sklearn.py
    ```

该命令执行为如同采用 adocker pythonenvironment，只会在远程 Linux VM 上执行。 CLI 窗口显示相同的输出信息。

### <a name="download-the-model-pickle-file"></a>下载模型 pickle 文件

在本教程的前一部分，已在 Machine Learning Workbench 中以本地方式运行了 iris_sklearn.py 脚本。 该操作已使用流行的 Python 对象序列化包 [pickle](https://docs.python.org/3/library/pickle.html) 序列化了逻辑回归模型。

1.  打开 Machine Learning Workbench 应用程序， 然后打开**myIris**在系列教程的前面部分中创建项目。

2.  打开项目后，选择**文件**按钮 （文件夹图标） 左侧的窗格中，打开项目文件夹中的文件列表。

3.  选择 iris_sklearn.py 文件。 Python 代码随即会在 Workbench 中的新文本编辑器选项卡中打开。

4.  查看 **iris_sklearn.py** 文件，确定在哪个位置生成了 pickle 文件。 选择 Ctrl+F 打开“查找”对话框，然后在 Python 代码中找到 pickle 一词。

此代码片段演示 pickle 输出文件是如何生成的。 输出 pickle 文件在磁盘上命名为 model.pkl。

    ```Python
        print("Export the model to model.pkl")
        f = open('./outputs/model.pkl', 'wb')
        pickle.dump(clf1, f)
        f.close()

    ```

1.  在前一运行的输出文件中找到模型 pickle 文件。

    当**iris_sklearn.py**运行脚本时，模型文件写入到**输出**文件夹中的，名称**model.pkl**。 此文件夹驻留在执行环境中选择运行该脚本，而不是在本地项目文件夹。 

    1. 若要找到文件，请选择**运行**按钮 （时钟图标） 左侧的窗格中，打开列表**所有运行**。  

    2. 此时会打开“所有运行”选项卡。 在运行表中，选择一个目标为的最近运行**本地**且脚本名称为**iris_sklearn.py**。  

    3. 此时会打开“运行属性”窗格。 在窗格的右上部分中，请注意**输出**部分。 有。 若要下载 pickle 文件，选中的复选框旁边**model.pkl**文件，并选择**下载**。 将文件保存到项目文件夹的根目录。 后续步骤中需要该文件。  

    ![下载 pickle 文件](media/azure-stack-solution-machine-learning/image55.png)

### <a name="get-scoring-script-and-schema-files"></a>获取评分脚本和架构文件

若要部署 web 服务和模型文件，评分脚本需要。 （可选） 需要 web 服务输入数据的架构。 评分脚本从当前文件夹加载 **model.pkl** 文件，并使用它来生成新预测。

1.  打开 Machine Learning Workbench 应用程序， 然后打开**myIris**的系列教程前一部分中创建项目。

2.  打开项目后，选择**文件**按钮 （文件夹图标） 左侧的窗格中，打开项目文件夹中的文件列表。

3.  选择 score_iris.py 文件。 此时会打开 Python 脚本。 此文件用作评分文件。

    ![评分文件](media/azure-stack-solution-machine-learning/image56.png)

1.  若要获取架构文件，请运行该脚本。 在命令栏中选择 local 环境和 score_iris.py 脚本，然后选择“运行”。

    此脚本创建的 JSON 文件中**输出**部分中，它将捕获模型所需的输入的数据架构。

1.  请注意“项目仪表板”窗格右侧的“作业”窗格。 等待最新版本 * * score_iris.py** 作业显示绿色**已完成**状态。 然后选择最新作业运行对应的超链接 **score_iris.py**，查看运行详细信息。

2.  在“运行属性”窗格的“输出”部分，选择新建的 service_schema.json 文件。 选中文件名旁边的复选框，然后选择“下载”。 将文件保存到项目根文件夹。

3.  返回到上一个选项卡并**score_iris.py**脚本。 通过使用数据收集，可以捕获模型输入和预测 web 服务。 以下步骤感兴趣的数据收集。

4.  查看文件导入类的顶部的代码**ModelDataCollector**。 它包含模型数据收集功能：

    ```Python  
        from azureml.datacollector import ModelDataCollector
    ```

1.  查看 init() 函数中用于实例化 ModelDataCollector 的以下代码行：

    ```Python  
        global inputs_dc, prediction_dc
        inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
        prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1.  查看 run(input_df) 函数中的以下代码行，因为这些行用于收集输入和预测数据：

    ```Python  
        inputs_dc.collect(input_df)
        prediction_dc.collect(pred)
    ```

现在，准备环境来操作化模型。

## <a name="step-5-deploy-and-use-azure-container-registry"></a>步骤 5： 部署和使用 Azure 容器注册表

部署和使用 Azure 容器注册表。

使用 **az acr create** 命令创建 Azure 容器注册表。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 资源组是相同的。

    ```CLI
        az acr create --resource-group <ResourceGroup> --name  <acrName> --sku Basic
    ```

### <a name="container-registry-login"></a>容器注册表登录

运行 **az acr login** 命令，登录 ACR 实例。 请提供创建容器注册表时所使用的唯一名称。

    ```CLI
        az acr login --name <acrName>
    ```

该命令将返回登录成功的消息一次完成。

### <a name="prepare-to-operationalize-locally-for-development-and-testing-the-service"></a>本地准备操作化用于开发和测试服务

使用*本地模式下*部署在本地计算机上和用于开发和测试在 Docker 容器中运行。

若要完成以下步骤来操作化模型，必须在本地运行 Docker 引擎。 使用`-h`末尾的每个命令显示相应的帮助消息的标志。

    > [!Note]  
    > If Docker engine is not locally available, proceed by creating a cluster in Azure for deployment and keep the cluster for re-use, or delete it after the tutorial to avoid ongoing charges.

    > [!Note]  
    > Web services deployed locally do not appear in Azure Portal's list of services. They will be running in Docker on the local machine.

1.  打开命令行界面 (CLI)。 在 Machine Learning Workbench 应用程序的“文件”菜单上，选择“打开命令提示符”。

    命令行提示符将在当前项目文件夹位置中打开**c:\\temp\\myIris**。

1.  请确保 Azure 资源提供程序**Microsoft.ContainerRegistry**在订阅中注册。 在步骤 3 中创建环境之前注册此资源提供程序。 检查是否已注册使用以下命令：

    ```CLI
        az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
    ```

    查看此输出：

    ```CLI
        Provider                                    Status 
        --------                                    --------
        Microsoft.Authorization                     Registered 
        Microsoft.ContainerRegistry                 Registered 
        microsoft.insights                          Registered 
        Microsoft.MachineLearningExperimentation    Registered 
    ```

    如果**Microsoft.ContainerRegistry**是未注册的使用以下命令：

    ```CLI
    az provider register --namespace Microsoft.ContainerRegistry
    ```

    注册可能需要几分钟时间。 使用以前的注册状态检查**az 提供程序列表**命令或以下命令：

    ```CLI
    az provider show -n Microsoft.ContainerRegistry
    ```

    第三行输出显示 **"registrationState": "Registering"**。 稍候片刻并重复**显示**命令，直到输出显示 **"registrationState":"注册。**

1.  创建环境。 运行此步骤一次每个环境。

    以下设置命令要求对订阅的参与者访问权限。 对部署到的资源组参与者访问权限是必需的。 使用 gflag 作为安装程序命令的一部分指定的资源组名称。

    ```CLI
    az ml env setup -n <new deployment environment name> --location <e.g. eastus2> -g <existing resource group name>
    ```

    遵照屏幕说明预配一个用于存储 Docker 映像的存储帐户、一个用于列出 Docker 映像的 Azure 容器注册表，以及一个用于收集遥测数据的 Azure Application Insights 帐户。 **如果使用 cswitch，命令会额外创建容器服务群集**。

    群集名称是用于标识在环境的方法。 位置应与 Azure 门户中创建的模型管理帐户的位置相同。

    为了确保环境设置成功，请使用以下命令来检查状态：

    ```CLI
    az ml env show -n <deployment environment name> -g <existing resource group name>
    ```

    确保"Provisioning State"具有值"Succeeded"，如所示，在设置步骤 5 中的环境之前：

    ![预配状态](media/azure-stack-solution-machine-learning/image57.png)

1.  设置环境。

    完成设置后，使用以下命令设置操作化环境时所需的环境变量。 使用前面在步骤 3 中用的同一环境名称。 使用完成设置过程时在命令窗口中输出的同一资源组名称。

    ```CLI
        az ml env set -n <deployment environment name> -g <existing resource group name>
    ```

1.  若要验证的本地 web 服务部署的操作化环境的正确配置，请输入以下命令：

    ```CLI
    az ml env show
    ```

    现在，创建实时 web 服务。

    > [!Note]  
    > 重复使用模型管理帐户和环境进行后续的 web 服务部署。 没有无需为每个 web 服务创建它们。 帐户或环境可以有多个与之关联的 Web 服务。

### <a name="create-a-real-time-web-service-by-using-separate-commands"></a>使用单独的命令创建实时 Web 服务

作为一种替代方法**az ml 服务创建实时**前面所示，也可以单独执行这些步骤的命令。

先注册模型， 再生成清单，然后生成 Docker 映像，最后创建 Web 服务。 此循序渐进的方法能够在每个步骤的更大的灵活性。 此外，可能会重复使用在前面的步骤中生成的实体。

1. 通过提供 pickle 文件名注册模型。

    ```CLI
    az ml model register --model model.pkl --name model.pkl
    ```

    此命令生成模型 ID。

2.  创建清单。

    若要创建清单，请使用以下命令并提供前一步骤中生成的模型 ID 输出： 
    
    ```CLI
    az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
    ```

    此命令生成清单 ID。

3.  创建 Docker 图像。

    若要创建 Docker 映像，请使用以下命令并提供前一步骤中的清单 ID 值输出。 Conda 依赖项也可以通过使用`-c`切换。 
    
    ```CLI
    az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
    ```
    
    此命令生成 Docker 映像 ID。

2.  创建服务。

    若要创建的服务，使用以下命令并提供前一步骤中的映像 ID 输出： 
    
    ```CLI
    az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
    ```
    
    此命令生成 Web 服务 ID。
    
    接下来，运行 web 服务。

## <a name="step-6-deploy-a-kubernetes-cluster-to-azure-stack"></a>步骤 6： 将 Kubernetes 群集部署到 Azure Stack

将 Kubernetes 群集部署到 Azure Stack。

可以使用 Azure 资源管理器模板生成的 Azure Stack 上的 Azure 容器服务 (ACS) 引擎安装 Kubernetes。 [*Kubernetes* ](https://kubernetes.io/)是一个开放源代码系统用于自动部署、 缩放和管理容器中的应用程序。 一个[*容器*](https://www.docker.com/what-container)包含图像，类似于 VM 中。 与不同的 VM，容器映像包括运行之类的代码，运行时执行代码、 特定的库和设置的应用程序所需的资源。

使用 Kubernetes 到：

 -  开发可以大规模伸缩、升级并可快速部署的应用程序。

 -  简化应用程序的设计，并通过不同的 Helm 应用程序来提高其可靠性。 [*Helm* ](https://github.com/kubernetes/helm)是一种开放源打包工具，可帮助安装和管理 Kubernetes 应用程序的生命周期。

 -  轻松地监视和诊断应用程序与缩放的运行状况和升级功能。

> [!Note]  
> 安装群集大约需要一小时，请计划适当。

### <a name="prerequisites-for-kubernetes-cluster-deployment"></a>部署 Kubernetes 群集的先决条件

若要开始，请确认权限和 Azure Stack 就绪：

1.  验证**创建 Kubernetes 群集 （预览版）** 项是在 Azure Stack Marketplace 中可用。 如果此项为缺失，适用于 Azure Stack 操作员将此项添加到 Azure Stack 环境。

2.  验证能够在 Azure Active Directory (Azure AD) 租户中创建应用程序。 权限是 Kubernetes 部署所必需的。

    正在检查权限的说明，请参阅[*检查 Azure Active Directory 权限*](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)。

3.  生成一个 SSH 公钥和私钥对，用于登录到 Azure Stack 上的 Linux VM。 创建群集时需要的公钥。 有关说明请参阅[生成用于 SSH 身份验证密钥](#generate-an-authenticatio-key-for-ssh)。

4.  检查 Azure Stack 租户门户中的订阅有效，并且有足够的公共 IP 地址来添加新的应用程序。

    无法将群集部署到 Azure Stack**管理员**订阅。 使用**用户**订阅。

###  <a name="generate-an-authentication-key-for-ssh"></a>生成用于 SSH 身份验证密钥

从 Linux 会话的 Windows 子系统中使用以下命令生成身份验证密钥： 

1. 键入：

    ```Bash  
    ssh-keygen -t rsa
    ```
    
2. 选择`id_rsa`出现提示时。 
3. 创建一个通行短语，出现提示时。 请务必注意此密码以供将来使用。 
    输出将类似于下面： 
    
    ```Bash  
    Your identification has been saved in `id_rsa`.
    Your public key has been saved in `id_rsa.pub`. 
    The key fingerprint is: SHA256:lUtUUjzaqWqGeolEPKeBmsnrhcNGM9Dn2OxYatt05SE  <user>@<machine-name>
    The key's randomart image is:  
    ```
    ![Alt text](media/azure-stack-solution-machine-learning/image58.png)

4. 生成密钥之后, 将粘贴在密钥信息使用以下命令： 
    ```Bash
    cat id_rsa.pub
    ```
    输出将类似于下面： 
    ```Bash
    ssh-rsa  AAAAB3NzaC1yc2EAAAADAQABAAABAQDHaWrAktR3BlQ356T8Qvv8O2Q/U/hsXQwS9xJbuduuc9lkJwddzgmNCyM9PooZWYtGVXyHU6SC3YH1XkwqGtKhtPb03d24hmhX1euAaqIqHHSp4WgUS5s1gNsp37L8QCSGNCnF31FWavI8bnjOjccUkMowKl8iyGN++5UyQgNuynEVUbFJjrntoDL66HUu88xDxTcVB7rqDr2QKFwYJkg4HSoHYpezJd7W8kcmv33eh0xs8nlDA7Dzu7zXpyVc54bH9XtPR6EUXaQa+UqKaNlQNiJqEs+1X/zNuK9V6NLVNiO0h3jCHI3K8o4VnZyRKHCQProasiiPLUUJ6SF/RTLN  <user>@<machine-name>
    ```
    
5. 将生成的密钥复制到 SSH 公共密钥字段。

### <a name="create-a-service-principal-in-azure-ad"></a>在 Azure AD 中创建服务主体

1.  登录到全局[ *Azure 门户*](http://portal.azure.com/)。

2.  使用与 Azure Stack 实例相关联的 Azure AD 租户登录。

3.  创建 Azure AD 应用程序。

    1. 选择**Azure Active Directory** > **+ 应用注册**> **新建应用程序注册**。
    2. 输入应用程序的“名称”。 
    3. 选择“Web 应用/API”。 
    4. 输入 `http://localhost` 作为“登录 URL”。 
    5. 选择“创建”。

1.  请记下**应用程序 ID**。 创建群集时需要此 ID，作为引用**服务主体客户端 ID**。

2.  选择“设置” > “密钥”。

    1. 输入“说明”。 
    2. 对于“过期”这一项，请选择“永不过期”。 
    3. 选择“保存”。 记下密钥字符串。 创建群集时需要键的字符串，作为引用**服务主体客户端机密**。

### <a name="give-the-service-principal-access"></a>为服务主体提供访问权限

服务主体访问权限向订阅，因此它可能会造成资源。

1.  登录到[管理门户](https://adminportal.local.azurestack.external/)。

2.  选择**更多服务**> * * 用户订阅 * * > **+ 添加**。

3.  选择创建的订阅。

4.  选择“访问控制(IAM)”，然后选择“+ 添加”。

5.  选择“所有者”角色。

6.  选择创建服务主体的应用程序名称。 如果需要请在搜索框中键入名称。

7.  选择“保存”。

8.  打开 [Azure Stack 门户](https://portal.local.azurestack.external)。

9.  选择“+新建” > “计算” > “Kubernetes 群集”。 选择“创建”。

    ![部署解决方案模板](media/azure-stack-solution-machine-learning/image59.png)

10\。 在“创建 Kubernetes 群集”中选择“基本设置”。

    ![Deploy Solution Template](media/azure-stack-solution-machine-learning/image60.png)

11. 输入 **Linux VM 管理员用户名**。 构成 Kubernetes 群集和 DVM 的 Linux 虚拟机的用户名。

12. 输入 **SSH 公钥**，用于向所有作为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机授权。

13. 输入特定于区域的**主配置文件 DNS 前缀**。 这必须是区域唯一命名，例如`ask8s-12345`。 请尝试选择它作为资源组同名，作为最佳做法。

    > [!Note]  
    > 为每个群集使用新且唯一的主配置文件 DNS 前缀。

14. 输入**代理池配置文件计数**。 此计数包含群集中的代理数。 其范围为 1 到 4。

15. 输入**服务主体 ClientId**，供 Kubernetes Azure 云提供程序使用。

16. 输入**服务主体客户端机密**创建服务主体应用程序时创建。

17. 输入 **Kubernetes Azure 云提供程序版本**。 这是 Kubernetes Azure 提供程序的版本。 Azure Stack 为每个 Azure Stack 版本发布了自定义的 Kubernetes 内部版本。

18. 选择**订阅**id。

19. 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。

20. 选择资源组的“位置”。 这是为 Azure Stack 安装选择的区域。

### <a name="specify-the-azure-stack-settings"></a>指定 Azure Stack 设置

1.  选择“Azure Stack 戳设置”。

    ![部署解决方案模板](media/azure-stack-solution-machine-learning/image61.png)

2.  输入**租户 Azure 资源管理器终结点**。 这是在连接后即可为 Kubernetes 群集创建资源组的 Azure 资源管理器终结点。 对于集成系统需要从 Azure Stack 操作员的终结点。 为 Azure Stack 开发工具包 (ASDK)，使用`https://management.local.azurestack.external`。

3.  输入租户的**租户 ID**。 请参阅[*获取租户 ID* ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)查找租户 ID 值。

### <a name="install-kubectl-on-windows-powershell-environment"></a>在 Windows PowerShell 环境中安装 kubectl

从 WSL 环境中运行以下命令在 WSL 环境中安装 kubectl。

```PowerShell  
Install-script -name install-kubectl -scope CurrentUser -force
Install-kubectl.ps1 -downloadlocation “C:\Users\<Current User>\Documents\Kube
```

### <a name="install-kubectl-on-the-windows-subsystem-for-linux-environment"></a>安装 kubectl 的 Windows 子系统上的 Linux 环境

从在 WSL 环境中运行以下命令在 WSL 环境中安装 kubectl。

```Bash  
    apt-get update && apt-get install -y apt-transport-https
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add
    sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
    apt-get update
    apt-get install -y kubectl
```

### <a name="configure-kubectl"></a>配置 kubectl

Kubectl 以查找并访问 Kubernetes 群集，以便*kubeconfig 文件*需要。 这是使用 kube up.sh 或者部署在 Minikube 群集创建群集时自动创建的。 请参阅[*入门指南*](https://kubernetes.io/docs/setup/)有关创建群集的详细信息。 访问其他用户创建的群集，请参阅[*共享群集访问文档*](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)。 默认情况下，kubectl 配置位于 **~.kube/config**。

### <a name="check-the-kubectl-configuration"></a>检查 kubectl 配置

检查该 kubectl 已正确配置通过获取群集状态：

```Bash  
kubectl cluster-info
```

正确配置 kubectl 以显示 url 响应时访问群集。

kubectl 未正确配置或无法连接到 Kubernetes 群集，如果将显示以下消息：

```Bash  
The connection to the server <server-name:port> was refused -  did you specify the right host or port?
```

例如，当本地便携式计算机上运行的 Kubernetes 群集，一种工具可能需要 (minikube 或类似) 来重新运行上述命令。

如果 kubectl 群集信息返回 url 响应，但群集是仍未正确配置的可访问，检查使用：

```Bash  
> kubectl cluster-info dump
```

### <a name="enable-shell-autocompletion"></a>启用命令行程序自动完成功能

kubectl 包括自动完成功能的支持，使 shell 启用快速、 轻松。

完成脚本本身由 kubectl 生成，可从配置文件进行访问。

### <a name="connect-to-the-kubernetes-cluster"></a>连接到 Kubernetes 群集

若要连接到群集，Kubernetes 命令行客户端 (**kubectl**) 需要。 有关连接到和管理群集的说明位于[Azure 容器服务文档。](https://docs.microsoft.com/azure/container-service/dcos-swarm/)

可用于任何 SSH 客户端连接的 Kubernetes 群集。 建议使用 Windows 子系统用于 Linux (WSL)。 连接到 Kubernetes 群集的计算机将为群集创建的资源组中，将启动 vmd 边缘 ml 堆栈的前缀。

```Bash  
ssh <user>@vmd-edge-ml-stack.<FQDN of Kubernetes Machine in  Azure Stack>
```

连接到 Kubernetes 计算机后，运行以下计算机获取 Kubernetes 配置文件。

```Bash  
sudo find / -name \*kubeconfig\* -type f
```

输出将如下所示：

```Bash  
/var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

复制此文件路径信息，然后运行以下命令中，粘贴从前面复制的 kubeconfig 文件路径中：

```Bash  
sudo cat  /var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

输出将是文本的大块，这是文本的原始的 JSON 内容。 复制此输出文本，然后将此代码粘贴到 Visual Studio 文件中，将另存为 JSON 文件。 这会导致本地存储的 kubeconfig.json 文件。 (保存到/mntc/用户/<current user>kubeconfig.json 的 / / Kube 目录)

### <a name="configure-the-kubernetes-cluster"></a>Kubernetes 群集配置

一旦获得本地 JSON 文件，则在新的 WSL 会话中，使用以下命令来配置群集。

```Bash  
    cd /mnt/c/users/<current user>/documents/Kube
    kubectl proxy
    kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
    kubectl proxy
    set KUBECONFIG=”/mnt/c/users/<current user>/documents/Kube/kubeconfig.json”
    kubectl.exe config view
```

（请参阅下面的输出），将定义 Kubernetes 配置设置。

![Alt text](media/azure-stack-solution-machine-learning/image62.png)

启动本地代理服务：

```Bash  
kubectl proxy
```

浏览到 kubernetes 群集 UI 在以下地址： `https://localhost:8001`。

![Alt text](media/azure-stack-solution-machine-learning/image63.png)

现在，已部署容器，并位于云中，您可以看到在本地的容器的位置。

![Alt text](media/azure-stack-solution-machine-learning/image64.png)

自定义**iris_deployment.yaml**文件 (位于 /*mnt/c/用户/<current user>/文档/Kube 目录*) 以便**webservicename**和容器**图像**并**名称**匹配部署，使用选择的任何代码编辑器。

![Alt text](media/azure-stack-solution-machine-learning/image65.png)

将容器端口设置为**5001。**

![Alt text](media/azure-stack-solution-machine-learning/image66.png)

然后创建**imagePullSecret**:

### <a name="create-a-secret-in-the-cluster-that-holds-the-authorization-token"></a>包含授权令牌在群集中创建机密

Kubernetes 群集使用的机密**docker 注册表**要使用容器注册表拉取专用映像进行身份验证类型。

创建此密钥，其命名为**azuremlcr**:

```Bash  
kubectl create secret docker-registry azuremlcr --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password= "<your-pword>" --docker-email=<your-email>
```

查找：

- **< 你注册表服务器 >** 是 Azure 容器注册表**登录服务器**。
- **< 你的名称 >** 是 Azure 容器注册表**用户名**。
- **< 你 pword >** 是 Azure 容器注册表**密码**。 请确保该密码是用引号引起来。
- **< 你的电子邮件 >** 是具有到容器的读/写访问权限的用户。
- 在上找到此信息**Azure 容器****注册表**下**访问密钥**。
- Docker 凭据现在都在群集中设置机密的称谓**azuremlcr**。

保存**iris_deployment.yaml**文件 (位于 /*mnt/c/用户/<current user>/文档/Kube 目录*)。

### <a name="create-the-kubernetes-container"></a>创建 Kubernetes 容器

```Bash  
kubectl.exe create -f /mnt/c/users/<current  user>/documents/Kube/iris_deployment.yaml
```

    ![Alt text](media/azure-stack-solution-machine-learning/image67.png)

检查部署状态：

```Bash  
Kubectl get deployments
```

    ![Alt text](media/azure-stack-solution-machine-learning/image68.png)

部署可能需要一些时间。

### <a name="configure-azure-devops-to-deploy-automatically"></a>配置 Azure DevOps，以自动部署

#### <a name="create-a-team-project"></a>创建团队项目

1.  确保[项目集合管理员组成员身份。](https://docs.microsoft.com/vsts/organizations/security/set-project-collection-level-permissions?view=vsts) 若要创建团队项目**创建新的项目**权限必须设置为**允许**。

2.  从项目页上，选择**新的项目**。

    ![Alt text](media/azure-stack-solution-machine-learning/image69.png)

1.  将项目命名**HybridMLIris**。

2.  选择为其初始源控件类型**Git**

3.  选择进程并选择**创建**。

    ![Alt text](media/azure-stack-solution-machine-learning/image70.png)

### <a name="import-some-code--create-repository"></a>导入一些代码创建存储库

需要使用 YAML 代码的 Git 存储库。

#### <a name="add-user-to-the-git-repo"></a>将用户添加到 GIT 存储库

1.  从默认项目面板中，选择生成 Git 凭据。

    ![Alt text](media/azure-stack-solution-machine-learning/image71.png)

1.  所需和保存 Git 凭据的位置，请输入密码。

    ![Alt text](media/azure-stack-solution-machine-learning/image72.png)

1.  通过选择初始化存储库**初始化**按钮，创建**自述文件**文件。

    ![Alt text](media/azure-stack-solution-machine-learning/image73.png)

#### <a name="clone-the-git-repository-locally-and-upload-the-code"></a>本地 Git 存储库克隆并上传代码。 

1.  在计算机中创建一个目录`c:\\users\\<User>\\source\\repos\\hybridMLIris`，并克隆存储库

    ```Bash  
    sudo mkdir /mnt/c/users/<User>/source sudo mkdir /mnt/c/users/<User>/source/repos sudo mkdir /mnt/c/users/<User>/source/repos/hybridMLIris cd /mnt/c/users/<User>/source/repos/hybridMLIris sudo git clone  https://<yourvstssite>.visualstudio.com/HybridMLIris/_git/HybridMLIris
    ```

    ![Alt text](media/azure-stack-solution-machine-learning/image74.png)

1.  导航到新克隆存储库：

    ```Bash  
    ls
    cd ./HybridMLIris
    ```
    
    ![Alt text](media/azure-stack-solution-machine-learning/image75.png)

1.  复制**iris_deployment.yaml**进入存储库的文件。

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_deployment.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_deployment.yaml
    ``` 

1.  提交在 GIT 中的更改

    ```Bash  
    git add . git commit -m Added Deployment YAML git push
    ```

    ![Alt text](media/azure-stack-solution-machine-learning/image76.png)

### <a name="prepare-the-private-build-and-release-agent-for-vsts-integration"></a>为 VSTS 集成准备专用生成和发布代理

#### <a name="prerequisites"></a>必备组件

VSTS 进行身份验证针对 Azure 资源管理器使用服务主体。 VSTS 能够预配 Azure Stack 订阅中的资源需要参与者状态。 \**需要配置才能启用此类身份验证的高级步骤如下：**

1.  应创建服务主体，或使用现有的服务主体。

2.  需要创建服务主体的身份验证密钥。

3.  Azure Stack 订阅需要验证通过基于角色的访问控制，若要允许 SPN 将参与者角色的一部分。

4.  必须使用 Azure Stack 终结点和 SPN 信息在 VSTS 中创建新的服务定义。

#### <a name="service-principal-creation"></a>服务主体创建

请参阅[服务主体创建说明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)创建服务主体，并选择 Web 应用程序/API 应用程序类型。

**创建访问密钥**

服务主体需要使用密钥进行身份验证，请遵循本部分中的步骤生成密钥。

1.  从**应用注册**Azure Active Directory 中选择的应用程序。

    ![选择应用程序](media/azure-stack-solution-machine-learning/image77.png)

1.  请记下的值**应用程序 id。在 VSTS 中配置服务终结点时，使用的值。**

    ![Alt text](media/azure-stack-solution-machine-learning/image78.png)

1.  若要生成身份验证密钥，请选择“设置”。

    ![选择“设置”](media/azure-stack-solution-machine-learning/image79.png)

1.  选择“密钥”。

    ![选择密钥](media/azure-stack-solution-machine-learning/image80.png)

1.  提供密钥说明和密钥持续时间。 完成后，选择“保存”。

    ![保存密钥](media/azure-stack-solution-machine-learning/image81.png)

保存密钥后, 会显示密钥的值。 复制此值，因为稍后需要它。 **键值**与应用程序时需要登录的应用程序 ID。 存储应用程序可在其中检索密钥值。

![Alt text](media/azure-stack-solution-machine-learning/image82.png)

#### <a name="get-tenant-id"></a>获取租户 ID

VSTS 服务终结点配置的一部分，需要**租户 ID** ，对应于 Azure Stack 戳已部署到的 AAD 目录。 请遵循本部分所述的步骤收集租户 ID。

1.  选择“Azure Active Directory”。

    ![选择 azure active directory](media/azure-stack-solution-machine-learning/image83.png)

1.  若要获取租户 ID，请选择**属性**为 Azure AD 租户。

    ![选择 Azure AD 属性](media/azure-stack-solution-machine-learning/image84.png)

1.  复制“目录 ID”。 此值是租户 id。

    ![租户 ID](media/azure-stack-solution-machine-learning/image85.png)

授予服务主体的权限在 Azure Stack 订阅中部署资源

若要访问订阅中的资源，将分配到角色的应用程序。 决定哪个角色表示应用程序的相应权限。 若要了解有关可用角色的信息，请参阅 [RBAC：内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

在订阅、 资源组或资源的级别设置作用域。 较低级别的作用域将继承权限。 例如，添加到资源组的读取者角色的应用程序使得它可以读取资源组和它所包含的所有资源。

1.  导航到所需级别的作用域为应用程序分配。 例如，若要在订阅范围内分配角色，选择“订阅”。

    ![Alt text](media/azure-stack-solution-machine-learning/image86.jpeg)

1.  选择**订阅**（资源组或资源），为应用程序分配。

    ![选择进行分配的订阅](media/azure-stack-solution-machine-learning/image87.png)

1.  选择“访问控制 (IAM)”。

    ![选择访问权限](media/azure-stack-solution-machine-learning/image88.png)

1.  选择 **添加** 。

    ![选择添加](media/azure-stack-solution-machine-learning/image89.png)

1.  选择要为应用程序分配的角色。 下图显示了“所有者”角色。

    ![Alt text](media/azure-stack-solution-machine-learning/image90.png)

1.  默认情况下，可用选项中不显示 Azure Active Directory 应用程序。 若要查找的应用程序，**提供的名称**在搜索字段中，选择它。

    ![Alt text](media/azure-stack-solution-machine-learning/image91.png)

1.  选择“保存”完成角色分配。 分配给该作用域角色的用户列表中显示的应用程序。

### <a name="role-based-access-control"></a>基于角色的访问控制

Azure 基于角色的访问控制 (RBAC) 可用于对 Azure 和 Azure Stack 的精细访问管理。 使用 RBAC，可以授予仅执行其任务所需的用户的访问量。 有关基于角色的访问控制的详细信息请参阅[管理对 Azure 订阅资源的访问](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)。

**VSTS 代理池**

而不是单独管理每个代理，代理已组织成**代理池**。 代理池定义该池中所有代理的共享边界。 在 VSTS 中，代理池的作用域到 VSTS 帐户，以便可以在团队项目之间共享。 有关详细信息和如何创建 VSTS 代理池，请参阅[创建代理池和队列。](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)

**适用于 Azure Stack 添加 aPersonal 访问令牌 (PAT)**

 -  登录到 VSTS 帐户，然后选择上的**帐户配置文件**名称。

 -  选择“管理安全性”以访问令牌创建页。

![Alt text](media/azure-stack-solution-machine-learning/image92.png)

![Alt text](media/azure-stack-solution-machine-learning/image93.jpeg)

![Alt text](media/azure-stack-solution-machine-learning/image94.jpeg)

> [!Note]  
> 获取令牌信息。 退出此屏幕后不再会显示此信息。

1.  复制**令牌**。

    ![Alt text](media/azure-stack-solution-machine-learning/image95.png)

#### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>在 Azure Stack 托管的生成服务器上安装 VSTS 生成代理

1.  连接到**Build Server**部署在 Azure Stack 主机上。

    > [!Note]  
    > 请确保 Azure Stack 托管生成服务器可从公共 Internet 访问。 如果没有，请使用 Azure Stack 操作员以获取访问权限。

    ```Bash  
    ssh <user>@<buildserver.publicip>
    ```

2.  Ubuntu 生成服务器升级到最新版本 (18.04):

    ```Bash  
    sudo su
    apt-get update
    apt-get upgrade
    apt-get dist-upgrade
    apt-get autoremove
    do-release-upgrade -d
    ```

    > [!Note]  
    > 此操作将需要一些时间。

2.  安装生成服务器的先决条件应用程序。 从 bash shell 的 ubuntu 基于生成服务器运行以下命令：

    ```Bash  
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg
    sudo mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/
    wget -q https://packages.microsoft.com/config/ubuntu/18.04/prod.list 
    sudo mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
    sudo chown root:root /etc/apt/trusted.gpg.d/microsoft.asc.gpg
    sudo chown root:root /etc/apt/sources.list.d/microsoft-prod.list
    sudo apt-get install apt-transport-https
    sudo apt-get update
    sudo apt-get install liblttng-ust0 libcurl3 libssl1.0.0 curl lsb-release libkrb5-3 zlib1g libicu60 aspnetcore-runtime-2.1 dotnet-sdk-2.1
    wget https://github.com/PowerShell/PowerShell/releases/download/v6.1.0-preview.3/powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo dpkg -i powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo apt-get install -f
    AZ_REPO=$(lsb_release -cs)
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | \
        sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add –
    sudo apt-get update && sudo apt-get install azure-cli
    ```

2.  下载并部署生成代理作为服务使用**个人访问令牌 (PAT)** 和 VM 管理员帐户运行。

    ![Alt text](media/azure-stack-solution-machine-learning/image96.png)

    ```Bash  
        cd \home\<user>
        sudo mkdir myagent && cd myagent
        wget https://vstsagentpackage.azureedge.net/agent/2.134.2/vsts-agent-linux-x64-2.134.2.tar.gz
        sudo tar zxvf vsts-agent-linux-x64-2.134.2.tar.gz
    ```

2.  转到提取的生成代理文件夹。 运行下面的代码。

    ```Bash  
        cd ..
        sudo chmod -R 777 myagent
        cd myagent
        ./config.sh
    ```

    ![Alt text](media/azure-stack-solution-machine-learning/image97.png)

2.  之后 **./config.sh**完成后，请运行以下代码以便在服务器启动服务，并启动服务：

    ```Bash  
    sudo ./svc.sh install
    sudo ./svc.sh start
    ```

VSTS 文件夹中，代理是现在可见。

#### <a name="endpoint-creation-permissions"></a>终结点创建权限

用户可以创建终结点，使 VSTO 生成能够将 Azure 服务应用部署到堆栈。 VSTS 会连接到生成代理，而后者会连接到 Azure Stack。

![Alt text](media/azure-stack-solution-machine-learning/image98.png)

1.  在“设置”菜单中，选择“安全性”。

2.  在左侧的“VSTS 组”列表中，选择“终结点创建者”。

    ![Alt text](media/azure-stack-solution-machine-learning/image99.png)

3.  上**成员选项卡上，** 选择 **+ 添加**。

    ![Alt text](media/azure-stack-solution-machine-learning/image100.png)

1.  类型**用户名**并从列表中选择用户名。

2.  选择“保存更改”。

    ![Alt text](media/azure-stack-solution-machine-learning/image101.png)

3.  在左侧的“VSTS 组”列表中，选择“终结点管理员”。

4.  上**成员选项卡上，** 选择 **+ 添加**。

5.  键入一个**用户名**并从列表中选择该用户。

6.  选择**保存更改。**

    ![buchatech](media/azure-stack-solution-machine-learning/image102.jpeg)

    Azure Stack 中的生成代理会从 VSTS 获取指令，然后，VSTS 会传达与 Azure Stack 通信所需的终结点信息。

    从 VSTS 到 Azure Stack 的连接现已准备就绪。

    ![Alt text](media/azure-stack-solution-machine-learning/image103.png)

### <a name="configure-build-and-release-definitions"></a>配置生成和发布定义

现在，建立连接，您将手动映射创建 Azure 的终结点，AKS 和 Azure 容器注册表与生成和发布定义。

#### <a name="create-the-build-definition-for-the-yaml-code"></a>创建生成定义的 YAML 代码

1.  选择生成和发布中心下的生成部分并创建新的定义。

    ![Alt text](media/azure-stack-solution-machine-learning/image104.png)

1.  选择 VSTS Git，并选择前面创建的存储库。

    ![Alt text](media/azure-stack-solution-machine-learning/image105.png)

1.  选择空管道作为模板

    ![Alt text](media/azure-stack-solution-machine-learning/image106.png)

1.  命名生成**复制项目**和选择 Azure Stack 构建服务器的代理队列。

    ![Alt text](media/azure-stack-solution-machine-learning/image107.png)

1.  在进程中，选择第 1 阶段和对其重命名**复制项目**，然后**将任务添加**到阶段：

    ![Alt text](media/azure-stack-solution-machine-learning/image108.png)

1.  选择**发布生成项目**从**实用工具**列表，然后选择**添加**。

    ![Alt text](media/azure-stack-solution-machine-learning/image109.png)

1.  选择**发布路径**，然后选择**iris_deployment.yaml**文件。

    ![Alt text](media/azure-stack-solution-machine-learning/image110.png)

1.  命名项目**iris_deployment** ，然后选择要发布位置**Azure 管道**。

    ![Alt text](media/azure-stack-solution-machine-learning/image111.png)

1.  选择**保存并排队**。

    ![Alt text](media/azure-stack-solution-machine-learning/image112.png)

1.  通过选择生成 id。 检查生成的状态

    ![Alt text](media/azure-stack-solution-machine-learning/image113.png)

成功将类似于此：

![Alt text](media/azure-stack-solution-machine-learning/image114.png)

#### <a name="create-the-release-definition-for-the-yaml-code"></a>创建发布定义的 YAML 代码

1.  选择版本部分下生成和发布中心，新的定义

    ![Alt text](media/azure-stack-solution-machine-learning/image115.png)

1.  为模板选择空管道。

    ![Alt text](media/azure-stack-solution-machine-learning/image106.png)

1.  名称的环境 Azure Stack。

    ![Alt text](media/azure-stack-solution-machine-learning/image116.png)

1.  通过选择添加新项目**项目**和 **+ 添加**

2.  选择生成作为源类型和**HybridMLIris**为项目。

3.  然后选择前面创建的源的生成定义。

4.  然后选择**添加**。

    ![Alt text](media/azure-stack-solution-machine-learning/image117.png)

1.  从环境中，选择 Azure Stack，然后将新任务添加到 Azure Stack

    ![Alt text](media/azure-stack-solution-machine-learning/image118.png)


1.  代理阶段设置到 Azure Stack 托管生成服务器的代理队列。

    ![Alt text](media/azure-stack-solution-machine-learning/image119.png)

1.  将新任务添加到此阶段选择部署到 Kubernetes 任务下部署并选择添加。

    ![Alt text](media/azure-stack-solution-machine-learning/image120.png)


1.  其命名为**Kubectl Apply** （默认名称），然后选择应用命令。

    ![Alt text](media/azure-stack-solution-machine-learning/image121.png)

    现在，创建新的 Kubernetes 服务连接。

#### <a name="create-kubernetes-service-endpoint"></a>创建 Kubernetes 服务终结点

1.  Kubernetes 服务连接下选择 **+ 新建**按钮，然后选择**Kubernetes**从列表中。 可以使用此终结点连接**VSTS**并**Azure 容器服务 (AKS)**。

2.  **连接名称**： 提供连接名称。

3.  **服务器 URL**： 提供 formathttp 中的容器服务地址: / / {API 服务器地址}

4.  **Kubeconfig**： 若要获取 Kubeconfig 值，请使用管理员权限启动命令提示符中运行以下 Azure 命令。

    > [!Important]  
    > 使用此 CLI 窗口来完成后续步骤。

6.  在 CLI 窗口中登录到 Azure。 [详细了解 az 登录](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

7.  在命令提示符处，输入：

    ```CLI
        az login
    ```

10. 此命令返回的代码可以在浏览器中使用<https://aka.ms/devicelogin>。

11. 转到<https://aka.ms/devicelogin>在浏览器中。 出现提示时，输入在 CLI 中，接收到浏览器中的代码。

    ![Kubernetes 服务终结点](media/azure-stack-solution-machine-learning/image122.png)

1.  若要获取的 Kubernetes 群集的访问凭据在命令提示符处键入以下命令。

### <a name="azure-ml-workbench-cli"></a>Azure ML Workbench CLI

az aks get-credentials 来获取凭据资源组<yourResourceGroup>名称 <yourazurecontainerservice>

![Kubernetes 服务终结点](media/azure-stack-solution-machine-learning/image123.png)

1.  导航到 **.kube**的主目录下的文件夹 (例如： c:\\用户\\<user>\\文档\\Kube)

2.  将复制的内容**config**文件，并将其粘贴在 Kubernetes 连接窗口中。 选择**确定**按钮。

    ![Kubernetes 服务终结点](media/azure-stack-solution-machine-learning/image124.png)
    

3.  一旦创建并选择 Kubernetes 终结点，选择使用配置文件复选框以添加配置文件。 然后浏览到链接的项目中的 iris_deployment.yaml 文件。

    ![Alt text](media/azure-stack-solution-machine-learning/image125.png)

    ![Alt text](media/azure-stack-solution-machine-learning/image126.png)

4.  保存发布定义。

#### <a name="check-the-status-of-the-release-definition"></a>检查发布定义的状态。 

保存后，发布定义 VSTS 应自动启动发布。

在 WSL 命令提示符处，运行快速命令，然后检查 Kubernetes UI 检查部署的状态。

```Bash  
kubectl get deployments
```

输出应类似于此操作，请在部署过程中。

![Alt text](media/azure-stack-solution-machine-learning/image127.png)

```Bash  
kubectl proxy
```

Kubernetes UI 开始运行后，浏览到在部署[ **https://localhost:8001/** ](https://localhost:8001/)然后导航到**工作负荷-> 副本集**。

![Alt text](media/azure-stack-solution-machine-learning/image128.png)

### <a name="deploy-the-yaml-service"></a>部署 YAML 服务

#### <a name="upload-the-irisserviceyaml-to-the-repository-and-sync-changes"></a>将 iris_service.yaml 上传到存储库和同步更改

1.  导航到新克隆存储库：

    ```Bash  
    cd /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/
    ```

    ![Alt text](media/azure-stack-solution-machine-learning/image75.png)

1.  复制**iris_service.yaml**进入存储库的文件。

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_service.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_service.yaml
    ```

1.  提交在 GIT 中的更改

    ```Bash  
    git add .
    git commit -m “Added Service YAML” 
    git push
    ```

![Alt text](media/azure-stack-solution-machine-learning/image129.png)

#### <a name="update-the-build-definition-for-the-yaml-code"></a>更新 YAML 代码的生成定义

1.  选择生成和发布中心下的生成部分并选择前面创建的定义。

    ![Alt text](media/azure-stack-solution-machine-learning/image130.png)

2.  选择编辑按钮以编辑定义。

    ![Alt text](media/azure-stack-solution-machine-learning/image131.png)

3.  **将任务添加**到阶段。 选择**发布生成项目**从**实用工具**列表，然后选择**添加**。

    ![Alt text](media/azure-stack-solution-machine-learning/image108.png)

4.  其命名为**Kubectl Apply** （默认名称），然后选择应用命令。



#### <a name="update-the-release-definition-for-the-yaml-code"></a>更新的 YAML 代码发布定义

1.  选择下生成和发布中心 theReleases 部分并选择前面创建的发布定义。 然后选择编辑链接。

    ![Alt text](media/azure-stack-solution-machine-learning/image132.png)

1.  选择的环境**Azure Stack**然后将新任务添加到 Azure Stack。

    ![Alt text](media/azure-stack-solution-machine-learning/image133.png)

1.  添加**新的任务**到此阶段中，选择**部署到 Kubernetes**任务下**部署**，然后选择**添加**。

    ![Alt text](media/azure-stack-solution-machine-learning/image134.png)

1.  其命名为**Kubectl Apply** （默认名称），然后选择应用命令。

    ![Alt text](media/azure-stack-solution-machine-learning/image109.png)

1.  为更早版本，创建的 Azure Stack 连接设置 Kubernates 服务连接，然后选择**使用配置文件**复选框以添加配置文件。 浏览到链接的项目中的 iris_service.yaml 文件。

    ![Alt text](media/azure-stack-solution-machine-learning/image135.png)


    ![Alt text](media/azure-stack-solution-machine-learning/image136.png)

1.  保存发布定义。

#### <a name="check-the-status-of-the-release-definition"></a>检查发布定义的状态

保存后，发布定义 VSTS 应自动启动发布。

在 WSL 命令提示符处，运行快速命令，然后检查 Kubernetes UI 检查部署的状态。

```Bash  
kubectl get deployments
```

输出应类似于此操作，请在部署过程中。

![Alt text](media/azure-stack-solution-machine-learning/image127.png)


```Bash  
kubectl proxy
```

Kubernetes UI 开始运行后，浏览到在部署[ **https://localhost:8001/** ](https://localhost:8001/)然后导航到**工作负荷-> 副本集**。

![Alt text](media/azure-stack-solution-machine-learning/image137.png)


### <a name="kubernetes-scoring-and-validation"></a>Kubernetes 评分和验证

启动 Kubernetes UI

```Bash  
kubectl proxy
```

浏览到 Kubernetes UI，然后转到**部署** -> **鸢尾花部署** -> **新副本设置** ->  **Iris-部署-xxxxxxxxx** （其中 x 是部署 ID）。

![Alt text](media/azure-stack-solution-machine-learning/image138.png)

然后导航到**Services** ，然后选择**外部终结点**要验证的服务的正常工作。

![Alt text](media/azure-stack-solution-machine-learning/image139.png)

应显示一条类似于下面的验证消息：

![Alt text](media/azure-stack-solution-machine-learning/image140.png)

#### <a name="create-azure-stack-scoring-function-app-in-the-azure-stack-portal"></a>创建在 Azure Stack 门户中评分函数应用的 Azure Stack

所需的函数应用托管的每个函数的执行。 Function app 允许函数分组为逻辑单元，以便更轻松的管理、 部署和共享资源。

1.  从 Azure Stack 用户门户中，选择 **+ 新建**位于左上角的按钮，然后选择**Web + 移动** >**Function App**。

    ![Alt text](media/azure-stack-solution-machine-learning/image141.png)

1.  命名函数**数据函数**并将其放在同一资源组使用其余的机器学习内容。 让该工具自动创建一个新的应用服务计划，供使用，并使用前面创建的应用程序存储的存储帐户。

    ![定义新的 function app 设置](media/azure-stack-solution-machine-learning/image142.png)

1.  选择**创建**预配和部署函数应用。

2.  在门户的右上角选择通知图标，并观察**部署成功**消息。

    ![定义新的函数应用设置](media/azure-stack-solution-machine-learning/image143.png)

1.  选择**转到资源**若要查看新的 function app。

    ![Alt text](media/azure-stack-solution-machine-learning/image144.png)

1.  通过选择创建新的函数**函数**，然后 **+ 新建函数**按钮。

    ![Alt text](media/azure-stack-solution-machine-learning/image145.png)

1.  选择 HTTP 触发器

    ![Alt text](media/azure-stack-solution-machine-learning/image146.png)

1.  选择**C\#** 作为语言，并将函数命名：**清理得分数据**，并将授权级别设置为**Anonymous**。

    ![Alt text](media/azure-stack-solution-machine-learning/image147.png)

1.  复制-粘贴到函数清理得分数据的代码示例的内容。

    ![Alt text](media/azure-stack-solution-machine-learning/image148.png)

#### <a name="use-postman-to-validate-functions"></a>使用 Postman 来验证函数

若要确保已设置 Kbernetes 和函数正确可用于免费应用 Postman 进行测试和验证架构和函数。 若要开始此过程，首先需要从 Kubernetes 实例中收集一些信息。

1.  浏览到 Kubernetes UI，然后转到**部署** -> **鸢尾花部署** -> **新副本设置** ->  **Iris-部署-xxxxxxxxx** （其中 x 是部署 ID）

    ![Alt text](media/azure-stack-solution-machine-learning/image138.png)

1.  然后导航到**Services**并复制**外部终结点**。

    ![Alt text](media/azure-stack-solution-machine-learning/image149.png)

1.  下载并安装 Postman 应用[此处](https://www.getpostman.com/apps)必要。

2.  登录到 Postman 应用并关闭新建文件对话框。

    ![Alt text](media/azure-stack-solution-machine-learning/image150.png)

1.  从在 postman 应用中，选择 POST...

    ![Alt text](media/azure-stack-solution-machine-learning/image151.png)

1.  粘贴**外部终结点**下的 postman 应用到的 URL**请求 URL**添加**\\分数**到如下所示的 URL 的末尾。

    ![Alt text](media/azure-stack-solution-machine-learning/image152.png)

1.  选择**正文**选项卡，然后将数据类型作为**原始**，然后**JSON**。

    ![Alt text](media/azure-stack-solution-machine-learning/image153.png)

1.  从 web 浏览器中，导航到**外部终结点**。 将以下内容添加到 URL **/swagger.json**这会导致用来测试安装程序的服务 Swagger 文件。

    ![Alt text](media/azure-stack-solution-machine-learning/image154.png)

1.  中列出的示例复制**Swagger.JSON**文件。

2.  在 Postman 应用中，示例粘贴到 Post 正文，然后选择**发送**。 它应返回一个值，类似于如下所示。

    ![Alt text](media/azure-stack-solution-machine-learning/image155.png)

## <a name="step-7-create-an-azure-stack-storage-account-and-storage-queue"></a>步骤 7： 创建 Azure Stack 存储帐户和存储队列

创建数据的 Azure Stack 存储帐户和存储队列。

1.  登录到 Azure Stack 用户门户。 (每个 Azure Stack 有唯一的门户 URL)

2.  在 Azure Stack 用户门户中，展开左侧和右侧以打开服务菜单，然后选择菜单**所有服务**。 向下滚动到“存储”，选择“存储帐户”。 在中**存储帐户**窗口中选择**添加**。

3.  输入存储帐户的名称。

4.  选择存储帐户的复制选项： **LRS**。

5.  指定新资源组或选择现有资源组。

6.  选择**本地**的存储帐户的位置。

7.  选择**创建**创建存储帐户。

    ![Alt text](media/azure-stack-solution-machine-learning/image156.png)

1.  选择最近创建的存储帐户。

2.  选择上**队列**。

    ![Alt text](media/azure-stack-solution-machine-learning/image157.png)

1.  选择上 **+ 队列**并命名队列和选择**确定。**

    ![Alt text](media/azure-stack-solution-machine-learning/image158.png)

1.  获取**连接字符串**存储队列并将其复制。

    ![Alt text](media/azure-stack-solution-machine-learning/image159.png)

1.  导航到 Azure 函数应用，然后选择**应用程序设置**。

    ![Alt text](media/azure-stack-solution-machine-learning/image160.png)

1.  在函数应用的应用程序设置中向下滚动到应用程序设置，然后选择 **+ 添加新设置**。

    ![Alt text](media/azure-stack-solution-machine-learning/image161.png)

1.  输入中的存储帐户名称**名称**字段中，在末尾添加; （_s）

这允许应用程序以了解这是存储帐户终结点。

1.  然后粘贴连接字符串**值**字段。

    ![Alt text](media/azure-stack-solution-machine-learning/image162.png)

1.  向上滚动到顶部的应用程序设置，然后选择**保存**。

    ![Alt text](media/azure-stack-solution-machine-learning/image163.png)

### <a name="update-the-scoring-function-to-use-storage-queue"></a>更新要使用存储队列的评分函数

1.  选择上**集成**函数，并取消选中**获取**选项。

2.  选择“保存”。

3.  然后选择 **+ 新建输出**从输出。

    ![Alt text](media/azure-stack-solution-machine-learning/image164.png)

1.  然后选择**Azure 队列存储**，然后选择**选择**。

    ![Alt text](media/azure-stack-solution-machine-learning/image165.png)

1.  更新**队列名称**到存储队列之前，创建，然后设置**存储帐户连接**创建较早的和选择的存储帐户连接到**保存。**

    ![Alt text](media/azure-stack-solution-machine-learning/image166.png)

## <a name="step-8-create-a-function-to-handle-clean-data"></a>步骤 8： 创建一个函数来处理清理数据

创建一个新的 Azure Stack 函数来从 Azure Stack 的干净的数据移到 Azure。

1.  通过选择创建新的函数**函数**，然后 **+ 新建函数**按钮。

    ![Alt text](media/azure-stack-solution-machine-learning/image167.png)

1.  选择“计时器触发器”。

    ![Alt text](media/azure-stack-solution-machine-learning/image168.png)

1.  选择**C\#** 作为语言，并将函数命名：**上传到 azure**并将计划设置为**0 0 \*/1 \* \* \*** 它在 CRON 表示法为每小时一次。

    ![Alt text](media/azure-stack-solution-machine-learning/image169.png)

### <a name="get-the-connection-string-to-the-azure-hosted-storage-account"></a>获取 Azure 托管的存储帐户的连接字符串

1.  浏览到<https://portal.azure.com>，然后选择**Azure 存储帐户**之前创建。

2.  选择**访问密钥**，然后将复制**连接字符串**的存储帐户。

    ![Alt text](media/azure-stack-solution-machine-learning/image170.png)

### <a name="update-the-upload-to-azure-function-to-use-the-azure-hosted-storage"></a>更新上载到 azure 函数，以使用 Azure 托管的存储

1.  导航到 Azure 函数应用，然后选择**应用程序设置**。

    ![Alt text](media/azure-stack-solution-machine-learning/image171.png)

1.  在函数应用的应用程序设置中向下滚动到应用程序设置，然后选择 **+ 添加新设置**。

    ![Alt text](media/azure-stack-solution-machine-learning/image172.png)

1.  输入中的存储帐户名称**名称**字段中，在末尾添加; （_s）

这允许应用程序以了解这是存储帐户终结点。

1.  然后粘贴 Azure 托管存储帐户连接字符串**值**字段。

    ![Alt text](media/azure-stack-solution-machine-learning/image173.png)

1.  向上滚动到顶部的应用程序设置，然后选择**保存**。

    ![Alt text](media/azure-stack-solution-machine-learning/image174.png)

1.  导航回到**上传到 azure**函数。

2.  选择上**集成**函数上。

3.  然后选择 **+ 新建输出**从输出。

    ![Alt text](media/azure-stack-solution-machine-learning/image175.png)

1.  然后选择**Azure Blob 存储**，然后选择**选择**。

    ![Alt text](media/azure-stack-solution-machine-learning/image176.png)

1.  更新**路径**到以下格式在前面创建的存储容器： **uploadeddata / {rand guid}.txt**，然后设置**存储帐户连接**到存储帐户连接到 Azure 之前创建选择**保存。**

    ![Alt text](media/azure-stack-solution-machine-learning/image177.png)

1.  复制-粘贴的代码示例的内容**上传到 azure**到函数。

2.  根据需要为指向存储帐户连接字符串修改。

3.  保存并运行代码。

    ![Alt text](media/azure-stack-solution-machine-learning/image178.png)

1.  检查要查看的数据的 Azure 托管的存储帐户已分析到从 Azure 云： 成功将类似于下面。

    ![Alt text](media/azure-stack-solution-machine-learning/image179.png)

清理敏感数据的 Azure Stack 托管 Kubernetes 机器学习数据并将其上载到 Azure 公有云，从本地 Azure Stack 中，通过 Azure Stack 托管函数的应用程序，并可以暂存中边缘/断开连接的上传数据此方案。

## <a name="next-steps"></a>后续步骤

 - 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
