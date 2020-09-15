---
title: 创建 Azure Synapse 工作区 - Azure 资源管理器模板
description: 了解如何使用 Azure 资源管理器模板创建 Synapse 工作区。
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: c64f3d835eeede79f937bbaadb0a54992176438d
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2020
ms.locfileid: "89500651"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-a-deployment-template"></a>快速入门：使用部署模板创建 Azure Synapse 工作区

此 Azure 资源管理器模板（ARM 模板）将创建具有基础 Data Lake Storage 的 Azure Synapse 工作区。 Azure Synapse 工作区是 Azure Synapse Analytics 中分析过程的安全对象协作边界。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

可通过选择“可视化”链接来查看模板，如下所示：

[![可视化](../media/template-deployments/template-visualize-button.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

该模板定义了两个资源：

- 存储帐户
- 工作区

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开模板。 使用此模板创建 Synapse 工作区。
   
   [![部署到 Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. 输入或更新以下值：

   * 订阅：选择 Azure 订阅。
   * 资源组： 选择“新建”，输入资源组的唯一名称，然后选择“确定”。 新的资源组会促进资源清理。
   * **区域**：选择区域。  例如“美国中部”。
   * 名称：输入工作区的名称。
   * SQL 管理员登录：输入 SQL Server 的管理员用户名。
   * SQL 管理员密码：输入 SQL Server 的管理员密码。
   * **标记值**：接受默认值。 
   * 审阅并创建：选中。
   * **创建**：选中。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Synapse Analytics 和 Azure 资源管理器，请继续阅读以下文章。

- 阅读 [Azure Synapse Analytics 概述](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 
- 了解有关 [Azure 资源管理器](../azure-resource-manager/management/overview.md)的详细信息
- [创建并部署第一个 ARM 模板](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
