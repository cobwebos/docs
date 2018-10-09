---
title: 从 Azure 逻辑应用访问 Azure 虚拟网络
description: 本概述介绍了独立逻辑应用如何从使用专用资源的集成服务环境 (ISE) 连接到 Azure 虚拟网络
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 9546b8ca33ef7da2d570b547446858e2a4099234
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393125"
---
# <a name="access-to-azure-virtual-network-resources-from-isolated-azure-logic-apps"></a>从独立 Azure 逻辑应用访问 Azure 虚拟网络资源

> [!NOTE]
> 此功能在个人预览版中提供。 若要请求访问权限，[请在此处创建加入请求](https://aka.ms/iseprivatepreview)。

有时，逻辑应用和集成帐户需要访问受保护的资源，例如虚拟机 (VM) 和其他系统或 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)内的服务。 若要提供此访问权限，可以[创建一个集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 作为用于创建逻辑应用和集成帐户的位置。 

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

通过创建一个 ISE，可将专用和独立逻辑应用实例部署到 Azure 虚拟网络中。 专用实例使用专用资源（如存储），并与公用“全局”逻辑应用服务分开运行。 这种分离还有助于减少其他 Azure 租户可能对应用性能产生的影响或[对环境产生的不良影响](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)。 

本概述介绍了如何创建 ISE，帮助逻辑应用和集成帐户直接访问 Azure 虚拟网络内的资源，并比较了 ISE 和全局逻辑应用服务之间的差异。

<a name="difference"></a>

## <a name="isolated-versus-global"></a>独立与全局

在 Azure 中创建集成服务环境 (ISE) 时，可以为环境选择 Azure 虚拟网络作为对等方。 Azure 将逻辑应用服务的专用实例部署到虚拟网络中，从而形成一个独立的环境，在其中你可以在专用资源上创建并运行逻辑应用。 创建逻辑应用时，可以选择此环境作为应用的位置，还可以让逻辑应用直接访问虚拟网络中的资源。  

ISE 中的逻辑应用可提供与全局逻辑应用服务相同的用户体验和类似的功能。 你不仅可以使用全局逻辑应用服务提供的相同内置对象和连接器，而且可以从提供 ISE 版本的连接器中进行选择。 例如，下面是一些提供在 ISE 中运行的版本的标准连接器：
 
* Azure Blob 存储、文件存储和表存储
* Azure 队列
* Azure 服务总线
* FTP
* SSH FTP (SFTP)
* SQL Server
* AS2、X12 和 EDIFACT

ISE 和非 ISE 连接器之间的区别在于触发器和操作运行的位置：

* 如果在 ISE 中使用内置的触发器和操作，例如 HTTP，则这些触发器和操作会始终运行在与逻辑应用相同的 ISE 中。 

* 对于提供两个版本的连接器：一个版本运行在 ISE 中，而另一个版本运行在全局逻辑应用服务中。  

  具有 ISE 标签的连接器始终运行在与逻辑应用相同的 ISE 中。 没有 ISE 标签的连接器运行在全局逻辑应用服务中。 

  ![选择 ISE 连接器](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* 在 ISE 中配置的连接器同样可在全局逻辑应用服务中使用。 

> [!IMPORTANT]
> 运行在 ISE 中的逻辑应用、内置操作和连接器使用不同的定价计划，而不是基于消费的定价计划。 有关详细信息，请参阅[逻辑应用定价](../logic-apps/logic-apps-pricing.md)。

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>用于访问虚拟网络的权限

创建集成服务环境 (ISE) 时，可以为环境选择 Azure 虚拟网络作为对等方。 但是，在创建 ISE 时，只能创建此关系，或对等互连。 此关系可让你的 ISE 访问虚拟网络中的资源，然后可让此 ISE 中的逻辑应用直接连接到虚拟网络中的资源。 对于链接到 ISE 的虚拟网络中的本地系统，逻辑应用可以直接使用以下任何项来访问这些系统： 

* 该系统的 ISE 连接器，例如 SQL Server

* HTTP 操作 

* 自定义连接器

对于不在虚拟网络中或不具有 ISE 连接器的本地系统，仍然可以在[设置和使用本地数据网关](../logic-apps/logic-apps-gateway-install.md)后进行连接。

你必须在 Azure 虚拟网络中为 Azure 逻辑应用服务设置基于角色的访问控制 (RBAC) 权限之后，才能为环境选择 Azure 虚拟网络作为对等方。 此任务要求向 Azure 逻辑应用服务分配“网络参与者”和“经典参与者”角色。 有关对等互连所需的角色权限的详细信息，请参阅[创建、更改或删除虚拟网络对等互连中的“权限”部分](../virtual-network/virtual-network-manage-peering.md#permissions)。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>集成帐户与 ISE

可以将集成帐户与运行在集成服务环境 (ISE) 中的逻辑应用一起使用，但这些集成帐户还必须使用与链接的逻辑应用相同的 ISE。 ISE 中的逻辑应用只能引用同一 ISE 中的这些集成帐户。 创建集成帐户时，可以选择 ISE 作为集成帐户的位置。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure 逻辑应用论坛</a>。
* 若要提交功能建议或对功能建议进行投票，请访问<a href="http://aka.ms/logicapps-wish" target="_blank">逻辑应用用户反馈网站</a>。

## <a name="next-steps"></a>后续步骤

* 了解如何[从独立的逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* 详细了解 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
