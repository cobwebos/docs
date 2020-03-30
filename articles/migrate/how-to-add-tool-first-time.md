---
title: 在 Azure 迁移中添加评估/迁移工具
description: 介绍如何创建 Azure 迁移项目并添加评估/迁移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185941"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>第一次添加评估/迁移工具

本文介绍如何首次向[Azure 迁移](migrate-overview.md)项目添加评估或迁移工具。  
Azure 迁移提供了一个中心中心，用于跟踪本地应用和工作负荷以及私有/公共云 VM 到 Azure 的发现、评估和迁移。 中心提供用于评估和迁移的 Azure 迁移工具，以及其他工具和独立软件供应商 （ISV）[产品](migrate-services-overview.md#isv-integration)。 

## <a name="create-a-project-and-add-a-tool"></a>创建项目并添加工具

在 Azure 订阅中设置新的 Azure 迁移项目，并添加工具。

- Azure 迁移项目用于存储从要评估或迁移的环境收集的发现、评估和迁移元数据。 
- 在项目中，您可以跟踪发现的资产，并协调评估和迁移。

1. 在 Azure 门户中选择“所有服务”，然后搜索 **Azure Migrate**。****
2. 在“服务”下选择“Azure Migrate”。********

    ![设置 Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. 在“概述”中，单击“评估和迁移服务器”。********
4. 在“发现、评估和迁移服务器”下，单击“评估和迁移服务器”。********

    ![发现和评估服务器](./media/how-to-add-tool-first-time/assess-migrate.png)

1. 在“发现、评估和迁移服务器”中，单击“添加工具”。********
2. 在“迁移项目”中选择你的 Azure 订阅，并创建一个资源组（如果没有）。****
3. 在 **"项目详细信息**"中，指定要在其中创建项目的项目名称和地理位置。 

    ![创建 Azure Migrate 项目](./media/how-to-add-tool-first-time/migrate-project.png)

    可在下述任一地理位置创建 Azure Migrate 项目。

   **地理** | **存储位置区域**
    --- | ---
    亚洲   | 东南亚或东亚
    欧洲 | 欧洲北部或欧洲西部
    日本  | 日本东部或日本西部
    United Kingdom | 英国南部或英国西部
    United States | 美国中部或美国西部 2
    Canada | 加拿大中部
    印度  | 印度中部或印度南部
    澳大利亚 | 澳大利亚东南部

    为项目指定的地理位置仅用于存储从本地 VM 中收集的元数据。 可为实际迁移选择任一目标区域。

    如果要在地理位置中指定用于部署迁移项目及其关联资源的特定区域（订阅中的策略限制可能只允许将 Azure 资源部署到特定的 Azure 区域），则可以使用以下 API 进行创建迁移项目。 指定订阅 ID、资源组名称、迁移项目名称以及位置（部署 Azure 迁移的表中提及的任何 Azure 区域）。

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. 单击 **"下一步**"，然后添加评估或迁移工具。

    > [!NOTE]
    > 创建项目时，需要添加至少一个评估或迁移工具。

5. 在 **"选择评估工具"** 中，添加评估工具。 如果您不需要评估工具，请选择 **"立即跳过添加评估工具** > **下一步**"。 
2. 在**选择迁移工具**中，根据需要添加迁移工具。 如果您现在不需要迁移工具，请选择 **"立即跳过添加迁移工具** > **"。**
3. 在 **"审阅 + 添加工具**"中，查看设置并单击"**添加工具**"。

创建项目后，您可以选择用于评估和迁移服务器和工作负荷、数据库和 Web 应用的其他工具。

## <a name="create-additional-projects"></a>创建其他项目

在某些情况下，您可能需要创建其他 Azure 迁移项目。 例如，如果数据中心位于不同的地理位置，或者需要将元数据存储在不同的地理位置。 创建其他项目，如下所示：

1. 在当前 Azure 迁移项目中，单击 **"服务器**"或"**数据库**"。
2. 在右上角，单击当前项目名称旁边的 **"更改**"。
3. 在 **"设置"** 中，选择 **"单击此处"创建新项目**。
4. 创建新项目并添加新工具，如上一过程所述。

## <a name="next-steps"></a>后续步骤

了解如何添加其他[评估和](how-to-assess.md)[迁移](how-to-migrate.md)工具。 
