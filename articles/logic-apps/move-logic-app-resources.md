---
title: 跨订阅、资源组或区域移动逻辑应用-Azure 逻辑应用
description: 将逻辑应用或集成帐户迁移到其他 Azure 订阅、资源组或位置（区域）
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 8c3bad32943b83cbfe4c96087f3fef1c51f64bb1
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679113"
---
# <a name="move-logic-app-resources-to-other-azure-subscriptions-resource-groups-or-regions"></a>将逻辑应用资源移到其他 Azure 订阅、资源组或区域

若要将逻辑应用或相关资源移到另一个 Azure 订阅、资源组或区域，可以通过多种方式来完成这些任务，如 Azure 门户、Azure PowerShell、Azure CLI 和 REST API。 在移动资源之前，请查看以下注意事项： 

* 只能在 Azure 资源组或订阅之间移动[特定逻辑应用资源类型](../azure-resource-manager/move-support-resources.md#microsoftlogic)。

* 检查 Azure 订阅和每个 Azure 区域中可包含的逻辑应用资源的数量[限制](../logic-apps/logic-apps-limits-and-config.md)。 这些限制会影响当区域在订阅或资源组中保持不变时，是否可以移动特定的资源类型。 例如，每个 Azure 订阅中的每个 Azure 区域只能有一个免费层集成帐户。

* 移动资源时，Azure 会创建新的资源 Id。 因此，请确保改为使用新的 Id，并更新与所移动资源关联的任何脚本或工具。 在订阅、资源组或区域之间移动逻辑应用后，必须重新创建或重新授权任何基于 OAuth 的连接。

## <a name="prerequisites"></a>必备组件

* 用于创建逻辑应用或要移动的集成帐户的 Azure 订阅

* 资源所有者：移动和设置所需的资源。 了解有关[基于角色的访问控制（RBAC）](../role-based-access-control/built-in-roles.md#owner)的详细信息。

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>在订阅之间移动资源

若要将某个资源（例如逻辑应用或集成帐户）移动到另一个 Azure 订阅，可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API。 这些步骤涵盖了 Azure 门户，可在资源的区域保持相同时使用。 有关其他步骤和常规准备，请参阅[将资源移到新的资源组或订阅](../azure-resource-manager/resource-group-move-resources.md)。

1. 在[Azure 门户](https://portal.azure.com)中，找到并选择要移动的逻辑应用资源。

1. 在资源的 "**概述**" 页上，单击 "**订阅**" 旁边的 "**更改**" 链接。

1. 在 "**移动资源**" 页上，选择要移动的逻辑应用资源和任何相关资源。

1. 从 "**订阅**" 列表中，选择目标订阅。

1. 从 "**资源组**" 列表中，选择目标资源组。 或者，若要创建其他资源组，请选择 "**创建新组**"。

1. 若要确认你是否了解与已移动资源关联的任何脚本或工具在使用新的资源 Id 进行更新之前都不起作用，请选择确认框，然后选择 **"确定"** 。

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>在资源组之间移动资源

若要将某个资源（例如逻辑应用或集成帐户）移动到另一个 Azure 资源组，可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API。 这些步骤涵盖了 Azure 门户，可在资源的区域保持相同时使用。 有关其他步骤和常规准备，请参阅[将资源移到新的资源组或订阅](../azure-resource-manager/resource-group-move-resources.md)。

在实际在组之间移动资源之前，你可以测试是否可以成功地将资源移到另一个组。 有关详细信息，请参阅[验证移动](../azure-resource-manager/resource-group-move-resources.md#validate-move)。

1. 在[Azure 门户](https://portal.azure.com)中，找到并选择要移动的逻辑应用资源。

1. 在资源的 "**概述**" 页上，选择 "**资源组**" 旁边的 "**更改**" 链接。

1. 在 "**移动资源**" 页上，选择要移动的逻辑应用资源和任何相关资源。

1. 从 "**资源组**" 列表中，选择目标资源组。 或者，若要创建其他资源组，请选择 "**创建新组**"。

1. 若要确认你是否了解与已移动资源关联的任何脚本或工具在使用新的资源 Id 进行更新之前都不起作用，请选择确认框，然后选择 **"确定"** 。

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>在区域之间移动资源

如果要将逻辑应用移动到不同的区域，选项取决于创建逻辑应用的方式。 根据你选择的选项，必须在逻辑应用中重新创建或重新授权连接。

* 在 Azure 门户中，在新区域中重新创建逻辑应用，并重新配置工作流设置。 为了节省时间，你可以将基础工作流定义和源应用的连接复制到目标应用。 若要查看逻辑应用后面的 "代码"，请在逻辑应用设计器工具栏上选择 "**代码视图**"。

* 通过使用 Visual Studio 和适用于 Visual Studio 的 Azure 逻辑应用工具，你可以从 Azure 门户作为[Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)[打开和下载逻辑应用](../logic-apps/manage-logic-apps-with-visual-studio.md)。 此模板最适用于部署，并包含逻辑应用的资源定义，包括工作流本身和连接。 模板还为要在部署时使用的值声明参数。 这样，你就可以更轻松地根据需要更改逻辑应用的部署位置和方式。 若要指定部署的位置和其他必要信息，可以使用单独的参数文件。

* 如果你使用持续集成（CI）和持续交付（CD）工具（如 Azure DevOps 中的 Azure Pipelines）创建和部署逻辑应用，则可以使用这些工具将你的应用部署到另一个区域。

有关逻辑应用的部署模板的详细信息，请参阅以下主题：

* [概述：使用 Azure 资源管理器模板自动部署 Azure 逻辑应用](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [查找并打开逻辑应用，并将其从 Azure 门户下载到 Visual Studio 中](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [为 Azure 逻辑应用创建 Azure 资源管理器模板](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [部署 Azure 逻辑应用的 Azure 资源管理器模板](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>相关资源

某些 Azure 资源（例如 Azure 中的本地数据网关资源）可以存在于不同于使用这些资源的逻辑应用的区域中。 但是，其他 Azure 资源（例如链接的集成帐户）必须与逻辑应用位于同一区域。 根据你的方案，确保你的逻辑应用可以访问应用预期在同一区域中存在的资源。

例如，若要将逻辑应用链接到集成帐户，则这两个资源必须存在于同一区域中。 在灾难恢复等情况下，通常需要具有相同配置和项目的集成帐户。 在其他情况下，可能需要具有不同配置和项目的集成帐户。

Azure 逻辑应用中的自定义连接器对具有相同 Azure 订阅和相同 Azure Active Directory 租户的连接器作者和用户可见。 这些连接器在部署逻辑应用的同一区域中可用。 有关详细信息，请参阅[在组织中共享自定义连接器](https://docs.microsoft.com/connectors/custom-connectors/share)。

从 Visual Studio 获取的模板仅包含逻辑应用及其连接的资源定义。 因此，如果逻辑应用使用其他资源（例如，集成帐户和 B2B 项目，如合作伙伴、协议和架构），则必须使用 Azure 门户导出该集成帐户的模板。 此模板包括集成帐户和项目的资源定义。 但是，模板不是完全参数化的。 因此，你必须手动参数化要用于部署的值。

### <a name="export-templates-for-integration-accounts"></a>导出集成帐户的模板

1. 在[Azure 门户](https://portal.azure.com)中，找到并打开你的集成帐户。

1. 在集成帐户的菜单上的 "**设置**" 下，选择 "**导出模板**"。

1. 在工具栏上，选择 "**下载**" 并保存模板。

1. 打开并编辑模板，将部署所需的值参数化。

## <a name="next-steps"></a>后续步骤

[将 Azure 资源移到新的资源组或订阅](../azure-resource-manager/resource-group-move-resources.md)