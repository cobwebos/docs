---
title: 在 Azure Migrate 中首次添加评估/迁移工具 |Microsoft Docs
description: 介绍如何创建 Azure Migrate 项目并添加评估/迁移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: bd119956ced79b73b0376fe4530c9eafaf870238
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934180"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>第一次添加评估/迁移工具

本文介绍如何首次将评估或迁移工具添加到[Azure Migrate](migrate-overview.md)项目。  
Azure Migrate 提供了一个中心，用于跟踪对 Azure 的本地应用程序、工作负荷以及专用/公有云 Vm 的发现、评估和迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商（ISV）[产品](migrate-services-overview.md#isv-integration)。 

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
3. 在 "**项目详细信息**" 中，指定要在其中创建项目的项目名称和地理位置。 

    ![创建 Azure Migrate 项目](./media/how-to-add-tool-first-time/migrate-project.png)

    可在下述任一地理位置创建 Azure Migrate 项目。

   **地域** | **存储位置区域**
    --- | ---
    亚洲   | 东南亚或东亚
    欧洲 | 欧洲北部或欧洲西部
    日本  | 日本东部或日本西部
    英国 | 英国南部或英国西部
    美国 | 美国中部或美国西部2
    加拿大 | 加拿大中部
    印度  | 印度中部或印度南部
    澳大利亚 | 澳大利亚东南部

    为项目指定的地理位置仅用于存储从本地 VM 中收集的元数据。 可为实际迁移选择任一目标区域。

    如果你想要指定地理位置内的特定区域以便部署迁移项目及其关联资源（订阅中的策略限制可能只允许将 Azure 资源部署到特定 Azure 区域），可以使用以下 API 来创建迁移项目。 指定订阅 ID、资源组名称、迁移项目名称和位置（在其中部署 Azure Migrate 的表中提及的任何 Azure 区域）。

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. 单击 "**下一步**"，并添加评估或迁移工具。

    > [!NOTE]
    > 创建项目时，需要至少添加一个评估或迁移工具。

5. 在 "**选择评估工具**" 中，添加一个评估工具。 如果不需要评估工具，请选择 "**跳过添加评估工具** >  **"。** 
2. 在 "**选择迁移工具**" 中，根据需要添加迁移工具。 如果**你现在不**需要迁移工具，请选择 " > **立即跳过添加迁移工具**"。
3. 在 "查看" 和 "**添加工具**" 中，查看设置，然后单击 "**添加工具**"。

创建项目后，可以选择用于评估和迁移服务器、数据库和 web 应用的其他工具。

## <a name="create-additional-projects"></a>创建其他项目

在某些情况下，可能需要创建其他 Azure Migrate 项目。 例如，如果你的数据中心位于不同的地理位置，或者需要将元数据存储在不同的地理位置。 创建另一个项目，如下所示：

1. 在当前 Azure Migrate 项目中，单击 "**服务器**" 或 "**数据库**"。
2. 在右上角单击当前项目名称旁边的 "**更改**"。
3. 在 "**设置**" 中，选择 **"单击此处以创建新项目**"。
4. 创建一个新的项目并添加一个新工具，如前一过程中所述。

## <a name="next-steps"></a>后续步骤

了解如何添加其他[评估](how-to-assess.md)和[迁移](how-to-migrate.md)工具。 
