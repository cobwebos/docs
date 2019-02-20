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
ms.date: 02/12/2019
ms.openlocfilehash: 8d7fc6d8f581c3ad0e0f3266ea615acadcb7bc25
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176197"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>使用集成服务环境 (ISE) 从 Azure 逻辑应用连接到 Azure 虚拟网络

> [!NOTE]
> 此功能在个人预览版中提供。 若要加入个人预览版，[在此处创建请求](https://aka.ms/iseprivatepreview)。

对于逻辑应用和集成帐户需要访问 [ Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)的情况，请创建[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 ISE 是专用的隔离环境，它使用独立于公共服务或“全局”逻辑应用服务的专用存储和其他资源。 这种分离还减少了其他 Azure 租户可能对应用性能产生的影响。 可以将 ISE 注入到 Azure 虚拟环境，后者然后会将逻辑应用服务部署到虚拟网络中。 创建逻辑应用或集成帐户时，请选择此 ISE 作为其位置。 然后，逻辑应用或集成帐户可以直接访问虚拟网络中的虚拟机 (VM)、服务器、系统和服务等资源。

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

本文介绍如何完成以下任务：

* 在 Azure 虚拟网络中设置端口，使流量能够跨虚拟网络中的子网遍历集成服务环境 (ISE)。

* 在 Azure 虚拟网络中设置权限，使专用逻辑应用实例能够访问你的虚拟网络。

* 创建集成服务环境 (ISE)。

* 创建可在 ISE 中运行的逻辑应用。

* 在 ISE 中创建逻辑应用的集成帐户。

有关集成服务环境的详细信息，请参阅[从 Azure 逻辑应用访问 Azure 虚拟网络资源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。

  > [!IMPORTANT]
  > 运行在 ISE 中的逻辑应用、内置操作和连接器使用不同的定价计划，而不是基于消费的定价计划。 有关详细信息，请参阅[逻辑应用定价](../logic-apps/logic-apps-pricing.md)。

* [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。 如果没有虚拟网络，请了解如何[创建 Azure 虚拟网络](../virtual-network/quick-create-portal.md)。 若要部署 ISE，还需要在虚拟网络中创建子网。 可以提前创建这些子网，也可以等到在创建 ISE 时创建子网。 另外，[请确保虚拟网络中开放了这些端口](#ports)，使 ISE 能够正常运行且可供访问。

* 若要为逻辑应用提供对 Azure 虚拟网络的直接访问权限，请[设置网络的基于角色的访问控制 (RBAC) 权限](#vnet-access)，使逻辑应用服务有权访问你的虚拟网络。

* 若要使用一个或多个自定义的 DNS 服务器来部署 Azure 虚拟网络，请[遵循此指南设置这些服务器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)，然后将 ISE 部署到虚拟网络。 否则，每次更改 DNS 服务器时，都必须重启 ISE（ISE 公共预览版提供的一项功能）。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

<a name="ports"></a>

## <a name="set-up-network-ports"></a>设置网络端口

若要保持正常运行且可供访问，集成服务环境 (ISE) 需要使用虚拟网络中的特定端口。 否则，如果其中的任一端口不可用，ISE 可能会停止运行，使你无法访问 ISE。 在虚拟网络中使用 ISE 时，常见的设置问题是一个或多个端口被阻止。 对于 ISE 与目标系统之间的连接，所用的连接器还可能有其自身的端口要求。 例如，如果使用 FTP 连接器来与 FTP 系统通信，请确保在该 FTP 系统上使用的端口（例如用于发送命令的端口 21）可用。

若要在部署 ISE 的虚拟网络子网中控制入站和出站流量，可以参考[如何跨子网筛选网络流量](../virtual-network/tutorial-filter-network-traffic.md)，对这些子网设置[网络安全组](../virtual-network/security-overview.md)。 下列表格描述了 ISE 在虚拟网络中使用的端口，以及这些端口的使用位置。 星号 (*) 表示任一和所有流量源。 [服务标记](../virtual-network/security-overview.md#service-tags)表示一组 IP 地址前缀，在创建安全规则时，这些前缀有助于尽量降低复杂性。

| 目的 | 方向 | Source Port <br>Destination Port | 源服务标记 <br>目标服务标记 |
|---------|-----------|---------------------------------|-----------------------------------------------|
| 与 Azure 逻辑应用通信 <br>从 Azure 逻辑应用通信 | 入站 <br>出站 | * <br>80 和 443 | INTERNET <br>VIRTUAL_NETWORK |
| Azure Active Directory | 出站 | * <br>80 和 443 | VIRTUAL_NETWORK <br>AzureActiveDirectory |
| Azure 存储依赖项 | 出站 | * <br>80 和 443 | VIRTUAL_NETWORK <br>存储 |
| 连接管理 | 出站 | * <br>443 | VIRTUAL_NETWORK <br>INTERNET |
| 发布诊断日志和指标 | 出站 | * <br>443 | VIRTUAL_NETWORK <br>AzureMonitor |
| 逻辑应用设计器 - 动态属性 <br>逻辑应用的运行历史记录 <br>连接器部署 <br>请求触发器终结点 | 入站 | * <br>454 | INTERNET <br>VIRTUAL_NETWORK |
| 应用服务管理依赖项 | 入站 | * <br>454 和 455 | AppServiceManagement <br>VIRTUAL_NETWORK |
| API 管理 - 管理终结点 | 入站 | * <br>3443 | APIManagement <br>VIRTUAL_NETWORK |
| “记录到事件中心”策略和监视代理中的依赖项 | 出站 | * <br>5672 | VIRTUAL_NETWORK <br>EventHub |
| 访问角色实例之间的 Azure Redis 缓存实例 | 入站 <br>出站 | * <br>6381-6383 | VIRTUAL_NETWORK <br>VIRTUAL_NETWORK |
|||||

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>设置虚拟网络权限

创建集成服务环境 (ISE) 时，选择在其中注入环境的 Azure 虚拟网络。 但必须在虚拟网络中设置基于角色的访问控制 (RBAC) 权限之后，才能选择在其中注入环境的虚拟网络。 要设置权限，请将这些特定的角色分配给 Azure 逻辑应用服务：

1. 在 [Azure 门户](https://portal.azure.com)中，找到并选择自己的虚拟网络。

1. 在虚拟网络的菜单中，选择“访问控制(IAM)”。

1. 在“访问控制(IAM)”下，选择“添加角色分配”。

   ![添加角色](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. 在“添加角色分配”窗格上，根据说明将必要角色添加到 Azure 逻辑应用服务。

   1. 在“角色”下，选择“网络参与者”。

   1. 在“分配访问权限至”下，选择“Azure AD 用户、组或服务主体”。

   1. 在“选择”下，输入“Azure 逻辑应用”。

   1. 显示成员列表后，请选择“Azure 逻辑应用”。

      > [!TIP]
      > 如果找不到此服务，请输入逻辑用服务的应用 ID：`7cd684f4-8a78-49b0-91ec-6a35d38739ba`

   1. 完成后，选择“保存”。

   例如：

   ![添加角色分配](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

有关详细信息，请参阅[用于访问虚拟网络的权限](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

<a name="create-environment"></a>

## <a name="create-your-ise"></a>创建 ISE

若要创建集成服务环境 (ISE)，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)上的 Azure 主菜单上，选择“创建资源”。

   ![新建资源](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 在搜索框中，输入“集成服务环境”作为筛选器。
在结果列表中选择“集成服务环境(预览版)”，然后选择“创建”。

   ![选择“集成服务环境”](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![选择“创建”](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 为环境提供以下详细信息，然后选择“查看 + 创建”，例如：

   ![提供环境详细信息](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 属性 | 必选 | Value | 说明 |
   |----------|----------|-------|-------------|
   | **订阅** | 是 | <*Azure-subscription-name*> | 用于环境的 Azure 订阅 |
   | **资源组** | 是 | <*Azure-resource-group-name*> | 要在其中创建环境的 Azure 资源组 |
   | **集成服务环境名称** | 是 | <*environment-name*> | 为环境指定的名称 |
   | **位置** | 是 | <*Azure-datacenter-region*> | 要在其中部署环境的 Azure 数据中心区域 |
   | **额外容量** | 是 | 0、1、2、3 | 用于此 ISE 资源的处理单元数 |
   | **虚拟网络** | 是 | <Azure-virtual-network-name> | 要注入环境以便该环境中的逻辑应用可以访问虚拟网络的 Azure 虚拟网络。 如果没有网络，可以在此处创建一个。 <p>**重要说明**：创建 ISE 时可以仅执行此注入。 但是，在创建此关系之前，请确保已[在 Azure 逻辑应用的虚拟网络中设置基于角色的访问控制](#vnet-access)。 |
   | **子网** | 是 | <*subnet-resource-list*> | ISE 需要四个空的子网来在环境中创建资源。 因此，请确保这些子网未委派到任何服务。 创建环境后无法更改这些子网地址。 <p><p>要创建每个子网，请[按照此表下方的步骤操作](#create-subnet)。 每个子网必须满足以下条件： <p>- 必须为空。 <br>- 使用不以数字或连字符开头的名称。 <br>- 使用[无类别域际路由 (CIDR) 格式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)和 B 类地址空间。 <br>- 在地址空间中至少包括 `/27`，以便子网至少获得 32 个地址。 若要了解如何计算地址数目，请参阅 [IPv4 CIDR 块](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)。 例如： <p>- `10.0.0.0/24` 有 256 个地址，因为 2<sup>(32-24)</sup> 是 2<sup>8</sup> 或 256。 <br>- `10.0.0.0/27` 有 32 个地址，因为 2<sup>(32-27)</sup> 是 2<sup>5</sup> 或 32。 <br>- `10.0.0.0/28` 只有 16 个地址，因为 2<sup>(32-28)</sup> 是 2<sup>4</sup> 或 16。 |
   |||||

   <a name="create-subnet"></a>

   **创建子网**

   1. 在“子网”列表下，选择“管理子网配置”。

      ![管理子网配置](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. 在“子网”窗格中，选择“子网”。

      ![添加子网](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. 在“添加子网”窗格中，提供此信息。

      * **名称**：子网的名称
      * **地址范围(CIDR 块)**：虚拟网络采用 CIDR 格式的子网范围

      ![添加子网详细信息](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. 完成后，选择“确定”。

   1. 重复这些步骤创建额外三个子网。

1. Azure 成功验证 ISE 信息后，请选择“创建”，例如：

   ![成功验证后，选择“创建”](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure 随即开始部署该环境，但此过程最长可能需要两个小时才能完成。 
   若要检查部署状态，请在 Azure 工具栏上选择通知图标打开通知窗格。

   ![检查部署状态](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   如果部署成功完成，Azure 会显示以下通知：

   ![部署成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   > [!NOTE]
   > 如果部署失败或删除 ISE，Azure 在释放子网之前可能需要长达一个小时。 因此，可能需要等待一段时间才能在另一个 ISE 中重复使用这些子网。

1. 如果部署完成后 Azure 未自动转到你的环境，可以选择“转到资源”来查看该环境。  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>创建逻辑应用 - ISE

要创建使用集成服务环境 (ISE) 的逻辑应用，请遵循[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)中的步骤，但要注意以下差别： 

* 创建逻辑应用时，在“位置”属性下，从“集成服务环境”部分中选择 ISE，例如：

  ![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* 可以使用相同的内置触发器和操作（例如 HTTP），这些内置功能在运行逻辑应用的同一 ISE 中运行。 带有“ISE”标签的连接器也在运行逻辑应用的同一 ISE 中运行。 没有 ISE 标签的连接器运行在全局逻辑应用服务中。

  ![选择 ISE 连接器](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* 将 ISE 注入到 Azure 虚拟网络中后，ISE 中的逻辑应用可以直接访问该虚拟网络中的资源。 对于连接到虚拟网络的本地系统，请将 ISE 注入到该网络，以便逻辑应用可使用这些项中的任意一项直接访问这些系统： 

  * 该系统的 ISE 连接器，例如 SQL Server
  
  * HTTP 操作 
  
  * 自定义连接器

  对于没有在虚拟网络中或没有 ISE 连接器的本地系统，请首先[设置本地数据网关](../logic-apps/logic-apps-gateway-install.md)。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>创建集成帐户 - ISE

若要对集成服务环境 (ISE) 中的逻辑应用使用集成帐户，该集成帐户必须使用逻辑应用所在的同一个环境。 ISE 中的逻辑应用只能引用同一 ISE 中的集成帐户。 

要创建使用 ISE 的集成帐户，请按照[如何创建集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)中的步骤，但“位置”属性除外，该属性现在会显示“集成服务环境”部分。 请选择 ISE 而不要选择区域，例如：

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>获取支持

* 有关问题，请访问 <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure 逻辑应用论坛</a>。
* 若要提交功能建议或对功能建议进行投票，请访问<a href="https://aka.ms/logicapps-wish" target="_blank">逻辑应用用户反馈网站</a>。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
