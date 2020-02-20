---
title: 适用于 Azure 数据资源管理器的 azure DevOps 任务
description: 在本主题中，你将了解如何创建发布管道并部署
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472037"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>适用于 Azure 数据资源管理器的 azure DevOps 任务

[Azure DevOps Services](https://azure.microsoft.com/services/devops/)提供了开发协作工具，如高性能管道、免费专用 Git 存储库、可配置看板，以及广泛的自动和连续测试功能。 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)是一项 Azure DevOps 功能，使你能够管理 CI/CD，以使用适用于任何语言、平台和云的高性能管道来部署你的代码。
[Azure 数据资源管理器管理命令](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX)是一个 Azure Pipelines 任务，通过它可以创建发布管道，并将数据库更改部署到 Azure 数据资源管理器数据库。 它在[Visual Studio Marketplace](https://marketplace.visualstudio.com/)免费提供。

本文档介绍了使用**Azure 数据资源管理器-管理命令**任务将架构更改部署到数据库的简单示例。 有关完整的 CI/CD 管道，请参阅[Azure DevOps 文档](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)。

## <a name="prerequisites"></a>必备条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* Azure 数据资源管理器群集设置：
    * [Azure 数据资源管理器群集和数据库](/azure/data-explorer/create-cluster-database-portal)
    * 通过[预配 Azure AD 应用程序](/azure/kusto/management/access-control/how-to-provision-aad-app)创建 Azure Active Directory （Azure AD）应用。
    * 通过[管理 azure 数据资源管理器数据库权限](/azure/data-explorer/manage-database-permissions)，授予对 azure 数据资源管理器数据库上 Azure AD 应用的访问权限。
* Azure DevOps 安装程序：
    * [注册免费组织](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [创建组织](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [在 Azure DevOps 中创建项目](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [带 Git 的代码](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>创建文件夹

在 Git 存储库中创建以下示例文件夹（"*函数*"、"*策略*"、"*表*"）。 按如下所示将文件从[此处](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline)复制到各自的文件夹中，并提交更改。 提供示例文件以执行以下工作流。

![创建文件夹](media/devops/create-folders.png)

> [!TIP]
> 创建自己的工作流时，建议使代码具有幂等性。 例如，使用创建[-合并表](/azure/kusto/management/create-table-command#create-merge-table)而不是[。 create table](/azure/kusto/management/create-table-command)，并使用[. create 或 alter](/azure/kusto/management/create-alter-function) function，而不是[. create](/azure/kusto/management/create-function) function。

## <a name="create-a-release-pipeline"></a>创建发布管道

1. 登录到[Azure DevOps 组织](https://dev.azure.com/)。
1. **从左侧**菜单中选择 "**管道**" > "，然后选择"**新建管道**"。

    ![新建管道](media/devops/new-pipeline.png)

1. 此时将打开 "**新建发布管道**" 窗口。 在 "**管道**" 选项卡的 "**选择模板**" 窗格中，选择 "**空作业**"。

     ![选择模板](media/devops/select-template.png)

1. 选择**阶段**按钮。 在 "**暂存**" 窗格中，添加**阶段名称**。 选择 "**保存**" 以保存管道。

    ![为阶段命名](media/devops/stage-name.png)

1. 选择 "**添加项目**" 按钮。 在 "**添加项目**" 窗格中，选择代码所在的存储库，填写相关信息，然后单击 "**添加**"。 选择 "**保存**" 以保存管道。

    ![添加项目](media/devops/add-artifact.png)

1. 在 "**变量**" 选项卡中，选择 " **+ 添加**" 以创建将在任务中使用的**终结点 URL**的变量。 写入终结点的**名称**和**值**。 选择 "**保存**" 以保存管道。 

    ![创建变量](media/devops/create-variable.png)

    若要查找 Endpoint_URL，Azure 门户的**azure 数据资源管理器群集**的 "概述" 页包含 azure 数据资源管理器群集 URI。 采用以下格式构造 URI `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`。  例如，https：\//kustodocs.westus.kusto.windows.net？ DatabaseName = SampleDB

    ![Azure 数据资源管理器群集 URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>创建要部署的任务

1. 在 "**管道**" 选项卡中，单击 " **1 个作业，0个任务"** 以添加任务。 

    ![添加任务](media/devops/add-task.png)

1. 按照此顺序创建三个任务来部署**表**、**函数**和**策略**。 

1. 在 "**任务**" 选项卡中，选择 "按**代理作业** **+** "。 搜索“Azure 数据资源管理器”。 在**Marketplace**中，安装**Azure 数据资源管理器–管理命令**扩展。 然后，在 "**运行 Azure 数据资源管理器命令**中选择"**添加**"。

     ![添加管理命令](media/devops/add-admin-commands.png)

1. 单击左侧的**Kusto 命令**，并将任务更新为以下信息：
    * **显示名称**：任务名称
    * **文件路径**：在 "**表**" 任务中，指定 */Tables/* ，因为表创建文件位于*表*文件夹中。
    * **终结点 URL**：输入在上一步中创建的 `EndPoint URL`变量。
    * 选择 "**使用服务终结点**"，然后选择 " **+ 新建**"。

    ![Update Kusto 命令任务](media/devops/kusto-command-task.png)

1. 在 "**添加 Azure 数据资源管理器服务连接**" 窗口中填写以下信息：

    |设置  |建议的值  |
    |---------|---------|
    |**连接名称**     |    输入名称以标识此服务终结点     |
    |**群集 Url**    |    可在 Azure 数据资源管理器群集的 "概述" 部分的 Azure 门户中找到值 | 
    |**服务主体 Id**    |    输入 AAD 应用 ID （创建为先决条件）     |
    |**服务主体应用密钥**     |    输入 AAD 应用密钥（创建为必备项）    |
    |**AAD 租户 Id**    |      输入你的 AAD 租户（如 microsoft.com、contoso.com ...）    |

    选中 "**允许所有管道使用此连接**" 复选框。 选择“确定”。

    ![添加服务连接](media/devops/add-service-connection.png)

1. 重复步骤1-5 两次，以从 "*函数*和*策略*" 文件夹部署文件。 选择“保存”。 在 "**任务**" 选项卡中，请参阅创建的三个任务：**部署表**、**部署函数**和**部署策略**。

    ![部署所有文件夹](media/devops/deploy-all-folders.png)

1. 选择 " **+ release** > **创建发布**" 以创建发布。

    ![创建发布](media/devops/create-release.png)

1. 在 "**日志**" 选项卡中，检查部署状态是否为 "成功"。

    ![部署成功](media/devops/deployment-successful.png)

现在已完成创建发布管道，以便将三个任务部署到预生产。