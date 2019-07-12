---
title: 在 Azure Migrate 中首次添加评估/迁移工具 |Microsoft Docs
description: 介绍如何创建 Azure Migrate 项目并添加评估/迁移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b226f7c5879673b573133cde45db78d8d1f2fffa
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812020"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>第一次添加评估/迁移工具

本文介绍如何添加到评估或迁移工具[Azure Migrate](migrate-overview.md)第一次的项目。  
Azure Migrate 提供中心，以跟踪发现、 评估和迁移的本地应用和工作负荷，及私钥/公钥云 Vm 到 Azure。 在中心提供了用于评估和迁移，以及第三方、 独立软件供应商 (ISV) 的 Azure 迁移工具[产品/服务](migrate-services-overview.md#isv-integration)。 

## <a name="create-a-project-and-add-a-tool"></a>创建一个项目并添加工具

设置新的 Azure Migrate 项目中的 Azure 订阅，并添加一个工具。

- Azure Migrate 项目用于存储发现、 评估和迁移从正在评估或迁移的环境收集的元数据。 
- 在项目中可以跟踪已发现的资产并安排评估和迁移。

1. 在 Azure 门户 >**所有服务**，搜索**Azure Migrate**。
2. 下**Services**，选择**Azure Migrate**。

    ![设置 Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. 在中**概述**，单击**评估和迁移服务器**。
4. 下**发现、 评估和迁移服务器**，单击**评估和迁移服务器**。

    ![发现和评估服务器](./media/how-to-add-tool-first-time/assess-migrate.png)

1. 在中**发现、 评估和迁移服务器**，单击**将工具添加**。
2. 在中**迁移项目**，选择你的 Azure 订阅，并创建资源组，如果你还没有。
3. 在中**项目详细信息**，指定项目名称，并想要创建项目的地理位置。 

    ![创建 Azure Migrate 项目](./media/how-to-add-tool-first-time/migrate-project.png)

    您可以在任何这些地理区域中创建 Azure Migrate 项目。

    **地域** | **存储位置区域**
    --- | ---
    亚洲 | 东南亚或东亚
    欧洲 | 欧洲北部或欧洲西部
    英国 | 英国南部或英国西部
    美国 | 美国中部或美国西部 2

    为项目指定的地理位置仅用于存储从本地 VM 中收集的元数据。 您可以选择在实际迁移任何目标区域。

4. 单击**下一步**，并添加评估或迁移工具。

    > [!NOTE]
    > 创建项目时您需要添加至少一个评估或迁移工具。

5. 在中**选择评估工具**，添加评估工具。 如果你不需要评估工具，请选择**现在添加评估工具时跳过** > **下一步**。 
2. 在中**选择迁移工具**，根据需要添加一个迁移工具。 如果你不需要迁移工具，请稍后再试，请选择**跳过添加迁移工具现在** > **下一步**。
3. 在中**查看 + 添加工具**，查看设置，然后单击**将工具添加**。

创建项目后，您可以选择用于评估和迁移的服务器和工作负荷、 数据库和 web 应用的其他工具。

## <a name="create-additional-projects"></a>创建其他项目

在某些情况下，可能需要创建其他 Azure Migrate 项目。 例如，如果在不同的地理区域中有数据中心或需要在另一个地理位置中存储的元数据。 创建其他项目，如下所示：

1. 在当前的 Azure Migrate 项目中，单击**服务器**或**数据库**。
2. 在右上角，单击**更改**当前的项目名称旁边。
3. 在中**设置**，选择**创建新项目，请单击此处**。
4. 创建一个新项目并添加新工具，如前面的过程中所述。

## <a name="next-steps"></a>后续步骤

了解如何添加其他[评估](how-to-assess.md)并[迁移](how-to-migrate.md)工具。 
