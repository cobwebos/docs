---
title: 在 Azure 逻辑应用中将项目添加到 integration service 环境（ISEs）
description: 将逻辑应用、自定义连接器和集成帐户添加到 integration service 环境（ISE），以访问 Azure 虚拟网络（Vnet），同时保持专用并独立于公用或 "全局" Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 6327f0c14b46ceaadbf7adaa58a70c32b39b7c2a
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960492"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>在 Azure 逻辑应用中将项目添加到 integration service 环境（ISE）

创建[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)后，添加逻辑应用、集成帐户和连接器等项目，以便它们可以访问 Azure 虚拟网络中的资源。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 你创建的用于运行逻辑应用的 ISE。 如果没有 ISE，请[先创建 ise](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>创建逻辑应用

若要生成在 integration service 环境（ISE）中运行的逻辑应用，请执行以下步骤：

1. 找到并打开 ISE （如果尚未打开）。 在 ISE 菜单的 "**设置**" 下，选择 "**逻辑应用** > **添加**"。

   ![向 ISE 中添加新的逻辑应用](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   -或-

   在 Azure 主菜单中，依次选择“创建资源” > “集成” > “逻辑应用”。

1. 提供要用于逻辑应用的名称、Azure 订阅和 Azure 资源组（新的或现有的）。

1. 从 "**位置**" 列表中的 " **Integration service 环境**" 部分，选择你的 ISE，例如：

   ![选择集成服务环境](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > 如果要将逻辑应用与集成帐户一起使用，则这些逻辑应用和集成帐户必须使用相同的 ISE。

1. 继续[以常规方式创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

   有关在使用 ISE 与全局逻辑应用服务相比，触发器和操作的工作方式以及如何标记它们的区别，请参阅[ISE 概述中的隔离与全局](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)。

1. 若要在 ISE 中管理逻辑应用和 API 连接，请参阅[管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>创建集成帐户

基于在创建时选择的[ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) ，ISE 包含特定的集成帐户使用情况，无需额外付费。 集成服务环境（ISE）中存在的逻辑应用只能引用位于同一 ISE 中的集成帐户。 因此，若要使集成帐户在 ISE 中使用逻辑应用，集成帐户和逻辑应用必须使用与其位置相同的*环境*。 有关集成帐户和 ISEs 的详细信息，请参阅[integration accounts WITH ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)。

若要创建使用 ISE 的集成帐户，请遵循以下步骤：

1. 找到并打开 ISE （如果尚未打开）。 在 ISE 菜单的 "**设置**" 下，选择 "**集成帐户**" @no__t "**添加**"。

   ![向 ISE 中添加新的集成帐户](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   -或-

   在 Azure 主菜单中，选择 "**创建资源** > **集成**@no__t 集成**帐户**"。

1. 提供要用于集成帐户的名称、Azure 订阅、Azure 资源组（新的或现有的）和定价层。

1. 从 "**位置**" 列表中的 " **Integration service 环境**" 部分，选择逻辑应用使用的相同 ISE，例如：

   ![选择集成服务环境](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [以常规方式将逻辑应用链接到集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。

1. 继续操作，方法是将项目添加到集成帐户，如[贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)和[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)。

1. 若要管理 ISE 中的集成帐户，请参阅[管理 integration service 环境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>添加 ISE 连接器

可以添加可在 ISE 中使用但未部署在 ISE 中的 Microsoft 托管连接器。

1. 在 ISE 菜单上的 "**设置**" 下，选择 "**托管连接器**"。 在工具栏中选择“添加”。

   ![查看托管连接器](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. 在 "**添加新的托管连接器**" 窗格中，打开 "**查找连接器**" 列表。 如果需要可用的连接器，请选择该连接器，然后选择 "**创建**"。

   此列表仅显示符合条件但未部署在 ISE 中的连接器。 你的 ISE 中已经部署的连接器无法选择。

   ![选择合格的连接器](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>创建自定义连接器

若要在 ISE 中使用自定义连接器，请直接在 ISE 中创建这些自定义连接器。

1. 找到并打开 ISE （如果尚未打开）。 在 ISE 菜单的 "**设置**" 下，选择 "**自定义连接器** > **添加**"。

   ![创建自定义连接器](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. 提供要用于自定义连接器的名称、Azure 订阅和 Azure 资源组（新的或现有的）。

1. 从 "**位置**" 列表中的 " **Integration service 环境**" 部分，选择逻辑应用使用的相同 ISE，然后选择 "**创建**"，例如：

   ![选择集成服务环境](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. 选择新的自定义连接器，然后选择 "**编辑**"，例如：

   ![选择和编辑自定义连接器](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. 通过从[OpenAPI 定义](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition)或[SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)按常规方式创建连接器继续。

1. 若要管理 ISE 中的自定义连接器，请参阅[管理 integration service 环境](../logic-apps/ise-manage-integration-service-environment.md)。

## <a name="next-steps"></a>后续步骤

* [管理 integration service 环境](../logic-apps/ise-manage-integration-service-environment.md)
