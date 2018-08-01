---
title: 访问 Azure 逻辑应用的本地数据源 | Microsoft Docs
description: 创建并设置本地数据网关，以便从逻辑应用访问本地数据源
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: yshoukry, LADocs
ms.suite: integration
ms.openlocfilehash: 65c7e03b349314ad61fa5f1ea8322f4d1352b8e6
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145683"
---
# <a name="connect-to-data-sources-on-premises-from-azure-logic-apps-with-on-premises-data-gateway"></a>通过本地数据网关从 Azure 逻辑应用连接到本地数据源

若要从逻辑应用访问本地数据源，可在 Azure 中创建一个数据网关资源，使逻辑应用能够使用[本地连接器](../logic-apps/logic-apps-gateway-install.md#supported-connections)。 本文介绍[在本地计算机上下载并安装网关](../logic-apps/logic-apps-gateway-install.md)之后，如何创建 Azure 网关资源。 

有关如何将网关用于其他服务的信息，请参阅以下文章：

* [Microsoft Power BI 本地数据网关](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow 本地数据网关](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps 本地数据网关](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services 本地数据网关](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>先决条件

* 已[在本地计算机上下载并安装数据网关](../logic-apps/logic-apps-gateway-install.md)。

* 网关安装尚未关联到 Azure 中的网关资源。 只能将网关安装链接到一个网关资源，创建网关资源和选择网关安装时，会发生这种链接。 此链接使得网关安装不可用于其他资源。

* 登录到 Azure 门户并创建网关资源时，必须使用先前用于[安装本地数据网关](../logic-apps/logic-apps-gateway-install.md#requirements)的同一个登录帐户。
此外，必须使用用于安装网关的同一个 [Azure 订阅](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer)。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。

* 若要在 Azure 门户中创建和维护网关资源，[Windows 服务帐户](../logic-apps/logic-apps-gateway-install.md#windows-service-account)必须至少拥有“参与者”权限。 本地数据网关以 Windows 服务的形式运行，并设置为使用 `NT SERVICE\PBIEgwService` 作为 Windows 服务登录凭据。 

  > [!NOTE]
  > Windows 服务帐户与用于连接到本地数据源的帐户或用于登录到云服务的 Azure 工作或学校帐户不同。

## <a name="download-and-install-gateway"></a>下载并安装网关

在继续执行本文中的步骤之前，必须已在本地计算机上安装网关。
如果未安装，请遵循[下载并安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)的步骤。 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>为网关创建 Azure 资源

在本地计算机上安装网关后，可为网关创建 Azure 资源。 此步骤还将网关资源与 Azure 订阅相关联。

1. 登录到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>。 请务必使用安装网关时所用的同一 Azure 工作或学校电子邮件地址。

2. 在 Azure 主菜单上，选择“创建资源” > 
“集成” > “本地数据网关”。

   ![查找“本地数据网关”](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. 在“创建连接网关”页上，提供网关资源的以下信息：

   | 属性 | 说明 | 
   |----------|-------------|
   | **Name** | 网关资源的名称。 | 
   | **订阅** | Azure 订阅的名称，应是逻辑应用所在的同一订阅。 默认订阅取决于用来登录的 Azure 帐户。 | 
   | **资源组** | 用于组织相关资源的 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)的名称 | 
   | **位置** | 根据 Azure 限制，此位置必须是在[网关安装](../logic-apps/logic-apps-gateway-install.md)期间为网关云服务选择的同一区域。 <p>**注意**：请确保网关资源位置与网关云服务位置相符。 否则，网关安装可能不会显示在已安装网关列表中，因此无法在下一步中选择。 可以为网关资源和逻辑应用使用不同的区域。 | 
   | **安装名称** | 如果尚未选择网关安装，请选择前面安装的网关。 | 
   | | | 

   下面是一个示例：

   ![提供创建本地数据网关所需的详细信息](./media/logic-apps-gateway-connection/createblade.png)

4. 若要将网关资源添加到 Azure 仪表板，请选择“固定到仪表板”。 完成后，选择“创建”。

   若要随时查找或查看网关，请从 Azure 主菜单中选择“所有服务”。 
   在搜索框中，输入“本地数据网关”，然后选择“本地数据网关”。

   ![查找“本地数据网关”](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>连接到本地数据

创建网关资源并将 Azure 订阅与此资源相关联后，可以使用该网关在逻辑应用与本地数据源之间创建连接。

1. 在 Azure 门户的逻辑应用设计器中创建或打开逻辑应用。

2. 添加支持本地连接的连接器，例如 **SQL Server**。

3. 现在设置连接：

   1. 选择“通过本地数据网关连接”。 

   2. 对于“网关”，请选择前面创建的网关资源。 

      尽管网关连接位置必须与逻辑应用位于同一区域，但可以选择另一区域中的网关。

   3. 提供唯一的连接名称和其他所需信息。 

      唯一的连接名称有助于在以后轻松标识该连接，尤其是在创建多个连接的时候。 另请包括用户名的限定域（如果适用）。
   
      下面是一个示例：

      ![在逻辑应用和数据网关之间创建连接](./media/logic-apps-gateway-connection/blankconnection.png)

   4. 完成后，选择“创建”。 

网关连接现在已准备就绪，可供逻辑应用使用。

## <a name="edit-connection"></a>编辑连接

为逻辑应用创建网关连接后，可能需要在以后更新该特定连接的设置。

1. 查找网关连接：

   * 若要查找逻辑应用的所有 API 连接，请在逻辑应用菜单中的“开发工具”下，选择“API 连接”。 
   
     ![转到逻辑应用，选择“API 连接”。](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * 查找与 Azure 订阅关联的所有 API 连接： 

     * 在 Azure 主菜单中，转到“所有服务” > “Web” > “API 连接”。 
     * 或者，在 Azure 主菜单中转到“所有资源”。

2. 选择所需的网关连接，然后选择“编辑 API 连接”。

   > [!TIP]
   > 如果更新未生效，请尝试[停止网关 Windows 服务，并重新启动该服务](./logic-apps-gateway-install.md#restart-gateway)。

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>删除网关资源

要创建其他网关资源、将网关与其他资源相关联，或者移除网关资源，则可删除网关资源，不影响网关安装。 

1. 从 Azure 主菜单中转到“所有资源”。 

2. 找到并选择所需的网关资源。

3. 如果尚未选择，请在网关资源菜单中选择“本地数据网关”。 

4. 在资源工具栏上选择“删除”。

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>常见问题

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* [保护逻辑应用](./logic-apps-securing-a-logic-app.md)
* [逻辑应用的常见示例和方案](./logic-apps-examples-and-scenarios.md)
