---
title: 创建和管理 Azure Migrate 项目
description: 在 Azure 迁移中查找、创建、管理和删除项目。
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: f5079ed979d98f2c6f0c654c860c6f176f366497
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676395"
---
# <a name="create-and-manage-azure-migrate-projects"></a>创建和管理 Azure Migrate 项目

本文介绍如何创建、管理和删除[Azure 迁移](migrate-services-overview.md)项目。


## <a name="create-a-project-for-the-first-time"></a>首次创建项目

首次设置 Azure 迁移时，将创建项目并添加评估或迁移工具。 [请按照这些说明](how-to-add-tool-first-time.md)进行首次设置。

## <a name="create-additional-projects"></a>创建其他项目

如果已有 Azure 迁移项目，并且想要创建其他项目，请执行以下操作：  

1. 在[Azure 公共门户](https://portal.azure.com)或[Azure 政府中](https://portal.azure.us)，搜索**Azure 迁移**。
2. 在 Azure 迁移仪表板>**服务器**上，选择右上角的**更改**。

   ![更改 Azure 迁移项目](./media/create-manage-projects/switch-project.png)

3. 要创建新项目，请选择**此处单击**。

   ![创建第二个 Azure 迁移项目](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>查找项目

查找项目，如下所示：

1. 在[Azure 门户](https://portal.azure.com)中，搜索**Azure 迁移**。
2. 在 Azure 迁移仪表板>**服务器**中，选择右上角的**更改**。

    ![切换到现有的 Azure 迁移项目](./media/create-manage-projects/switch-project.png)

3. 选择适当的订阅和 Azure 迁移项目。


如果在以前的 Azure 迁移[版本中](migrate-services-overview.md#azure-migrate-versions)创建了项目，则查找它，如下所示：

1. 在[Azure 门户](https://portal.azure.com)中，搜索**Azure 迁移**。
2. 在 Azure 迁移仪表板中，如果在以前的版本中创建了项目，则会出现引用旧项目的横幅。 选择横幅。

    ![访问现有项目](./media/create-manage-projects/access-existing-projects.png)

3. 查看旧项目的列表。


## <a name="delete-a-project"></a>删除项目

删除如下：

1. 打开在其中创建项目的 Azure 资源组。
2. 在资源组页中，选择 **"显示隐藏类型**"。
3. 选择要删除的迁移项目及其关联的资源。
    - 资源类型是**Microsoft.迁移/迁移项目**。
    - 如果资源组由 Azure 迁移项目独占使用，则可以删除整个资源组。


请注意：

- 删除时，将删除项目和有关已发现计算机的元数据。
- 如果使用旧版本的 Azure 迁移，则打开创建项目的 Azure 资源组。 选择要删除的迁移项目（资源类型是**迁移项目**）。
- 如果将依赖项分析与 Azure 日志分析工作区一起使用：
    - 如果将日志分析工作区附加到服务器评估工具，则不会自动删除工作区。 同一日志分析工作区可用于多个方案。
    - 如果要删除日志分析工作区，请手动执行此操作。

### <a name="delete-a-workspace-manually"></a>手动删除工作区

1. 浏览到附加到该项目的 Log Analytics 工作区。

    - 如果尚未删除 Azure 迁移项目，则可以在 **"基本服务器** > **评估**"中找到指向工作区的链接。
       ![LA](./media/create-manage-projects/loganalytics-workspace.png)工作区 .
       
    - 如果已删除 Azure 迁移项目，请在 Azure 门户的左侧窗格中选择 **"资源组"** 并查找工作区。
       
2. [按照说明](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)删除工作区。

## <a name="next-steps"></a>后续步骤

向 Azure 迁移项目添加[评估](how-to-assess.md)或[迁移](how-to-migrate.md)工具。
