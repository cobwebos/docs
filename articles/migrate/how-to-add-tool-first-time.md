---
title: 在 Azure Migrate 中添加评估/迁移工具
description: 介绍如何创建 Azure Migrate 项目并添加评估/迁移工具。
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 1294831162df973e1f87052c1520d5d95990cf8f
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901192"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>第一次添加评估/迁移工具

本文介绍如何首次将评估或迁移工具添加到[Azure Migrate](migrate-overview.md)项目。  
Azure Migrate 提供了一个中心，用于跟踪对 Azure 的本地应用程序、工作负荷以及专用/公有云 Vm 的发现、评估和迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及其他工具和独立软件供应商（ISV）[产品](migrate-services-overview.md#isv-integration)。 

## <a name="check-permissions-to-create-project"></a>检查创建项目的权限

如果尚未创建 Azure Migrate 项目，请检查您是否具有正确的权限。

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。  你应该拥有“参与者”或“所有者”权限。  
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。

## <a name="create-a-project-and-add-a-tool"></a>创建项目并添加工具

在 Azure 订阅中设置新的 Azure Migrate 项目，并添加工具。

- Azure Migrate 项目用于存储从正在评估或迁移的环境中收集的发现、评估和迁移元数据。 
- 在项目中，可以跟踪发现的资产，并安排评估和迁移。

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。 
2. 在“服务”下选择“Azure Migrate”。  

    ![设置 Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. 在“概述”中，单击“评估和迁移服务器”。  
4. 在“发现、评估和迁移服务器”下，单击“评估和迁移服务器”。  

    ![发现和评估服务器](./media/how-to-add-tool-first-time/assess-migrate.png)

1. 在“发现、评估和迁移服务器”中，单击“添加工具”。  
2. 在“迁移项目”中选择你的 Azure 订阅，并创建一个资源组（如果没有）。 
3. 在 "**项目详细信息**" 中，指定要在其中创建项目的项目名称和地理位置。  查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

    ![创建 Azure Migrate 项目](./media/how-to-add-tool-first-time/migrate-project.png)

    - 为项目指定的地理位置仅用于存储从本地 VM 中收集的元数据。 可为实际迁移选择任一目标区域。
    - 如果需要在地理位置的特定区域内部署项目，请使用以下 API 创建项目。 指定订阅 ID、资源组名称和项目名称以及位置。 查看[公有](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)的地理位置/区域。

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. 单击 "**下一步**"，并添加评估或迁移工具。

    > [!NOTE]
    > 创建项目时，需要至少添加一个评估或迁移工具。

5. 在 "**选择评估工具**" 中，添加一个评估工具。 如果不需要评估工具，请选择 "**跳过添加评估工具** > **"。** 
2. 在 "**选择迁移工具**" 中，根据需要添加迁移工具。 如果 > **你现在不**需要迁移工具，请选择 "**立即跳过添加迁移工具**"。
3. 在“检查 + 添加工具”中检查设置，然后单击“添加工具”。  

创建项目后，可以选择用于评估和迁移服务器、数据库和 web 应用的其他工具。

## <a name="create-additional-projects"></a>创建其他项目

在某些情况下，可能需要创建其他 Azure Migrate 项目。 例如，如果你的数据中心位于不同的地理位置，或者需要将元数据存储在不同的地理位置。 创建另一个项目，如下所示：

1. 在当前 Azure Migrate 项目中，单击 "**服务器**" 或 "**数据库**"。
2. 在右上角单击当前项目名称旁边的 "**更改**"。
3. 在 "**设置**" 中，选择 **"单击此处以创建新项目**"。
4. 创建一个新的项目并添加一个新工具，如前一过程中所述。

## <a name="next-steps"></a>后续步骤

- 开始[Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)或[Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)。
- 如果添加了 ISV 工具或 Movere，请查看用于将工具链接到 Azure Migrate 的[步骤](prepare-isv-movere.md)。
- 了解如何添加其他[评估](how-to-assess.md)和[迁移](how-to-migrate.md)工具。 
