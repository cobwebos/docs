---
title: 在 Azure Migrate 中添加迁移工具
description: 了解如何在 Azure Migrate 中添加迁移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185961"
---
# <a name="add-migration-tools"></a>添加迁移工具

本文介绍如何在[Azure Migrate](migrate-overview.md)中添加迁移工具。

Azure Migrate 提供了一个用于评估和迁移到 Azure 的工具中心。 它包括本机工具、其他 Azure 服务提供的工具和第三方独立软件供应商（ISV）产品。

如果要添加迁移工具并且尚未设置 Azure Migrate 项目，请遵循此[文](how-to-add-tool-first-time.md)。



## <a name="selecting-an-isv-tool"></a>选择 ISV 工具

如果你选择用于迁移的[ISV 工具](migrate-services-overview.md#isv-integration)，则可从获取许可证开始，或者根据 ISV 策略注册免费试用版。 每个工具中都有一个用于连接到 Azure Migrate 的选项。 部署工具，然后按照工具说明和文档，将工具工作区与 Azure Migrate 连接起来。 

## <a name="select-a-migration-scenario"></a>选择迁移方案

1. 在 Azure Migrate 项目中，单击“概述”。
2. 选择要使用的迁移方案：

    - 若要将计算机和工作负荷迁移到 Azure，请选择 "**评估和迁移服务器**"。
    - 若要迁移本地 SQL 计算机，请选择 "**评估和迁移数据库**"。
    - 若要迁移本地 web 应用，请选择 "**评估和迁移 web 应用**"。
    - 若要在脱机模式下将大量本地数据迁移到 Azure，请选择 "对**Data Box 排序**"。

    ![评估方案](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>选择服务器迁移工具

1. 单击 "**评估和迁移服务器**"。
2. 在**Azure Migrate 服务器**中，如果尚未添加迁移工具，请在 "**迁移工具**" 下选择 "**单击此处添加迁移工具**"。 如果已添加迁移工具，请在 "**添加更多迁移工具**" 中选择 "**更改**"。

    > [!NOTE]
    > 如果需要导航到不同的项目，请在**Azure Migrate 服务器**中，单击 "**查看其他迁移项目的详细信息**" 旁边的 "**单击此处**"。

3. 在**Azure Migrate**中，选择要使用的迁移工具。
    - 如果使用 Azure Migrate Server 迁移，则可以直接在 Azure Migrate 项目中设置和运行迁移。
    - 如果使用第三方评估工具，请导航到为 ISV 提供的链接，并根据提供的说明运行迁移。

## <a name="select-a-database-migration-tool"></a>选择数据库迁移工具

1. 单击 "**评估和迁移数据库**"
2. 在 "**数据库**" 中，单击 "**添加工具**"。
3. 在 "添加工具" >**选择 "迁移工具**" 中，选择要用于迁移数据库的工具。

## <a name="select-a-web-app-migration-tool"></a>选择 web 应用迁移工具

1. 单击 "**评估和迁移 web 应用**"。
2. 请访问 Azure App Service 的迁移工具的链接。 使用迁移工具可以：

    - **联机评估应用**：你可以使用 Azure App Service 迁移助手，通过联机的公共 URL 来评估和迁移应用。
    - **.Net/PHP**：对于内部 .NET 和 php 应用程序，可以下载并运行迁移助手。

## <a name="order-an-azure-data-box"></a>排序 Azure Data Box

若要将大量数据迁移到 Azure，可以订购 Azure DAta Box 进行脱机数据传输。

1. 单击 "**订单 Data Box**"。
2. 在 "**选择 Azure Data Box**中，指定你的订阅。 
3. 传输将是导入到 Azure。 指定数据源和数据的 Azure 区域目标。

## <a name="next-steps"></a>后续步骤

尝试使用适用于[hyper-v](tutorial-migrate-hyper-v.md)或[VMware](tutorial-migrate-vmware.md) Vm 的 Azure Migrate 服务器迁移进行迁移。
