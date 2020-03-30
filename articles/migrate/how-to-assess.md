---
title: 在 Azure 迁移中添加评估工具
description: 了解如何在 Azure 迁移中添加评估工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185916"
---
# <a name="add-assessment-tools"></a>添加评估工具

本文介绍如何在[Azure 迁移](migrate-overview.md)中添加评估工具。

Azure 迁移提供了用于评估和迁移到 Azure 的工具中心。 它包括 Azure 迁移工具，以及其他工具和独立的软件供应商 （ISV） 产品。

如果要添加评估工具，但尚没有 Azure 迁移项目，请按照[本文操作](how-to-add-tool-first-time.md)。

## <a name="select-a-tool"></a>选择工具

如果选择非 Azure 迁移工具进行评估，则根据工具策略从获取许可证或注册免费试用开始。 工具可以选择连接到 Azure 迁移。 按照说明和文档操作，将该工具连接到 Azure 迁移。 [了解有关工具的更多](migrate-services-overview.md)详细信息。


## <a name="select-an-assessment-scenario"></a>选择评估方案

1. 在 Azure Migrate 项目中，单击“概述”。****
2. 选择要使用的评估方案：

    - 要发现和评估用于迁移到 Azure 的计算机和工作负荷，请选择 **"评估和迁移服务器**"。
    - 要评估本地 SQL 计算机，请选择 **"评估和迁移数据库**"。
    - 要评估本地 Web 应用，请选择"**评估和迁移 Web 应用**"。

    ![评估方案](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>选择服务器评估工具 

1. 单击 **"评估和迁移服务器**"。
2. 在**Azure 迁移 - 服务器**中，如果尚未添加评估工具，请在 **"评估工具**"下选择 **"单击此处添加评估工具**"。 如果已添加评估工具，请在**添加更多评估工具**中，选择 **"更改**"。

    > [!NOTE]
    > 如果需要导航到其他项目，请在**Azure 迁移 - 服务器**中，在 **"查看其他迁移项目的详细信息"** 旁边，**单击此处**。

3. 在**Azure 迁移中**，选择要使用的评估工具。

    - 如果使用 Azure 迁移服务器评估，则可以直接在 Azure 迁移项目中设置、运行和查看评估。
    - 如果您使用不同的评估工具，请导航到为其站点提供的链接，并按照他们提供的说明运行评估。


## <a name="select-a-database-assessment-tool"></a>选择数据库评估工具

1. 单击 **"评估和迁移数据库**"
2. 在 **"数据库"** 中，单击"**添加工具**"。
3. 在添加工具>**选择评估工具**中，选择要用于评估数据库的工具。

## <a name="select-a-web-app-assessment-tool"></a>选择 Web 应用评估工具

1. 单击 **"评估和迁移 Web 应用**"。
2. 按照 Azure 应用服务的迁移工具的链接进行操作。 使用迁移工具：

    - **在线评估应用**：您可以使用 Azure 应用服务迁移助手在线评估具有公共 URL 的应用。
    - **.NET/PHP**：对于内部 .NET 和 PHP 应用，您可以下载并运行迁移助手。



## <a name="next-steps"></a>后续步骤

尝试使用 Azure 迁移服务器评估[VMware](tutorial-prepare-vmware.md) [VM、Hyper-V](tutorial-prepare-hyper-v.md)或[物理服务器](tutorial-prepare-physical.md)进行评估
