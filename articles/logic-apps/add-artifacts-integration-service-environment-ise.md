---
title: 向集成服务环境添加资源
description: 将逻辑应用、集成帐户、自定义连接器和托管连接器添加到集成服务环境 （ISE）
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164873"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>在 Azure 逻辑应用中向集成服务环境 （ISE） 添加资源

创建[集成服务环境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)后，添加逻辑应用、集成帐户和连接器等资源，以便它们可以访问 Azure 虚拟网络中的资源。 例如，创建 ISE 后可用的托管 ISE 连接器不会自动显示在逻辑应用设计器中。 在使用这些 ISE 连接器之前，必须手动[将这些连接器添加并部署到 ISE，](#add-ise-connectors-environment)以便它们显示在逻辑应用设计器中。

> [!IMPORTANT]
> 对于逻辑应用和集成帐户在 ISE 中协同工作，两者必须使用*与其位置相同的 ISE。*

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 您为运行逻辑应用而创建的 ISE。 如果没有 ISE，[请先创建 ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

* 要创建、添加或更新部署到 ISE 的资源，需要为该 ISE 上的所有者或参与者角色分配，或者通过与 ISE 关联的 Azure 订阅或 Azure 资源组继承权限。 对于没有所有者、参与者或继承权限的用户，可以为其分配集成服务环境参与者角色或集成服务环境开发人员角色。 有关基于角色的访问控制 （RBAC） 的详细信息，请参阅[什么是 Azure 资源的基于角色的访问控制 （RBAC）？](../role-based-access-control/overview.md)

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>创建逻辑应用

要生成在集成服务环境 （ISE） 中运行的逻辑应用，请按照以下步骤操作：

1. 查找并打开 ISE（如果尚未打开）。 在 ISE 菜单中，**在"设置"** 下，选择 **"逻辑应用** > **添加**"。

   ![向 ISE 添加新逻辑应用](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. 提供有关要创建的逻辑应用的信息，例如：

   ![选择集成服务环境](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | properties | 必选 | 描述 |
   |----------|----------|-------------|
   | **名称** | 是 | 要创建的逻辑应用的名称 |
   | **订阅** | 是 | 要使用的 Azure 订阅的名称 |
   | **资源组** | 是 | 要使用的 Azure 资源组（新资源或现有）的名称 |
   | **位置** | 是 | 在**集成服务环境中**，选择要使用的 ISE（如果尚未选择）。 <p><p> **重要提示**：要将逻辑应用与集成帐户一起使用，两者都必须使用相同的 ISE。 |
   ||||

1. 完成操作后，选择“创建”****。

1. 继续[以通常的方式创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

   有关触发器和操作的工作方式以及与多租户逻辑应用服务相比使用 ISE 时如何标记它们的差异，请参阅[ISE 概述中的隔离与多租户](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)。

1. 要管理 ISE 中的逻辑应用和 API 连接，请参阅[管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>创建集成帐户

根据创建时选择的[ISE SKU，](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)您的 ISE 包括特定的集成帐户使用情况，无需额外费用。 存在于集成服务环境 （ISE） 中的逻辑应用只能引用存在于同一 ISE 中的集成帐户。 因此，对于集成帐户要使用 ISE 中的逻辑应用，集成帐户和逻辑应用必须使用与其位置*相同的环境*。 有关集成帐户和 ISE 的详细信息，请参阅[ISE 的集成帐户](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)。

要创建使用 ISE 的集成帐户，请按照以下步骤操作：

1. 查找并打开 ISE（如果尚未打开）。 在 ISE 菜单中，**在"设置"** 下，选择 **"添加集成帐户** > **Add**"。

   ![向 ISE 添加新集成帐户](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. 提供有关要创建的逻辑应用的信息，例如：

   ![选择集成服务环境](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | properties | 必选 | 描述 |
   |----------|----------|-------------|
   | **名称** | 是 | 要创建的集成帐户的名称 |
   | **订阅** | 是 | 要使用的 Azure 订阅的名称 |
   | **资源组** | 是 | 要使用的 Azure 资源组（新资源或现有）的名称 |
   | **定价层** | 是 | 用于集成帐户的定价层 |
   | **位置** | 是 | 在**集成服务环境中**，选择逻辑应用使用的相同 ISE（如果尚未选择）。 <p><p> **重要提示**：要将集成帐户与逻辑应用一起使用，两者都必须使用相同的 ISE。 |
   ||||

1. 完成操作后，选择“创建”****。

1. [以通常的方式将逻辑应用链接到您的集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。

1. 继续将资源添加到您的集成帐户，如[贸易伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)和[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)。

1. 要管理 ISE 中的集成帐户，请参阅[管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>添加 ISE 连接器

创建 ISE 后可用的 Microsoft 托管连接器不会自动显示在逻辑应用设计器上的连接器选取器中。 在使用这些 ISE 连接器之前，必须手动将这些连接器添加并部署到 ISE，以便它们显示在逻辑应用设计器中。

1. 在 ISE 菜单上，**在"设置"** 下，选择 **"托管连接器**"。 在工具栏中选择“添加”。****

   ![查看托管连接器](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. 在"**添加新托管连接器**"窗格中，打开 **"查找连接器**"列表。 选择要使用的 ISE 连接器，但尚未在 ISE 中部署。 选择 **“创建”**。

   ![选择要在 ISE 中部署的 ISE 连接器](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   只有符合条件但尚未部署到 ISE 的 ISE 连接器才能选择。 已部署在 ISE 中的连接器似乎无法选择。

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>创建自定义连接器

要在 ISE 中使用自定义连接器，请直接从 ISE 内部创建这些自定义连接器。

1. 查找并打开 ISE（如果尚未打开）。 在 ISE 菜单中，在 **"设置"** 下，选择 **"自定义连接器** > **添加**"。

   ![创建自定义连接器](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. 提供用于自定义连接器的名称、Azure 订阅和 Azure 资源组（新或现有）。

1. 在 **"位置**"列表中，在 **"集成服务环境**"部分下，选择逻辑应用使用的相同 ISE，然后选择 **"创建**"，例如：

   ![选择集成服务环境](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. 选择新的自定义连接器，然后选择 **"编辑**"，例如：

   ![选择和编辑自定义连接器](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. 继续以通常的方式从[OpenAPI 定义](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition)或[SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)创建连接器。

1. 要管理 ISE 中的自定义连接器，请参阅[管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md)。

## <a name="next-steps"></a>后续步骤

* [管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md)
