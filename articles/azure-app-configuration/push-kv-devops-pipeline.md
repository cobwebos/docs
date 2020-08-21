---
title: 通过 Azure Pipelines 将设置推送到应用配置
description: 了解如何使用 Azure Pipelines 将键值推送到应用配置存储
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 07/27/2020
ms.author: lcozzens
ms.openlocfilehash: c1142ef7b37dee916118964778f6c1db2a65d591
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719628"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>通过 Azure Pipelines 将设置推送到应用配置

[Azure 应用配置推送](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push)任务将配置文件中的键值推送到你的应用配置存储中。 此任务将在管道内启用全部圆形功能，因为现在可以从应用配置存储中拉取设置，还可以将设置推送到应用配置存储。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- 应用配置资源-在 [Azure 门户](https://portal.azure.com)中免费创建一个。
- Azure DevOps 项目- [免费创建一个](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure 应用配置推送任务-免费下载 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.)。

## <a name="create-a-service-connection"></a>创建服务连接

通过服务连接，可以从 Azure DevOps 项目访问 Azure 订阅中的资源。

1. 在 Azure DevOps 中，前往包含目标管道的项目，并打开左下方的 **项目设置** 。
1. 在 " **管道** " 下，选择 " **服务连接** "，然后在右上方选择 " **新建服务连接** "。
1. 选择 " **Azure 资源管理器**"。
1. 选择 " **服务主体 (自动) **。
1. 填写订阅和资源。 为服务连接命名。

创建服务连接后，请查找分配给它的服务主体的名称。 在下一步中，你将向此服务主体添加新的角色分配。

1. 请参阅 "**项目设置**" "  >  **服务连接**"。
1. 选择在上一部分中创建的服务连接。
1. 选择 " **管理服务主体**"。
1. 请注意列出的 **显示名称** 。

## <a name="add-role-assignment"></a>添加角色分配

向任务中使用的凭据分配正确的应用配置角色分配，以便任务可以访问应用配置存储。

1. 导航到目标应用配置存储。 
1. 在左侧，选择 " **访问控制 (IAM") **。
1. 在顶部，选择 " **+ 添加** " 并选择 " **添加角色分配**"。
1. 在 " **角色**" 下，选择 " **应用配置数据所有者**"。 此角色允许任务在应用配置存储中进行读取和写入。 
1. 选择与您在上一节中创建的服务连接关联的服务主体。
  
## <a name="use-in-builds"></a>在生成中使用

本部分介绍如何在 Azure DevOps 生成管道中使用 Azure 应用配置推送任务。

1. 单击 "**管道**管道"，导航到 "生成管道" 页  >  **Pipelines**。 有关生成管道的文档可在 [此处](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)找到。
      - 如果要创建新的生成管道，请选择管道右侧的 " **显示助手** "，然后搜索 " **Azure 应用配置" 推送** 任务。
      - 如果使用的是现有生成管道，请在编辑管道时导航到 " **任务** " 选项卡，然后搜索 " **Azure 应用配置** " "推送" 任务。
2. 配置任务的必要参数，以将密钥值从配置文件推送到应用配置存储。 **配置文件路径**参数从文件存储库的根开始。
3. 保存并对生成进行排队。 生成日志将显示执行任务期间发生的任何故障。

## <a name="use-in-releases"></a>在发布中使用

本部分介绍如何在 Azure DevOps release 管道中使用 Azure 应用配置推送任务。

1. 通过选择 "**管道**"，导航到 "发布管道" 页  >  **Releases**。 可在 [此处](https://docs.microsoft.com/azure/devops/pipelines/release?view=azure-devops)找到有关发布管道的文档。
1. 选择现有的发布管道。 如果没有，请选择 " **+ 新建** " 以创建一个新的。
1. 选择右上角的 " **编辑** " 按钮以编辑发布管道。
1. 选择要添加任务的 **阶段** 。 可在 [此处](https://docs.microsoft.com/azure/devops/pipelines/release/environments?view=azure-devops)找到有关阶段的详细信息。
1. **+** 为该作业选择，然后在 "**部署**" 选项卡下添加 " **Azure 应用配置推送**任务"。
1. 配置任务内的必要参数，以将你的密钥值从配置文件推送到你的应用配置存储。 参数的说明可在下面的 " **参数** " 部分和每个参数旁边的工具提示中使用。
1. 保存并将发布排队。 版本日志将显示任务执行过程中遇到的任何故障。

## <a name="parameters"></a>参数

应用配置推送任务使用以下参数：

- **Azure 订阅**：包含可用 Azure 服务连接的下拉。 若要更新和刷新可用 Azure 服务连接的列表，请按文本框右侧的 " **刷新 Azure 订阅** " 按钮。
- **应用配置名称**：用于加载所选订阅下的可用配置存储的下拉。 若要更新和刷新可用配置存储的列表，请按文本框右侧的 " **刷新应用程序配置** " 按钮。
- **配置文件路径**：配置文件的路径。 可以浏览生成项目以选择配置文件。  (`...` ") " 文本框右侧的按钮。
- **Separator**：用于平展 docker-compose.override.yml 文件的分隔符。
- **深度**：要将 json 和 docker-compose.override.yml 文件平展到的深度。
- **Prefix**：附加到推送到应用配置存储的每个键开头的字符串。
- **标签**：作为应用配置存储中的标签添加到每个键值的字符串。
- **内容类型**：作为应用配置存储中的内容类型添加到每个键值的字符串。
- **标记**：格式为的 JSON 对象，用于 `{"tag1":"val1", "tag2":"val2"}` 定义添加到推送到应用配置存储的每个键值的标记。
- **删除具有指定前缀和标签的存储中的所有其他键-值**：默认值为未 **选中**状态。
  - **选中**：在推送配置文件中的新键值之前，删除应用配置存储中与指定前缀和标签匹配的所有键值。
  - **Unchecked**：将配置文件中的所有键值推送到应用配置存储中，并使应用配置存储中的所有其他内容保持不变。

填写所需参数后，运行管道。 指定配置文件中的所有键值将上传到应用配置。

## <a name="troubleshooting"></a>疑难解答

如果发生意外错误，可以通过将管道变量设置为来启用调试日志 `system.debug` `true` 。

## <a name="faq"></a>FAQ

**如何上传多个配置文件？**

在同一管道中创建 Azure 应用配置推送任务的多个实例，以将多个配置文件推送到应用配置存储。

**尝试将键值推送到配置存储时，为什么会收到409错误？**

如果任务尝试删除或覆盖在应用配置存储区中锁定的键值，将会出现409冲突错误消息。
