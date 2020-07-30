---
title: 访问本地数据源
description: 通过在 Azure 中创建数据网关资源从 Azure 逻辑应用连接到本地数据源
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, divswa, logicappspm
ms.topic: article
ms.date: 07/28/2020
ms.openlocfilehash: 675d4bdb0b8c0aa8e034d5a85dc027f642705fa9
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386174"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到本地数据源

在[本地计算机上安装本地*数据网关*](../logic-apps/logic-apps-gateway-install.md)并在从逻辑应用访问本地数据源之前，需要在 Azure 中创建网关资源，以便安装网关。 然后，你可以在要用于 Azure 逻辑应用中可用的[本地连接器](../connectors/apis-list.md#on-premises-connectors)的触发器和操作中选择此网关资源。

本文介绍如何为以前[安装在本地计算机上的网关](../logic-apps/logic-apps-gateway-install.md)创建 Azure 网关资源。 有关网关的详细信息，请参阅[网关的工作原理](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)。

> [!TIP]
> 若要在不使用网关的情况下直接访问 Azure 虚拟网络中的本地资源，请考虑改为创建[*集成服务环境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 

有关如何将网关用于其他服务的信息，请参阅以下文章：

* [Microsoft Power Automate 本地数据网关](/power-automate/gateway-reference)
* [Microsoft Power BI 本地数据网关](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps 本地数据网关](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 本地数据网关](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>支持的数据源

在 Azure 逻辑应用中，本地数据网关支持以下数据源的[本地连接器](../connectors/apis-list.md#on-premises-connectors)：

* BizTalk Server 2016
* 文件系统
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle 数据库
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Azure 逻辑应用支持通过数据网关进行读取和写入操作。 但是，这些操作存在[有效负载大小限制](/data-integration/gateway/service-gateway-onprem#considerations)。 虽然网关本身不额外收费，但[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)适用于这些连接器以及 Azure 逻辑应用中的其他操作。

## <a name="prerequisites"></a>先决条件

* 已经[在本地计算机上安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 此网关安装必须存在，然后才能创建链接到此安装的网关资源。

* 你具有与用于网关安装[相同的 Azure 帐户和订阅](../logic-apps/logic-apps-gateway-install.md#requirements)。 此 Azure 帐户必须仅属于单个[Azure Active Directory （Azure AD）租户或目录](../active-directory/fundamentals/active-directory-whatis.md#terminology)。 需要使用相同的 Azure 帐户和订阅在 Azure 中创建网关资源，因为只有网关管理员可以在 Azure 中创建网关资源。 当前不支持服务主体。

  * 当你在 Azure 中创建网关资源时，你可以选择网关安装，以链接到你的网关资源和仅该网关资源。 每个网关资源只能链接到一个网关安装。 不能选择已与另一网关资源关联的网关安装。
  
  * 逻辑应用和网关资源不必存在于同一 Azure 订阅中。 如果你具有订阅访问权限，则可以在可访问本地数据源的触发器和操作中选择包含网关资源的其他 Azure 订阅。

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>创建 Azure 网关资源

在本地计算机上安装网关后，请为网关创建 Azure 资源。

1. 使用安装网关时所使用的 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户搜索框中输入“本地数据网关”，然后选择“本地数据网关”。

   ![查找“本地数据网关”](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. 在“本地数据网关”下，选择“添加”。 

   ![为数据网关添加新的 Azure 资源](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. 在“创建连接网关”下，提供网关资源的以下信息。 完成操作后，选择“创建”。

   | 属性 | 说明 |
   |----------|-------------|
   | 资源名称 | 为网关资源提供一个名称，其中只包含字母、数字、连字符 (`-`)、下划线 (`_`)、括号（`(`、`)`）或句点 (`.`)。 |
   | **订阅** | 选择曾用于网关安装的 Azure 帐户的 Azure 订阅。 默认订阅取决于用来登录的 Azure 帐户。 |
   | **资源组** | 要使用的 [Azure 资源组](../azure-resource-manager/management/overview.md) |
   | **位置** | 在[网关安装](../logic-apps/logic-apps-gateway-install.md)期间为网关云服务选择的区域或位置。 否则，网关安装不会显示在“安装名称”列表中。 逻辑应用位置可能不同于网关资源位置。 |
   | **安装名称** | 选择一个网关安装，该安装仅在以下条件满足的情况下显示在列表中： <p><p>- 网关安装使用的区域与要创建的网关资源所在的区域相同。 <br>- 网关安装未关联到另一 Azure 网关资源。 <br>- 网关安装关联到要用来创建网关资源的 Azure 帐户。 <br>-你的 Azure 帐户属于单个[Azure Active Directory （Azure AD）租户或目录](../active-directory/fundamentals/active-directory-whatis.md#terminology)，并且与用于网关安装的帐户相同。 <p><p>有关详细信息，请参阅[常见问题解答](#faq)部分。 |
   |||

   以下示例演示的网关安装位于你的网关资源所在的区域，并关联到同一 Azure 帐户：

   ![提供创建数据网关资源所需的详细信息](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>连接到本地数据

创建网关资源并将 Azure 订阅与此资源相关联后，可以使用该网关在逻辑应用与本地数据源之间创建连接。

1. 在 Azure 门户的逻辑应用设计器中创建或打开逻辑应用。

1. 添加支持本地连接的连接器，例如 **SQL Server**。

1. 选择“通过本地数据网关连接”。

1. 在“网关”下，从“订阅”列表中选择 你的 Azure 订阅，该订阅有你需要的网关资源。

   如果你具有订阅访问权限，你可以从与不同的网关资源关联的不同 Azure 订阅中进行选择。 逻辑应用和网关资源不必存在于同一 Azure 订阅中。

1. 从“连接网关”列表中选择所需网关资源，该列表会显示所选订阅中的可用网关资源。 每个网关资源关联到单个网关安装。

   > [!NOTE]
   > 网关列表包含其他区域的网关资源，因为逻辑应用的位置可能不同于网关资源的位置。 

1. 提供唯一的连接名称和其他所需信息，具体取决于要创建的连接。

   唯一的连接名称有助于在以后轻松查找该连接，尤其是在创建多个连接的情况下。 另请包括用户名的限定域（如果适用）。

   以下是示例：

   ![在逻辑应用和数据网关之间创建连接](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. 完成操作后，选择“创建”。

网关连接现在已准备就绪，可供逻辑应用使用。

## <a name="edit-connection"></a>编辑连接

若要更新网关连接的设置，可以编辑连接。

1. 若要查找你的逻辑应用的所有 API 连接，请在逻辑应用菜单中的“开发工具”下，选择“API 连接”。 

   ![在逻辑应用菜单上，选择“API 连接”](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. 选择所需的网关连接，然后选择“编辑 API 连接”。

   > [!TIP]
   > 如果更新未生效，请尝试针对网关安装[停止网关 Windows 服务帐户，然后重启该帐户](../logic-apps/logic-apps-gateway-install.md#restart-gateway)。

查找与 Azure 订阅关联的所有 API 连接：

* 在 Azure 门户菜单中，选择“所有服务” > “Web” > “API 连接”。
* 或者，在 Azure 门户菜单中，选择“所有资源”。 将“类型”筛选器设置为“API 连接”。

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>删除网关资源

若要创建其他网关资源、将网关安装与其他网关资源相关联，或者移除网关资源，则可删除网关资源，不影响网关安装。

1. 在 Azure 门户菜单中选择“所有资源”，或从任何页面搜索并选择“所有资源”。 找到并选择所需的网关资源。

1. 如果尚未选择，请在网关资源菜单中选择“本地数据网关”。 在网关资源工具栏上，选择“删除”。

   例如：

   ![删除 Azure 中的网关资源](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>常见问题

**问**：在 Azure 中创建我的网关资源时，为何不显示我的网关安装？ <br/>
**答**：此问题的可能原因如下：

* 你的 Azure 帐户不同于在本地计算机上安装网关所用的帐户。 检查是否已使用用于网关安装的相同标识登录到 Azure 门户。 仅网关管理员可以在 Azure 中创建网关资源。 当前不支持服务主体。

* 你的 Azure 帐户不只属于单个[Azure AD 租户或目录](../active-directory/fundamentals/active-directory-whatis.md#terminology)。 检查是否使用的是在网关安装过程中使用的 Azure AD 租户或目录。

* 你的网关资源和网关安装在同一区域中不存在。 但是，逻辑应用的位置可能与网关资源位置不同。

* 你的网关安装已与另一网关资源关联。 每个网关资源只能链接到一个网关安装，只能链接到一个 Azure 帐户和订阅。 因此，不能选择已与另一网关资源关联的网关安装。 这些安装不会显示在“安装名称”列表中。

  若要查看 Azure 门户中的网关注册，请在*所有*azure 订阅中查找具有**本地数据网关**资源类型的所有 azure 资源。 若要取消关联其他网关资源中的网关安装，请参阅[删除网关资源](#change-delete-gateway-resource)。

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>后续步骤

* [保护逻辑应用](./logic-apps-securing-a-logic-app.md)
* [逻辑应用的常见示例和方案](./logic-apps-examples-and-scenarios.md)
