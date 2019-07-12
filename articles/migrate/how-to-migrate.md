---
title: 在 Azure Migrate 中添加迁移工具
description: 介绍如何在 Azure 迁移中心中添加迁移工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b3c77f052ed92db235b363e63859b9beb9e4f5a2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811864"
---
# <a name="add-migration-tools"></a>添加迁移工具

本文介绍如何添加中的迁移工具[Azure Migrate](migrate-overview.md)。

Azure Migrate 提供一个中心工具对于评估和迁移到 Azure。 它包括本机工具工具提供的其他 Azure 服务和第三方独立软件供应商 (ISV) 产品/服务。

如果你想要添加的迁移工具，并且尚未设置 Azure Migrate 项目，请按照这[一文](how-to-add-tool-first-time.md)。



## <a name="selecting-an-isv-tool"></a>选择 ISV 工具

如果愿意[ISV 工具](migrate-services-overview.md#isv-integration)迁移时，您可以通过获取许可证，或注册免费试用版，根据 ISV 策略中开始。 每个工具，在没有连接到 Azure Migrate 的选项。 部署工具，并按照使用 Azure Migrate 连接工具工作区的工具的说明和文档。 

## <a name="select-a-migration-scenario"></a>选择迁移方案

1. 在 Azure Migrate 项目中，单击**概述**。
2. 选择你想要使用的迁移方案：

    - 若要将计算机和工作负荷迁移到 Azure，请选择**评估和迁移服务器**。
    - 若要迁移本地 SQL 计算机上，选择**评估数据库迁移**。
    - 若要迁移的本地 web 应用，请选择**评估和将 web 应用迁移**。
    - 若要将大量的本地数据迁移到 Azure，在脱机模式下，选择**订购 Data Box**。

    ![评估方案](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>选择的服务器迁移工具

1. 单击**评估和迁移服务器**。
2. 在中**Azure Migrate-服务器**，如果你还，未添加迁移工具下**迁移工具**，选择**单击此处可添加迁移工具**。 如果你已在添加迁移工具**添加更多的迁移工具**，选择**更改**。

    > [!NOTE]
    > 如果你需要导航到另一个项目中的**Azure Migrate-服务器**下的**查看不同的迁移项目的详细信息**，单击**单击此处**。

3. 在中**Azure Migrate**，选择你想要使用的迁移工具。
    - 如果使用 Azure 迁移服务器迁移时，可以设置并直接在 Azure Migrate 项目中运行迁移。
    - 如果使用第三方评估工具，导航到为 ISV 提供的链接，然后运行它们提供的说明根据迁移。

## <a name="select-a-database-migration-tool"></a>选择数据库迁移工具

1. 单击**评估和迁移数据库**
2. 在中**数据库**，单击**将工具添加**。
3. 在将添加一个工具 >**选择迁移工具**，选择想要用于将数据库迁移的工具。

## <a name="select-a-web-app-migration-tool"></a>选择 web 应用迁移工具

1. 单击**评估和将 web 应用迁移**。
2. 按照 Azure App service 迁移工具的链接。 使用迁移工具：

    - **评估应用程序联机**:您可以评估和迁移使用公共 URL 联机，应用程序使用 Azure App Service 迁移助手。
    - **.NET/PHP**:对于内部.NET 和 PHP 应用程序，可以下载并运行迁移助手。

## <a name="order-an-azure-data-box"></a>Azure Data Box 订单

若要将大量数据迁移到 Azure，则可以订购 Azure DAta Box 的脱机数据传输。

1. 单击**订购 Data Box**。
2. 在中**选择 Azure Data Box**，指定你的订阅。 
3. 传输速度会导入到 Azure。 指定数据源和数据的 Azure 区域目标。

## <a name="next-steps"></a>后续步骤

尝试使用的 Azure 迁移服务器迁移的迁移[HYPER-V](tutorial-migrate-hyper-v.md)或[VMware](tutorial-migrate-vmware.md) Vm。
