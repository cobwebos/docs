---
title: 创建和管理 Azure Migrate 项目
description: 在 Azure Migrate 中查找、创建、管理和删除项目。
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: a49595f0580e71048239d5c5d8f4d1a66e24fe6d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269635"
---
# <a name="create-and-manage-azure-migrate-projects"></a>创建和管理 Azure Migrate 项目

本文介绍如何创建、管理和删除[Azure Migrate](migrate-services-overview.md)项目。


## <a name="create-a-project-for-the-first-time"></a>首次创建项目

首次设置 Azure Migrate 时，请创建一个项目，并添加一个评估或迁移工具。 [按照这些说明进行操作](how-to-add-tool-first-time.md)以首次设置。

## <a name="create-additional-projects"></a>创建其他项目

如果你已有一个 Azure Migrate 项目，并且想要创建其他项目，请执行以下操作：  

1. 在[Azure 门户](https://portal.azure.com)中，搜索**Azure Migrate**。
2. 在 Azure Migrate 仪表板 >**服务器**上，选择右上角的 "**更改**"。

   ![更改 Azure Migrate 项目](./media/create-manage-projects/switch-project.png)

3. 若要创建新项目，请选择 **"单击此处"** 。

   ![创建第二个 Azure Migrate 项目](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>查找项目

查找项目，如下所示：

1. 在[Azure 门户](https://portal.azure.com)中，搜索**Azure Migrate**。
2. 在 Azure Migrate 面板 >**服务器**"中，选择右上角的"**更改**"。

    ![切换到现有 Azure Migrate 项目](./media/create-manage-projects/switch-project.png)

3. 选择相应的订阅并 Azure Migrate 项目。


如果在 Azure Migrate 的[以前版本](migrate-services-overview.md#azure-migrate-versions)中创建项目，请按如下所示进行查找：

1. 在[Azure 门户](https://portal.azure.com)中，搜索**Azure Migrate**。
2. 在 Azure Migrate "仪表板" 中，如果已在以前的版本中创建了一个项目，则将显示引用较旧项目的标题。 选择标题。

    ![访问现有项目](./media/create-manage-projects/access-existing-projects.png)

3. 查看旧项目的列表。


## <a name="delete-a-project"></a>删除项目

删除，如下所示：

1. 打开在其中创建项目的 Azure 资源组。
2. 在 "资源组" 页上，选择 "**显示隐藏的类型**"。
3. 选择要删除的迁移项目及其关联的资源。
    - 资源类型为 " **migrateprojects**"。
    - 如果 Azure Migrate 项目专门使用资源组，则可以删除整个资源组。


请注意：

- 删除时，将删除项目和有关发现的计算机的元数据。
- 如果使用的是较旧版本的 Azure Migrate，请打开在其中创建项目的 Azure 资源组。 选择要删除的迁移项目（资源类型为 "**迁移项目**"）。
- 如果使用 Azure Log Analytics 工作区进行依赖关系分析：
    - 如果已将 Log Analytics 工作区附加到服务器评估工具，则不会自动删除此工作区。 同一 Log Analytics 工作区可用于多个方案。
    - 如果要删除 Log Analytics 工作区，请手动执行该操作。

### <a name="delete-a-workspace-manually"></a>手动删除工作区

1. 浏览到附加到该项目的 Log Analytics 工作区。

    - 如果尚未删除 Azure Migrate 项目，可以在**Essentials** > **服务器评估**中找到工作区的链接。
       ![LA 工作区](./media/create-manage-projects/loganalytics-workspace.png)。
       
    - 如果已删除 Azure Migrate 项目，请在 Azure 门户的左窗格中选择 "**资源组**"，然后找到工作区。
       
2. [按照说明](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)删除工作区。

## <a name="next-steps"></a>后续步骤

将[评估](how-to-assess.md)或[迁移](how-to-migrate.md)工具添加到 Azure Migrate 项目。
