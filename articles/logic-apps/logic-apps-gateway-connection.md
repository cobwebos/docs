---
title: "访问 Azure 逻辑应用的本地数据源 | Microsoft Docs"
description: "设置本地数据网关，以便从逻辑应用访问本地数据源"
keywords: "访问数据, 本地, 数据传输, 加密, 数据源"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/13/2017
ms.author: LADocs; dimazaid; estfan
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 4744405b2c294be564dedee308b4cba95cfcc3c6
ms.contentlocale: zh-cn
ms.lasthandoff: 09/15/2017

---
# <a name="access-data-sources-on-premises-from-logic-apps-with-the-on-premises-data-gateway"></a>通过本地数据网关，从逻辑应用访问本地数据源

要从逻辑应用访问本地数据源，请设置一个本地数据网关，以便逻辑应用将其与支持的连接器配合使用。 该网关充当一个桥梁，在本地数据源和逻辑应用之间进行快速的数据传输和加密。 网关通过 Azure 服务总线中继来自加密频道上的本地源的数据。 所有流量最初都是网关代理的安全出站流量。 详细了解[数据网关的工作原理](logic-apps-gateway-install.md#gateway-cloud-service)。 

网关支持连接到以下本地数据源：

*   BizTalk Server 2016
*   DB2  
*   文件系统
*   Informix
*   MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   SAP Application Server 
*   SAP Message Server
*   SharePoint
*   SQL Server
*   Teradata

以下步骤演示了如何设置适用于逻辑应用的本地数据网关。 有关受支持连接器的详细信息，请参阅[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)。 

有关如何将网关用于其他服务的信息，请参阅以下文章：

*   [Microsoft Power BI 本地数据网关](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Azure Analysis Services 本地数据网关](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow 本地数据网关](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Microsoft PowerApps 本地数据网关](https://powerapps.microsoft.com/tutorials/gateway-management/)

## <a name="requirements"></a>要求

* 必须已[在本地计算机上安装数据网关](logic-apps-gateway-install.md)。

* 登录 Azure 门户时，必须使用用于[安装本地数据网关](logic-apps-gateway-install.md#requirements)的同一工作或学校帐户。 登录帐户还必须具有 Azure 订阅，以便在 Azure 门户中为网关安装创建网关资源时使用。

* 网关安装不能已被 Azure 网关资源声明。 只能将网关安装关联到一个 Azure 网关资源。 声明发生在创建网关资源的时候，一旦声明，安装就不能供其他资源使用。

## <a name="set-up-the-data-gateway-connection"></a>设置数据网关连接

### <a name="1-install-the-on-premises-data-gateway"></a>1.安装本地数据网关

请执行[安装本地数据网关的步骤](logic-apps-gateway-install.md)（如果尚未安装）。 在继续执行其他步骤之前，请确保已在本地计算机上安装数据网关。

<a name="create-gateway-resource"></a>
### <a name="2-create-an-azure-resource-for-the-on-premises-data-gateway"></a>2.创建本地数据网关的 Azure 资源

在本地计算机上安装网关以后，必须在 Azure 中以资源形式创建数据网关。 此步骤还将网关资源与 Azure 订阅相关联。

1. 登录 [Azure 门户](https://portal.azure.com "Azure portal")。 请确保所使用的 Azure 工作或学校电子邮件地址是曾经用于安装网关的。

2. 在 Azure 中的左菜单上，选择“新建” > “企业集成” > “本地数据网关”，如下所示：

   ![查找“本地数据网关”](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. 在“创建连接网关”边栏选项卡上提供以下详细信息，创建数据网关资源：

    * 名称：输入网关资源的名称。 

    * 订阅：选择要与网关资源关联的 Azure 订阅。 
    此订阅应与逻辑应用的订阅相同。
   
      默认订阅取决于用来登录的 Azure 帐户。

    * **资源组**：创建资源组或选择现有资源组，以便部署网关资源。 
    资源组用于将相关的 Azure 资产作为集合管理。

    * **位置**：根据 Azure 限制，此位置必须是在[网关安装](logic-apps-gateway-install.md)期间为网关云服务选择的同一区域。 

      > [!NOTE]
      > 请确保网关资源位置与网关云服务位置相符。 否则，网关安装可能不会显示在已安装网关列表中，因此无法在下一步中选择。
      > 
      > 可以为网关资源和逻辑应用使用不同的区域。

    * **安装名称**：如果尚未选择网关安装，请选择之前安装的网关。 

    要将网关资源添加到 Azure 仪表板中，请选择“固定到仪表板”。 
    完成后，选择“创建”。

    例如：

    ![提供创建本地数据网关所需的详细信息](./media/logic-apps-gateway-connection/createblade.png)

    若要随时查找或查看数据网关，请从 Azure 左主菜单中，转到“更多服务”>“企业集成”>“本地数据网关”。

    ![转到“更多服务”、“企业集成”、“本地数据网关”](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>
### <a name="3-connect-your-logic-app-to-the-on-premises-data-gateway"></a>3.将逻辑应用连接到本地数据网关

创建数据网关资源并将 Azure 订阅与该资源关联以后，即可在逻辑应用和数据网关之间创建一个连接。

> [!NOTE]
> 网关连接位置必须与逻辑应用位于同一区域，但是可以使用位于另一区域的数据网关。

1. 在 Azure 门户的逻辑应用设计器中创建或打开逻辑应用。

2. 添加支持本地连接的连接器，例如 SQL Server。

3. 按照显示的顺序，选择“通过本地数据网关连接”，提供唯一的连接名称和必填信息，并选择要使用的数据网关资源。 完成后，选择“创建”。

   > [!TIP]
   > 唯一的连接名称有助于在以后轻松标识该连接，尤其是在创建多个连接的时候。 另请包括用户名的限定域（如果适用）。 

   ![在逻辑应用和数据网关之间创建连接](./media/logic-apps-gateway-connection/blankconnection.png)

恭喜，网关连接现在已准备就绪，可供逻辑应用使用了。

## <a name="edit-your-gateway-connection-settings"></a>编辑网关连接设置

为逻辑应用创建网关连接后，可能需要在以后更新该特定连接的设置。

1. 若要查找网关连接，请执行以下操作：

   * 在“逻辑应用”边栏选项卡的“开发工具”下，选择“API 连接”。 
   
     “API 连接”窗格显示与逻辑应用关联的所有 API 连接，包括网关连接。

     ![转到逻辑应用，选择“API 连接”。](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * 或者，从 Azure 左侧主菜单依次转到“更多服务”>“Web + 移动”>“API 连接”（适用于所有 API 连接，包括与 Azure 订阅关联的网关连接）。 

   * 或者，在 Azure 左主菜单上转到“所有资源”（适用于所有 API 连接，包括与 Azure 订阅关联的网关连接）。

2. 选择要查看或编辑的网关连接，并选择“编辑 API 连接”。

   > [!TIP]
   > 如果更新未生效，请尝试[停止网关 Windows 服务，并重新启动该服务](./logic-apps-gateway-install.md#restart-gateway)。

<a name="change-delete-gateway-resource"></a>
## <a name="switch-or-delete-your-on-premises-data-gateway-resource"></a>切换或删除本地数据网关资源

要创建其他网关资源、将网关与其他资源相关联，或者移除网关资源，则可删除网关资源，不影响网关安装。 

1. 从 Azure 左主菜单中，转到“所有资源”。 
2. 找到并选择数据网关资源。
3. 选择“本地数据网关”，并在资源工具栏上，选择“删除”。

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>常见问题

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>后续步骤

* [保护逻辑应用](./logic-apps-securing-a-logic-app.md)
* [逻辑应用的常见示例和方案](./logic-apps-examples-and-scenarios.md)

