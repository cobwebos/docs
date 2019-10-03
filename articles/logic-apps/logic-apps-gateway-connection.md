---
title: 从 Azure 逻辑应用访问本地数据源
description: 通过创建本地数据网关，从逻辑应用连接到本地数据源
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 09/01/2019
ms.openlocfilehash: d397adfb6ed2d3aef93bd40d14eb0ad199cdb90c
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309358"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到本地数据源

若要从逻辑应用中访问本地数据源，请在 Azure 门户中创建本地数据网关资源。 然后，逻辑应用可以使用[本地连接器](../connectors/apis-list.md#on-premises-connectors)。 Azure 逻辑应用支持通过网关执行的写入操作，包括插入和更新。 但是，这些操作会[限制其负载大小](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)。

本文说明在[本地计算机上下载并安装网关](../logic-apps/logic-apps-gateway-install.md)*后*如何创建 Azure 网关资源。 有关网关的详细信息，请参阅[网关的工作](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)原理。 

> [!TIP]
> 若要连接到 Azure 虚拟网络，请考虑改为创建[*集成服务环境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 

有关如何将网关用于其他服务的信息，请参阅以下文章：

* [Microsoft Power BI 本地数据网关](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow 本地数据网关](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps 本地数据网关](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services 本地数据网关](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>支持的数据源

对于 Azure 逻辑应用，本地数据网关支持以下数据源的[本地连接器](../connectors/apis-list.md#on-premises-connectors)：

* BizTalk Server 2016
* 文件系统
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

虽然网关本身不会产生额外成本，但[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)适用于这些连接器以及 Azure 逻辑应用中的其他操作。

## <a name="prerequisites"></a>先决条件

* 已在[本地计算机上安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。

* 你的[azure 帐户和 azure 订阅](../logic-apps/logic-apps-gateway-install.md#requirements)与安装本地数据网关时使用的相同。

* 你以前没有将网关安装链接到 Azure 中的其他网关资源。

  创建网关资源时，请选择要与网关资源关联的网关安装。 在创建网关资源时，无法选择已链接的网关安装。

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>创建 Azure 网关资源

在本地计算机上安装网关后，为网关创建 Azure 资源。 

1. 使用用于安装网关的同一 Azure 帐户登录到[Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户搜索框中输入 "本地数据网关"，并选择 "**本地数据**网关"。

   ![查找“本地数据网关”](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. 在 **"本地数据网关**" 下，选择 "**添加**"。

   ![添加数据网关](./media/logic-apps-gateway-connection/add-gateway.png)

1. 在 "**创建连接网关**" 下，提供网关资源的此信息。 完成操作后，选择“创建”。

   | 属性 | 描述 |
   |----------|-------------|
   | 资源名称 | 网关资源名称，只能包含字母、数字、连字符（`-`）、下划线（`_`）、括号（`(`、 `)`）和句点（`.`）。 |
   | **订阅** | 你的 Azure 订阅，该订阅必须与网关安装和逻辑应用相同。 默认订阅取决于用来登录的 Azure 帐户。 |
   | **资源组** | 要使用的[Azure 资源组](../azure-resource-manager/resource-group-overview.md) |
   | **Location** | 在[网关安装](../logic-apps/logic-apps-gateway-install.md)过程中为网关云服务选择的位置。 否则，网关安装将不会显示在 "**安装名称**" 列表中供你选择。 逻辑应用位置可能与网关资源位置不同。 |
   | **安装名称** | 如果尚未选择网关安装，请选择前面安装的网关。 之前链接的网关安装不会出现在此列表中供你选择。 |
   |||

   下面是一个示例：

   ![提供创建本地数据网关所需的详细信息](./media/logic-apps-gateway-connection/gateway-details.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>连接到本地数据

创建网关资源并将 Azure 订阅与此资源相关联后，可以使用该网关在逻辑应用与本地数据源之间创建连接。

1. 在 Azure 门户的逻辑应用设计器中创建或打开逻辑应用。

1. 添加支持本地连接的连接器，例如 **SQL Server**。

1. 选择“通过本地数据网关连接”。 

1. 对于**网关**，请选择你创建的网关资源。

   > [!NOTE]
   > "网关" 列表包括其他区域中的网关资源，因为逻辑应用的位置可能不同于网关资源的位置。

1. 提供唯一的连接名称和其他所需的信息，具体取决于要创建的连接。

   唯一的连接名称可帮助您在以后轻松找到该连接，尤其是在创建多个连接时。 另请包括用户名的限定域（如果适用）。
   
   下面是一个示例：

   ![在逻辑应用和数据网关之间创建连接](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. 完成操作后，选择“创建”。 

网关连接现在已准备就绪，可供逻辑应用使用。

## <a name="edit-connection"></a>编辑连接

若要更新网关连接的设置，可以编辑连接。

1. 若要仅查找逻辑应用的所有 API 连接，请在逻辑应用的菜单的 "**开发工具**" 下选择 " **API 连接**"。
   
   ![在逻辑应用菜单上，选择 "API 连接"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. 选择所需的网关连接，然后选择 "**编辑 API 连接**"。

   > [!TIP]
   > 如果更新不起作用，请尝试[停止并重新启动网关 Windows 服务帐户](../logic-apps/logic-apps-gateway-install.md#restart-gateway)进行网关安装。

查找与 Azure 订阅关联的所有 API 连接： 

* 在 Azure 主菜单中，转到“所有服务” > “Web” > “API 连接”。
* 或者，在 Azure 主菜单中转到“所有资源”。 将 "**类型**筛选器" 设置为 " **API 连接**"。

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>删除网关资源

若要创建其他网关资源，请将网关安装链接到不同的网关资源，或删除网关资源，而不会影响网关安装。 

1. 在 Azure 主菜单中，选择 "**所有资源**"。 找到并选择所需的网关资源。

1. 如果尚未选择，请在网关资源菜单中选择“本地数据网关”。 在 "网关资源" 工具栏上，选择 "**删除**"。

   例如：

   ![删除网关](./media/logic-apps-gateway-connection/gateway-delete.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>常见问题

**问**：在 Azure 中创建网关资源时为何看不到我的网关安装？ <br/>
**答**：此问题的可能原因如下：

* 网关安装已由 Azure 中的另一个网关资源注册并声明。 为网关安装创建网关资源后，实例列表中不会显示这些网关安装。 若要在 Azure 门户中检查网关注册，请查看所有 Azure 订阅的“本地数据网关”类型的所有 Azure 资源。

* 网关安装人员的 Azure AD 标识不同于登录 Azure 门户的人员。 请检查你是否使用了用于安装网关的同一标识登录。

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>后续步骤

* [保护逻辑应用](./logic-apps-securing-a-logic-app.md)
* [逻辑应用的常见示例和方案](./logic-apps-examples-and-scenarios.md)
