---
title: 快速入门：将 Power BI 工作区链接到 Synapse 工作区
description: 遵循本指南中的步骤将 Power BI 工作区链接到 Azure Synapse Analytics 工作区。
services: synapse-analytics
author: jocaplan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/29/2020
ms.author: jocaplan
ms.reviewer: jrasnick
ms.openlocfilehash: 9b4b9da37b753418773141f832f253003ad40014
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260450"
---
# <a name="quickstart-linking-a-power-bi-workspace-to-a-synapse-workspace"></a>快速入门：将 Power BI 工作区链接到 Synapse 工作区

本快速入门介绍如何将 Power BI 工作区连接到 Synapse Analytics 工作区，以便从 Synapse Studio（预览版）创建新的 Power BI 报表和数据集。

如果没有 Azure 订阅，请[在开始之前创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- [创建 Azure Synapse 工作区和关联的存储帐户](quickstart-create-workspace.md)。
- [Power BI Professional 或 Premium 工作区](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces)

## <a name="link-power-bi-workspace-to-your-synapse-workspace"></a>将 Power BI 工作区链接到 Synapse 工作区

1. 在 Synapse Studio 中，单击“管理”。

    ![Synapse Studio 单击管理。](media/quickstart-link-powerbi/synapse-studio-click-manage.png)

2. 在“外部连接”下，单击“链接服务”。

    ![已突出显示链接服务。](media/quickstart-link-powerbi/manage-click-linked-services.png)

3. 单击“+ 新建”。

    ![已突出显示“+ 新建链接服务”。](media/quickstart-link-powerbi/new-highlighted.png)

4. 单击“Power BI”，然后单击“继续” 。

    ![显示 Power BI 链接服务。](media/quickstart-link-powerbi/powerbi-linked-service.png)

5. 输入链接服务的名称，并从下拉列表中选择一个工作区。

    ![显示 Power BI 链接服务设置。](media/quickstart-link-powerbi/workspace-link-dialog.png)

6. 单击“创建”。

## <a name="view-power-bi-workspace-in-synapse-studio"></a>在 Synapse Studio 中查看 Power BI 工作区

链接工作区后，可以在 Synapse Studio 中浏览 Power BI 数据集、修改/创建新 Power BI 报表。

1. 单击“开发”。

    ![Synapse Studio 单击“开发”。](media/quickstart-link-powerbi/synapse-studio-click-develop.png)

2. 展开 Power BI 和要使用的工作区。

    ![展开 Power BI 和工作区。](media/quickstart-link-powerbi/develop-expand-powerbi.png)

可以在“开发”选项卡的顶部单击 + 创建新报表 。可以通过单击报表名称修改现有报表。 所有保存的更改都将写回到 Power BI 工作区。

![查看和修改 Power BI 报表。](media/quickstart-link-powerbi/powerbi-report.png)


## <a name="next-steps"></a>后续步骤

详细了解如何[创建有关 Azure 存储中存储的文件的 Power BI 报表](sql/tutorial-connect-power-bi-desktop.md)。
