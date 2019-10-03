---
title: 添加 Azure Migrate 中评估工具 |Microsoft Docs
description: 介绍如何在 Azure 迁移中心中添加评估工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: d176e6276d69cd3465aa4943efa86ea1e6b0736d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811643"
---
# <a name="add-assessment-tools"></a>添加评估工具

本文介绍如何添加在评估工具[Azure Migrate](migrate-overview.md)。

Azure Migrate 提供一个中心工具对于评估和迁移到 Azure。 它包括本机工具工具提供的其他 Azure 服务和第三方独立软件供应商 (ISV) 产品/服务。

如果你想要添加评估工具，并且你还没有 Azure Migrate 项目，请按照这[一文](how-to-add-tool-first-time.md)。

## <a name="selecting-an-isv-tool"></a>选择 ISV 工具

如果愿意[ISV 工具](migrate-services-overview.md#isv-integration)进行评估，您可以通过获取许可证，或注册免费试用版，根据 ISV 策略中开始。 每个工具，在没有连接到 Azure Migrate 的选项。 请按照使用 Azure Migrate 连接工具工作区的工具的说明和文档。 


## <a name="select-an-assessment-scenario"></a>选择评估方案

1. 在 Azure Migrate 项目中，单击**概述**。
2. 选择你想要使用的评估方案：

    - 若要发现和评估计算机和工作负荷是否适合迁移到 Azure，请选择**评估和迁移服务器**。
    - 若要评估的本地 SQL 计算机，请选择**评估数据库迁移**。
    - 若要评估的本地 web 应用，选择**评估和将 web 应用迁移**。

    ![评估方案](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>选择服务器评估工具 

1. 单击**评估和迁移服务器**。
2. 在中**Azure Migrate-服务器**，如果没有下添加的评估工具**评估工具**，选择**单击此处可添加评估工具**。 如果你已添加在评估工具**添加更多评估工具**，选择**更改**。

    > [!NOTE]
    > 如果你需要导航到另一个项目中的**Azure Migrate-服务器**下的**查看不同的迁移项目的详细信息**，单击**单击此处**。

3. 在中**Azure Migrate**，选择你想要使用的评估工具。

    
    ![评估工具](./media/how-to-assess/assess-tool.png)

    - 如果使用 Azure Migrate Server 评估，可以设置、 运行和 Azure Migrate 项目中直接查看评估。
    - 如果使用第三方评估工具，导航到为其站点提供的链接，然后运行它们提供的说明根据评估。


## <a name="select-a-database-assessment-tool"></a>选择数据库评估工具

1. 单击**评估和迁移数据库**
2. 在中**数据库**，单击**将工具添加**。
3. 在将添加一个工具 >**选择评估工具**，选择你想要使用它来评估你的数据库的工具。

## <a name="select-a-web-app-assessment-tool"></a>选择 web 应用程序评估工具

1. 单击**评估和将 web 应用迁移**。
2. 按照 Azure App service 迁移工具的链接。 使用迁移工具：

    - **评估应用程序联机**:可以使用 Azure App Service 迁移助手来评估使用公共 URL 联机，应用程序。
    - **.NET/PHP**:对于内部.NET 和 PHP 应用程序，可以下载并运行迁移助手。



## <a name="next-steps"></a>后续步骤

试用评估使用的 Azure Migrate Server 评估[HYPER-V](tutorial-prepare-hyper-v.md)或[VMware](tutorial-prepare-vmware.md) Vm。
