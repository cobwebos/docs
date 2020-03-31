---
title: Azure 数据资源管理器的 Azure DevOps 任务
description: 在本主题中，您将学习创建发布管道并部署
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472037"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure 数据资源管理器的 Azure DevOps 任务

[Azure DevOps 服务](https://azure.microsoft.com/services/devops/)提供开发协作工具，如高性能管道、免费专用 Git 存储库、可配置看板以及广泛的自动和连续测试功能。 [Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)是一种 Azure DevOps 功能，使您能够管理 CI/CD，以便使用适用于任何语言、平台和云的高性能管道部署代码。
[Azure 数据资源管理器 - 管理命令](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX)是 Azure 管道任务，它使您能够创建发布管道并将数据库更改部署到 Azure 数据资源管理器数据库。 它在[视觉工作室市场](https://marketplace.visualstudio.com/)免费提供。

本文档介绍了有关使用**Azure 数据资源管理器和管理命令**任务将架构更改部署到数据库的简单示例。 有关完整的 CI/CD 管道，请参阅[Azure DevOps 文档](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* Azure 数据资源管理器群集设置：
    * [Azure 数据资源管理器群集和数据库](/azure/data-explorer/create-cluster-database-portal)
    * 通过[预配 Azure AD 应用程序创建](/azure/kusto/management/access-control/how-to-provision-aad-app)Azure 活动目录 （Azure AD） 应用。
    * 通过[管理 Azure 数据资源管理器数据库权限](/azure/data-explorer/manage-database-permissions)，在 Azure 数据资源管理器数据库上授予对 Azure AD 应用的访问权限。
* Azure DevOps 设置：
    * [注册免费组织](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [创建组织](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [在 Azure 开发人员计划中创建项目](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [使用 Git 的代码](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>创建文件夹

在 Git 存储库中创建以下示例文件夹（*函数*、*策略*、*表*）。 [在此处](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline)将文件复制到相应的文件夹中，如下所示并提交更改。 提供示例文件以执行以下工作流。

![创建文件夹](media/devops/create-folders.png)

> [!TIP]
> 在创建自己的工作流时，我们建议您使代码具有幂等性。 例如，使用[.create-merge 表](/azure/kusto/management/create-table-command#create-merge-table)而不是[.create 表](/azure/kusto/management/create-table-command)，并使用[.create 或更改](/azure/kusto/management/create-alter-function)函数而不是[.create](/azure/kusto/management/create-function)函数。

## <a name="create-a-release-pipeline"></a>创建发布管道

1. 登录到 Azure [DevOps 组织](https://dev.azure.com/)。
1. 选择 **"管道** > **从左侧菜单中释放"** 并选择 **"新建管道**"。

    ![新建管道](media/devops/new-pipeline.png)

1. 将打开 **"新建"管道**窗口。 在"**管道"** 选项卡中，在"**选择模板**"窗格中，选择 **"空作业**"。

     ![选择模板](media/devops/select-template.png)

1. 选择 **"阶段"** 按钮。 在 **"阶段"** 窗格中，添加**舞台名称**。 选择 **"保存"** 以保存管道。

    ![命名舞台](media/devops/stage-name.png)

1. 选择 **"添加项目**"按钮。 在"**添加项目"** 窗格中，选择代码所在的存储库，填写相关信息，然后单击"**添加**"。 选择 **"保存"** 以保存管道。

    ![添加项目](media/devops/add-artifact.png)

1. 在 **"变量"** 选项卡中，选择 **"添加"** 以创建将在任务中使用的**终结点 URL**的变量。 编写终结点**的名称**和**值**。 选择 **"保存"** 以保存管道。 

    ![创建变量](media/devops/create-variable.png)

    要查找**Endpoint_URL，Azure 数据资源管理器群集**在 Azure 门户中的概述页包含 Azure 数据资源管理器群集 URI。 以以下格式`https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`构造 URI。  例如，https：\//kustodocs.westus.kusto.windows.net？数据库名称_示例DB

    ![Azure 数据资源管理器群集 URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>创建要部署的任务

1. 在 **"管道"** 选项卡中，单击**1 个作业，0 个任务**以添加任务。 

    ![添加任务](media/devops/add-task.png)

1. 按此顺序创建三个任务以部署**表**、**函数**和**策略**。 

1. 在"**任务"** 选项卡中**+**，按 **"代理作业**"选择 。 搜索“Azure 数据资源管理器”****。 在**应用商店**中，安装**Azure 数据资源管理器和管理员命令**扩展。 然后，选择 **"在****运行 Azure 数据资源管理器"命令**中添加。

     ![添加管理员命令](media/devops/add-admin-commands.png)

1. 单击左侧的**Kusto 命令**，然后使用以下信息更新任务：
    * **显示名称**：任务的名称
    * **文件路径**：在**表**任务中，指定 */table/*.csl，因为表创建文件位于*表*文件夹中。
    * **终结点 URL**：`EndPoint URL`输入在上一步中创建的变量。
    * 选择 **"使用服务终结点**"并选择 **" 新建**"。

    ![更新库斯塔命令任务](media/devops/kusto-command-task.png)

1. 在 **"添加 Azure 数据资源管理器"服务连接**窗口中完成以下信息：

    |设置  |建议的值  |
    |---------|---------|
    |**连接名称**     |    输入名称以标识此服务终结点     |
    |**群集 URL**    |    值可在 Azure 门户中的 Azure 数据资源管理器群集的概述部分中找到 | 
    |**服务主体 ID**    |    输入 AAD 应用 ID（作为先决条件创建）     |
    |**服务主体应用密钥**     |    输入 AAD 应用密钥（作为先决条件创建）    |
    |**AAD 租户 ID**    |      输入您的 AAD 租户（如microsoft.com、contoso.com...    |

    选择"**允许所有管道使用此连接**"复选框。 选择“确定”。

    ![添加服务连接](media/devops/add-service-connection.png)

1. 重复步骤 1-5 两次以从*函数*和*策略*文件夹中部署文件。 选择“保存”。**** 在"**任务"** 选项卡中，请参阅创建的三个任务：**部署表**、**部署函数**和**部署策略**。

    ![部署所有文件夹](media/devops/deploy-all-folders.png)

1. 选择 **+ 发布** > **创建版本**以创建版本。

    ![创建发布](media/devops/create-release.png)

1. 在 **"日志"** 选项卡中，检查部署状态是否成功。

    ![部署成功](media/devops/deployment-successful.png)

您现已完成发布管道的创建，用于部署三个任务进行预生产。