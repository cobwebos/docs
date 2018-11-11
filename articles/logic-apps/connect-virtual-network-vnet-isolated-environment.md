---
title: 通过集成服务环境 (ISE) 从 Azure 逻辑应用连接到 Azure 虚拟网络
description: 创建集成服务环境 (ISE)，以便逻辑应用和集成帐户可以访问 Azure 虚拟网络，同时保持专用并与公共或“全局”Azure 相隔离
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/25/2018
ms.openlocfilehash: d9a849fb5556332fab39467c270360c09c774cc9
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231774"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-through-an-integration-service-environment-ise"></a>通过集成服务环境 (ISE) 从 Azure 逻辑应用连接到 Azure 虚拟网络

> [!NOTE]
> 此功能在个人预览版中提供。 若要请求访问权限，[请在此处创建加入请求](https://aka.ms/iseprivatepreview)。

对于你的逻辑应用和集成帐户需要访问 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)的集成方案，请创建一个[*集成服务环境* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，并使它成为一个专用的隔离环境，使用与公共或*全局*逻辑应用服务独立的专用存储和其他资源。 这种分离还减少了其他 Azure 租户可能对应用性能产生的影响。 可以将此 ISE 链接到你的 Azure 虚拟环境，后者然后会将逻辑应用服务部署到你的虚拟网络中。 创建逻辑应用或集成帐户时，请选择此 ISE 作为其位置。 然后，逻辑应用或集成帐户可以直接访问虚拟网络中的虚拟机 (VM)、服务器、系统和服务等资源。 

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

本文将介绍如何执行以下任务：

* 在 Azure 虚拟网络中设置权限，使专用逻辑应用实例能够访问你的虚拟网络。

* 创建集成服务环境 (ISE)。 

* 创建可在 ISE 中运行的逻辑应用。 

* 在 ISE 中创建逻辑应用的集成帐户。

有关集成服务环境的详细信息，请参阅[从隔离的 Azure 逻辑应用访问 Azure 虚拟网络资源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 如果你没有 Azure 虚拟网络，请了解如何[创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md)。 

  > [!IMPORTANT]
  > 尽管不需要使用 Azure 虚拟网络来创建环境，但在创建环境时，只能选择一个虚拟网络作为该环境的对等方。 

* 若要为逻辑应用提供对 Azure 虚拟网络的直接访问权限，请[设置基于角色的访问控制 (RBAC) 权限](#vnet-access)，使逻辑应用服务有权访问你的虚拟网络。 

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>设置虚拟网络权限

创建集成服务环境时，可以选择一个 Azure 虚拟网络作为环境的对等方。 但是，只能在创建环境时执行此步骤（即对等互连）。 这种关系可使逻辑应用服务直接连接到该虚拟网络中的资源，并为环境授予这些资源的访问权限。 

在选择虚拟网络之前，必须在虚拟网络中设置基于角色的访问控制 (RBAC) 权限。 若要完成此任务，必须将特定的角色分配到 Azure 逻辑应用服务。

1. 在 [Azure 门户](https://portal.azure.com)中，找到并选择自己的虚拟网络。 

1. 在虚拟网络的菜单中，选择“访问控制(IAM)”。 

1. 在“访问控制”下，选择“角色分配”（如果尚未选择）。 在“角色分配”工具栏上，选择“添加”。 

   ![添加角色分配](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. 在“添加权限”窗格中，为 Azure 逻辑应用服务设置此表中的每个角色。 分配完每个角色后，请务必选择“保存”：

   | 角色 | 将访问权限分配到 | Select | 
   |------|------------------|--------|
   | **网络参与者** | **Azure AD 用户、组或应用程序** | 输入“Azure 逻辑应用”。 成员列表显示后，请选择相同的值。 <p>**提示**：如果找不到此服务，请输入逻辑应用服务的应用 ID：`7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   | **经典参与者** | **Azure AD 用户、组或应用程序** | 输入“Azure 逻辑应用”。 成员列表显示后，请选择相同的值。 <p>**提示**：如果找不到此服务，请输入逻辑应用服务的应用 ID：`7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   |||| 

   例如：

   ![添加权限](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

   有关对等互连所需的角色权限的详细信息，请参阅[创建、更改或删除虚拟网络对等互连中的“权限”部分](../virtual-network/virtual-network-manage-peering.md#permissions)。 

如果虚拟网络是通过 Azure ExpressRoute、Azure 点到站点 VPN 或 Azure 站点到站点 VPN 连接的，请转到下一部分以便可以添加所需的网关子网。 否则，请继续[创建环境](#create-environment)。

<a name="add-gateway-subnet"></a>

## <a name="add-gateway-subnet-for-virtual-networks-with-expressroute-or-vpns"></a>使用 ExpressRoute 或 VPN 为虚拟网络添加网关子网

完成前面的步骤后，为使集成服务环境 (ISE) 能够访问通过 [Azure ExpressRoute](../expressroute/expressroute-introduction.md)、[Azure 点到站点 VPN](../vpn-gateway/point-to-site-about.md) 或 [Azure 站点到站点 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 连接的 Azure 虚拟网络，还必须将一个[网关子网](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub)添加到虚拟网络：

1. 在 [Azure 门户](https://portal.azure.com)中，找到并选择自己的虚拟网络。 在虚拟网络的菜单中选择“子网”，然后选择“网关子网” > “确定”。

   ![添加网关子网](./media/connect-virtual-network-vnet-isolated-environment/add-gateway-subnet.png)

1. 现在，创建要与前面创建的网关子网关联的[路由表](../virtual-network/manage-route-table.md)。

   1. 在 Azure 主菜单中，选择 **“创建资源”** > 
    **“网络”** > **“路由表”**。

      ![创建路由表](./media/connect-virtual-network-vnet-isolated-environment/create-route-table.png)

   1. 提供有关路由表的信息，例如名称、要使用的 Azure 订阅、Azure 资源组和位置。 确保“BGP 路由传播”属性设置为“已启用”，然后选择“创建”。

      ![提供路由表详细信息](./media/connect-virtual-network-vnet-isolated-environment/enter-route-table-information.png)

   1. 在路由表菜单中选择“子网”，然后选择“关联”。 

      ![将路由表连接到子网](./media/connect-virtual-network-vnet-isolated-environment/associate-route-table.png)

   1. 选择“虚拟网络”，然后选择自己的虚拟网络。
   
   1. 选择“子网”，然后选择前面创建的网关子网。

   1. 完成后，选择“确定”。

1. 如果使用了点到站点 VPN，请同时完成以下步骤：

   1. 在 Azure 中，找到并选择自己的虚拟网络网关资源。

   1. 在网关的菜单中，选择“点到站点配置”。 
   然后选择“下载 VPN 客户端”，以获取最新的 VPN 客户端配置。

      ![下载最新的 VPN 客户端](./media/connect-virtual-network-vnet-isolated-environment/download-vpn-client.png)

现已设置完使用 ExpressRoute、点到站点 VPN 或站点到站点 VPN 的虚拟网络的网关子网。 若要继续创建集成服务环境，请执行后续步骤。

<a name="create-environment"></a>

## <a name="create-your-ise"></a>创建 ISE

若要创建集成服务环境 (ISE)，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)上的 Azure 主菜单上，选择“创建资源”。

   ![新建资源](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 在搜索框中，输入“集成服务环境”作为筛选器。
在结果列表中选择“集成服务环境(预览版)”，然后选择“创建”。

   ![选择“集成服务环境”](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![选择“创建”](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 提供环境的以下详细信息：

   ![提供环境详细信息](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 属性 | 必选 | 值 | 说明 |
   |----------|----------|-------|-------------|
   | **名称** | 是 | <*environment-name*> | 为环境指定的名称 | 
   | **订阅** | 是 | <*Azure-subscription-name*> | 用于环境的 Azure 订阅 | 
   | **资源组** | 是 | <*Azure-resource-group-name*> | 要在其中创建环境的 Azure 资源组 |
   | **位置** | 是 | <*Azure-datacenter-region*> | 要在其中存储有关环境的信息的 Azure 数据中心区域 |
   | **对等 VNET** | 否 | <*Azure-VNET-name*> | 要作为对等方与环境关联的，以便该环境中的逻辑应用可以访问你的虚拟网络的 Azure 虚拟网络。 在创建此关系之前，请确保已[在 Azure 逻辑应用的虚拟网络中设置基于角色的访问控制](#vnet-access)。 <p>**重要说明**：只能在创建环境时选择虚拟网络，不过，不一定非要使用虚拟网络。 | 
   | **对等互连名称** | 必须为选定的虚拟网络提供 | <*peering-name*> | 为对等关系指定的名称 | 
   | **VNET IP 范围** | 必须为选定的虚拟网络提供 | <*IP-address-range*> | 用于在环境中创建资源的 IP 地址范围。 此范围必须使用[无类域间路由 (CIDR) 格式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)（例如 10.0.0.1/16），并且需要 B 类地址空间。 该范围不能在“对等 VNET”属性中选择的虚拟网络的地址空间内，也不能在通过对等互连或网关连接了对等网络的其他任何专用 IP 地址范围内。 <p><p>**重要说明**：创建环境后无法更改此地址范围。 |
   |||||
   
1. 完成后，选择“创建”。 

   Azure 随即开始部署该环境，但此过程最长可能需要两个小时才能完成。 
   若要检查部署状态，请在 Azure 工具栏上选择通知图标打开通知窗格。

   ![检查部署状态](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   部署成功完成后，Azure 会显示以下通知：

   ![部署成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. 如果部署完成后 Azure 未自动转到你的环境，可以选择“转到资源”来查看该环境。  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>创建逻辑应用 - ISE

若要创建使用集成服务环境 (ISE) 的逻辑应用，请遵循[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)中的一般步骤，但要注意以下差别和事项： 

* 创建逻辑应用时，“位置”属性会在“集成服务环境”下面列出 ISE 以及可用的区域。 请选择 ISE 而不要选择区域，例如：

  ![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* 可以使用相同的内置功能，例如 HTTP 触发器或操作，这些内置功能将在运行父逻辑应用的同一 ISE 中运行。 带有“ISE”标签的连接器也在运行父逻辑应用的同一 ISE 中运行。 没有 ISE 标签的连接器运行在全局逻辑应用服务中。

  ![选择 ISE 连接器](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* 如果以前在 ISE 中将某个 Azure 虚拟网络设置为对等方，则 ISE 中的逻辑应用可以直接访问该虚拟网络中的资源。 对于链接到 ISE 的虚拟网络中的本地系统，逻辑应用可以直接使用以下任何项来访问这些系统： 

  * 该系统的 ISE 连接器，例如 SQL Server

  * HTTP 操作 

  * 自定义连接器

  对于不在虚拟网络中或不具有 ISE 连接器的本地系统，仍然可以在[设置和使用本地数据网关](../logic-apps/logic-apps-gateway-install.md)后进行连接。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>创建集成帐户 - ISE

若要对集成服务环境 (ISE) 中的逻辑应用使用集成帐户，该集成帐户必须使用逻辑应用所在的同一个环境。 ISE 中的逻辑应用只能引用同一 ISE 中的集成帐户。 

若要创建使用 ISE 的集成帐户，请遵循[如何创建集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)中的一般步骤，不过，“位置”属性现在会在“集成服务环境”下面列出你的 ISE 以及可用的区域。 请选择 ISE 而不要选择区域，例如：

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>获取支持

* 有关问题，请访问 <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure 逻辑应用论坛</a>。
* 若要提交功能建议或对功能建议进行投票，请访问<a href="https://aka.ms/logicapps-wish" target="_blank">逻辑应用用户反馈网站</a>。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
