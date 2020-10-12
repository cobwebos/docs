---
title: 跨订阅、资源组或区域移动逻辑应用
description: 将逻辑应用或集成帐户迁移到其他 Azure 订阅、资源组或位置（区域）
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: aca2c51ff14b99ba41b159cf32e59dc861de7a53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87826205"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>将逻辑应用资源移到其他 Azure 资源组、区域或订阅

若要将逻辑应用或相关资源迁移到另一个 Azure 资源组、区域或订阅，可以使用多种方式，例如使用 Azure 门户、Azure PowerShell、Azure CLI 和 REST API。 在移动资源之前，请查看以下注意事项： 

* 只能在 Azure 资源组或订阅之间移动[特定的逻辑应用资源类型](../azure-resource-manager/management/move-support-resources.md#microsoftlogic)。

* 检查可在 Azure 订阅中和每个 Azure 区域中使用的逻辑应用资源数[限制](../logic-apps/logic-apps-limits-and-config.md)。 当不同订阅或资源组所在的区域相同时，这些限制会影响到能否移动特定的资源类型。 例如，对于每个 Azure 区域中的每个 Azure 订阅，只能使用一个免费层集成帐户。

* 移动资源时，Azure 将创建新的资源 ID。 因此，请确保改用新的 ID，并更新与所要移动的资源关联的任何脚本或工具。

* 在订阅、资源组或区域之间迁移逻辑应用之后，必须重新创建或重新授权任何需要 Open Authentication (OAuth) 的连接。

* 只能将 [integration service 环境 (ISE) ](connect-virtual-network-vnet-isolated-environment-overview.md) 转移到同一 azure 区域或 azure 订阅中存在的另一个资源组。 不能将 ISE 移到另一个 Azure 区域或 Azure 订阅中存在的资源组。 此外，在此类移动之后，必须在逻辑应用工作流、集成帐户、连接等中更新对 ISE 的所有引用。

## <a name="prerequisites"></a>先决条件

* 创建要移动的逻辑应用或集成帐户时所用的同一个 Azure 订阅

* 拥有移动和设置所需资源的资源所有者权限。 详细了解 azure [RBAC)  (基于角色的访问控制 ](../role-based-access-control/built-in-roles.md#owner)。

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>在订阅之间移动资源

若要将某个资源（例如逻辑应用或集成帐户）移到另一个 Azure 订阅，可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API。 以下步骤使用 Azure 门户。当资源的区域相同时，可以使用此方法。 有关其他步骤和常规准备工作，请参阅[将资源移到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。

1. 在 [Azure 门户](https://portal.azure.com)中，找到并选择要移动的逻辑应用资源。

1. 在该资源的“概述”页上的“订阅”旁边，选择“更改”链接。  

1. 在“移动资源”页上，选择要移动的逻辑应用资源和任何相关资源。

1. 在“订阅”列表中选择目标订阅。

1. 在“资源组”列表中选择目标资源组。 若要创建不同的资源组，请选择“创建新组”。

1. 为了确认你已知道在使用新资源 ID 更新与所要移动的资源关联的任何脚本或工具之前，这些脚本或工具不会正常运行，请选中确认框，然后选择“确定”。

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>在资源组之间移动资源

若要将资源（例如逻辑应用、集成帐户或 [integration service 环境） (ISE) ](connect-virtual-network-vnet-isolated-environment-overview.md)移动到另一个 Azure 资源组，可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API。 以下步骤使用 Azure 门户。当资源的区域相同时，可以使用此方法。 有关其他步骤和常规准备工作，请参阅[将资源移到新的资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。

在组之间实际移动资源之前，可以测试是否能够成功地将资源移到另一个组。 有关详细信息，请参阅[验证移动](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move)。

1. 在 [Azure 门户](https://portal.azure.com)中，找到并选择要移动的逻辑应用资源。

1. 在该资源的“概述”页上的“资源组”旁边，选择“更改”链接。  

1. 在“移动资源”页上，选择要移动的逻辑应用资源和任何相关资源。

1. 在“资源组”列表中选择目标资源组。 若要创建不同的资源组，请选择“创建新组”。

1. 为了确认你已知道在使用新资源 ID 更新与所要移动的资源关联的任何脚本或工具之前，这些脚本或工具不会正常运行，请选中确认框，然后选择“确定”。

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>在区域之间移动资源

若要将逻辑应用移到不同的区域，可用的选项取决于逻辑应用的创建方式。 根据选择的选项，必须在逻辑应用中重新创建或重新授权连接。

* 在 Azure 门户中，在新区域中重新创建逻辑应用，并重新配置工作流设置。 为了节省时间，可将源应用中基础工作流定义和连接复制到目标应用。 若要查看逻辑应用附带的“代码”，请在逻辑应用设计器工具栏上选择“代码视图”。

* 使用 Visual Studio 和适用于 Visual Studio 的 Azure 逻辑应用工具，可以在 Azure 门户中以 [Azure 资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)的形式[打开和下载逻辑应用](../logic-apps/manage-logic-apps-with-visual-studio.md)。 此模板基本上已做好部署的准备，包含逻辑应用的资源定义（包括工作流本身和连接）。 该模板还声明了在部署时要使用的值的参数。 这样，你便可以根据自己的需求，更轻松地更改逻辑应用的部署位置和方式。 若要指定部署的位置和其他必要信息，可以使用单独的参数文件。

* 如果使用持续集成 (CI) 和持续交付 (CD) 工具（例如 Azure DevOps 中的 Azure Pipelines）创建并部署了逻辑应用，则可以使用这些工具将应用部署到另一区域。

有关逻辑应用部署模板的详细信息，请参阅以下主题：

* [概述：使用 Azure 资源管理器模板将 Azure 逻辑应用部署自动化](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [在 Azure 门户中查找、打开逻辑应用并将其下载到 Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [为 Azure 逻辑应用创建 Azure 资源管理器模板](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [为 Azure 逻辑应用部署 Azure 资源管理器模板](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>相关资源

某些 Azure 资源（例如 Azure 中的本地数据网关资源）所在的区域可能不同于使用这些资源的逻辑应用所在的区域。 但是，其他 Azure 资源（例如链接的集成帐户）必须与逻辑应用位于同一区域。 根据具体的方案，确保逻辑应用可以访问预期位于同一区域中的资源。

例如，若要将某个逻辑应用链接到某个集成帐户，则这两个资源必须位于同一区域。 在灾难恢复等方案中，你通常希望集成帐户使用相同配置的和项目。 在其他方案中，你可能需要让集成帐户使用不同的配置和项目。

具有相同 Azure 订阅和相同 Azure Active Directory 租户的连接器作者与用户可以看到 Azure 逻辑应用中的自定义连接器。 在部署逻辑应用的同一区域中，可以使用这些连接器。 有关详细信息，请参阅[在组织中共享自定义连接器](/connectors/custom-connectors/share)。

从 Visual Studio 获取的模板仅包含逻辑应用及其连接的资源定义。 因此，如果逻辑应用使用其他资源（例如集成帐户，以及合作伙伴、协议和架构等 B2B 项目），则必须使用 Azure 门户导出该集成帐户的模板。 此模板包含集成帐户和项目的资源定义。 但是，该模板尚未完全参数化。 因此，必须手动参数化要用于部署的值。

### <a name="export-templates-for-integration-accounts"></a>导出集成帐户的模板

1. 在 [Azure 门户](https://portal.azure.com)中，找到并打开你的集成帐户。

1. 在集成帐户菜单中的“设置”下，选择“导出模板”。 

1. 在工具栏上选择“下载”，然后保存该模板。

1. 打开并编辑该模板，以参数化所需的部署值。

## <a name="next-steps"></a>后续步骤

[将 Azure 资源移到新资源组或订阅中](../azure-resource-manager/management/move-resource-group-and-subscription.md)
