---
title: 在 Azure 迁移中添加迁移工具
description: 了解如何在 Azure 迁移中添加迁移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185961"
---
# <a name="add-migration-tools"></a>添加迁移工具

本文介绍如何在[Azure 迁移](migrate-overview.md)中添加迁移工具。

Azure 迁移提供了用于评估和迁移到 Azure 的工具中心。 它包括本机工具、其他 Azure 服务提供的工具和第三方独立软件供应商 （ISV） 产品。

如果要添加迁移工具但尚未设置 Azure 迁移项目，请按照[本文操作](how-to-add-tool-first-time.md)。



## <a name="selecting-an-isv-tool"></a>选择 ISV 工具

如果您选择[ISV 工具](migrate-services-overview.md#isv-integration)进行迁移，则可以根据 ISV 策略从获取许可证或注册免费试用开始。 每个工具中都有一个用于连接到 Azure Migrate 的选项。 部署该工具，并按照工具说明和文档将工具工作区与 Azure 迁移连接。 

## <a name="select-a-migration-scenario"></a>选择迁移方案

1. 在 Azure Migrate 项目中，单击“概述”。****
2. 选择要使用的迁移方案：

    - 要将计算机和工作负荷迁移到 Azure，请选择 **"评估和迁移服务器**"。
    - 要迁移本地 SQL 计算机，请选择 **"评估和迁移数据库**"。
    - 要迁移本地 Web 应用，请选择"**评估和迁移 Web 应用**"。
    - 要在脱机模式下将大量本地数据迁移到 Azure，请选择 **"订购数据框**"。

    ![评估方案](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>选择服务器迁移工具

1. 单击 **"评估和迁移服务器**"。
2. 在**Azure 迁移 - 服务器**中，如果尚未添加迁移工具，请在**迁移工具**下选择 **"单击此处添加迁移工具**"。 如果已添加迁移工具，请在 **"添加更多迁移工具"** 中，选择 **"更改**"。

    > [!NOTE]
    > 如果需要导航到其他项目，请在**Azure 迁移 - 服务器**中，在 **"查看其他迁移项目的详细信息"** 旁边，**单击此处**。

3. 在**Azure 迁移中**，选择要使用的迁移工具。
    - 如果使用 Azure 迁移服务器迁移，则可以直接在 Azure 迁移项目中设置和运行迁移。
    - 如果使用第三方评估工具，请导航到为 ISV 提供的链接，然后按照它们提供的说明运行迁移。

## <a name="select-a-database-migration-tool"></a>选择数据库迁移工具

1. 单击 **"评估和迁移数据库**"
2. 在 **"数据库"** 中，单击"**添加工具**"。
3. 在添加工具>**选择迁移工具**中，选择要用于迁移数据库的工具。

## <a name="select-a-web-app-migration-tool"></a>选择 Web 应用迁移工具

1. 单击 **"评估和迁移 Web 应用**"。
2. 按照 Azure 应用服务的迁移工具的链接进行操作。 使用迁移工具：

    - **在线评估应用**：您可以使用 Azure 应用服务迁移助手在线评估和迁移具有公共 URL 的应用。
    - **.NET/PHP**：对于内部 .NET 和 PHP 应用，您可以下载并运行迁移助手。

## <a name="order-an-azure-data-box"></a>订购 Azure 数据框

要将大量数据迁移到 Azure，可以订购 Azure DAta Box 进行脱机数据传输。

1. 单击 **"订购数据框**"。
2. 在 **"选择 Azure 数据框"中**，指定订阅。 
3. 传输将导入到 Azure。 指定数据源和数据的 Azure 区域目标。

## <a name="next-steps"></a>后续步骤

尝试使用 Azure 迁移服务器迁移进行[超 V](tutorial-migrate-hyper-v.md)或[VMware](tutorial-migrate-vmware.md) VM。
