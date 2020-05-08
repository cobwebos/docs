---
title: 在 Azure Migrate 中添加评估工具
description: 了解如何在 Azure Migrate 中添加评估工具。
ms.topic: how-to
ms.date: 04/26/2020
ms.openlocfilehash: 7e7d7cf26cab30cd7449e4643fdfb59faece47f2
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901060"
---
# <a name="add-assessment-tools"></a>添加评估工具

本文介绍如何在[Azure Migrate](migrate-overview.md)中添加评估工具。 

- 如果你想要添加一个评估工具，但还没有 Azure Migrate 项目，请遵循此[文](how-to-add-tool-first-time.md)。
- 如果已添加 ISV 工具或 Movere，请[按照步骤](prepare-isv-movere.md)进行操作以准备使用该工具。

## <a name="select-an-assessment-scenario"></a>选择评估方案

1. 在 Azure Migrate 项目中，单击“概述”。 
2. 选择要使用的评估方案：

    - 若要发现和评估要迁移到 Azure 的计算机和工作负荷，请选择 "**评估和迁移服务器**"。
    - 若要评估本地 SQL 计算机，请选择 "**评估和迁移数据库**"。
    - 若要评估本地 web 应用，请选择 "**评估和迁移 web 应用**"。

    ![评估方案](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>选择服务器评估工具 

1. 单击 "**评估和迁移服务器**"。
2. 在**Azure Migrate-服务器**中，如果尚未添加评估工具，请在 "**评估工具**" 下选择 **"单击此处添加评估工具**"。 如果已添加了评估工具，请在 "**添加更多评估工具**" 中选择 "**更改**"。

    > [!NOTE]
    > 如果需要导航到不同的项目，请在**Azure Migrate 服务器**中，单击 "**查看其他迁移项目的详细信息**" 旁边的 "**单击此处**"。

3. 在**Azure Migrate**中，选择要使用的评估工具。

    - 如果使用 Azure Migrate Server 评估，则可以直接在 Azure Migrate 项目中设置、运行和查看评估。
    - 如果使用不同的评估工具，请导航到为其站点提供的链接，并根据提供的说明运行评估。


## <a name="select-a-database-assessment-tool"></a>选择数据库评估工具

1. 单击 "**评估和迁移数据库**"
2. 在 "**数据库**" 中，单击 "**添加工具**"。
3. 在 "添加工具" >**选择 "评估工具**" 中，选择要用于评估数据库的工具。

## <a name="select-a-web-app-assessment-tool"></a>选择 web 应用评估工具

1. 单击 "**评估和迁移 web 应用**"。
2. 请访问 Azure App Service 的迁移工具的链接。 使用迁移工具可以：

    - **联机评估应用**：你可以使用 Azure App Service 迁移助手在联机情况下使用公共 URL 来评估应用。
    - **.Net/PHP**：对于内部 .NET 和 php 应用程序，可以下载并运行迁移助手。



## <a name="next-steps"></a>后续步骤

尝试使用[VMware](tutorial-prepare-vmware.md) Vm、 [hyper-v](tutorial-prepare-hyper-v.md)或[物理服务器](tutorial-prepare-physical.md)Azure Migrate 服务器评估进行评估
