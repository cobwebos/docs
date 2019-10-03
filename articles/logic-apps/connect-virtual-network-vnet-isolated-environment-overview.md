---
title: 使用集成服务环境 (ISE) 从 Azure 逻辑应用访问 Azure 虚拟网络
description: 本概述介绍集成服务环境 (ISE) 如何帮助逻辑应用访问 Azure 虚拟网络 (VNET)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 831a1457d865429fd53af1887a14c363b806300c
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516609"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>使用集成服务环境 (ISE) 从 Azure 逻辑应用访问 Azure 虚拟网络资源

有时, 你的逻辑应用和集成帐户需要访问[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)中受保护的资源, 例如虚拟机 (vm) 以及其他系统或服务。 若要设置此访问权限, 可以[创建一个*integration service 环境*(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , 以便在其中运行逻辑应用并创建集成帐户。

创建 ISE 时, Azure 会将 ISE*注入*到 azure 虚拟网络, 然后在 azure 虚拟网络中将逻辑应用服务的专用实例和隔离实例部署到 azure 虚拟网络。 此专用实例使用专用资源（如存储），并与公用“全局”逻辑应用服务分开运行。 分离隔离的私有实例和公共全局实例还有助于降低其他 Azure 租户对应用性能的影响, 这也称为["干扰邻居"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)。

创建 ISE 后, 当你开始创建逻辑应用或集成帐户时, 可以选择你的 ISE 作为逻辑应用或集成帐户的位置:

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

逻辑应用现在可以使用以下任一项直接访问内部或连接到虚拟网络的系统:

* 用于该系统的**ISE**标记的连接器, 例如 SQL Server
* **核心**标记的内置触发器或操作, 例如 HTTP 触发器或操作
* 自定义连接器

本概述介绍了如何通过 ISE 为逻辑应用和集成帐户提供对 Azure 虚拟网络的直接访问权限并比较 ISE 与全局逻辑应用服务之间的差异的详细信息。

> [!IMPORTANT]
> 在 ISE 中运行的逻辑应用、内置触发器、内置操作和连接器使用不同于基于消耗的定价计划的定价计划。 若要了解 ISEs 的定价和计费工作原理, 请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价费率, 请参阅[逻辑应用定价](../logic-apps/logic-apps-pricing.md)。
>
> 你的 ISE 还增加了对运行持续时间、存储保留、吞吐量、HTTP 请求和响应超时、消息大小和自定义连接器请求的限制。 
> 有关详细信息, 请参阅[Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md)。

<a name="difference"></a>

## <a name="isolated-versus-global"></a>独立与全局

在 Azure 中创建集成服务环境 (ISE) 时, 可以选择要*注入*ISE 的 Azure 虚拟网络。 然后, Azure 会在虚拟网络中注入或部署逻辑应用服务的专用实例。 此操作将创建一个独立环境，可以在专用资源上创建和运行逻辑应用。 创建逻辑应用时, 可以选择 ISE 作为应用的位置, 这使逻辑应用可直接访问虚拟网络和该网络中的资源。

ISE 中的逻辑应用可提供与全局逻辑应用服务相同的用户体验和类似的功能。 你不仅可以使用相同的内置触发器、内置操作, 还可以使用来自全局逻辑应用服务的连接器, 但你也可以使用 ISE 特定的连接器。 例如, 以下是一些标准连接器, 提供在 ISE 中运行的版本:

* Azure Blob 存储、文件存储和表存储
* Azure 队列、Azure 服务总线、Azure 事件中心和 IBM MQ
* FTP 和 SFTP-SSH
* SQL Server、SQL 数据仓库、Azure Cosmos DB
* AS2、X12 和 EDIFACT

ISE 和非 ISE 连接器之间的区别在于触发器和操作运行的位置：

* 在 ISE 中, 内置触发器和操作 (如 HTTP) 始终在与逻辑应用相同的 ISE 中运行, 并显示 "**核心**" 标签。

  ![选择 "核心" 内置触发器和操作](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* 在 ISE 中运行的连接器具有可在全局逻辑应用服务中使用的公开托管版本。 对于提供两个版本的连接器, 具有**ISE**标签的连接器始终在与逻辑应用相同的 ISE 中运行。 没有 ISE 标签的连接器运行在全局逻辑应用服务中。

  ![选择 ISE 连接器](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

ISE 还为运行持续时间、存储保留、吞吐量、HTTP 请求和响应超时、消息大小和自定义连接器请求提供了更大的限制。 有关详细信息, 请参阅[Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md)。

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE Sku

创建 ISE 时, 可以选择开发人员 SKU 或高级 SKU。 下面是这些 Sku 之间的差异:

* **开发人员**

  提供了可用于试验、开发和测试, 但不能用于生产或性能测试的低成本 ISE。 开发人员 SKU 包括内置的触发器和操作、标准连接器、企业连接器, 以及每月固定价格的单个[免费层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帐户。 但是, 此 SKU 不包括任何服务级别协议 (SLA)、用于扩展容量的选项或回收期间的冗余性, 这意味着你可能会遇到延迟或停机时间。

* **高级**

  提供了可用于生产的 ISE, 并包括 SLA 支持、内置触发器和操作、标准连接器、企业连接器、单个[标准层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帐户、用于扩展容量的选项, 以及在回收固定的每月价格。

> [!IMPORTANT]
> SKU 选项仅在创建 ISE 时可用, 不能在以后更改。

有关定价费率, 请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 若要了解 ISEs 的定价和计费工作原理, 请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE 终结点访问

创建 ISE 时, 可以选择使用内部或外部访问终结点。 这些终结点确定 ISE 中逻辑应用的请求或 webhook 触发器是否可以从虚拟网络外部接收调用。 这些终结点还会影响对逻辑应用运行历史记录中的输入和输出的访问。

* **内部**：专用终结点, 允许调用 ISE 中的逻辑应用, 以及仅*从虚拟网络内部*访问运行历史记录中的输入和输出
* **外部**：允许在 ISE 中调用逻辑应用的公共终结点, 以及*来自虚拟网络外部*的运行历史记录中的输入和输出的访问权限

> [!IMPORTANT]
> "访问终结点" 选项仅在创建 ISE 时可用, 不能在以后更改。

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>访问本地数据源

对于连接到 Azure 虚拟网络的本地系统, 请将 ISE 注入该网络, 以便逻辑应用可以使用以下任一项直接访问这些系统:

* 适用于该系统的 ISE 版本连接器, 例如 SQL Server
* HTTP 操作
* 自定义连接器

  * 如果你有需要本地数据网关的自定义连接器, 并且你在 ISE 外部创建了这些连接器, 则 ISE 中的逻辑应用还可以使用这些连接器。
  
  * 在 ISE 中创建的自定义连接器不适用于本地数据网关。 但是, 这些连接器可以直接访问连接到托管 ISE 的虚拟网络的本地数据源。 因此, 在与这些资源通信时, ISE 中的逻辑应用最有可能不需要数据网关。

对于未连接到虚拟网络或没有 ISE 版本连接器的本地系统, 你必须首先[设置本地数据网关](../logic-apps/logic-apps-gateway-install.md), 然后才能将逻辑应用连接到这些系统。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>集成帐户与 ISE

可以在集成服务环境 (ISE) 中将集成帐户与逻辑应用结合使用。 但是，这些集成帐户必须使用相同的 ISE 作为链接的逻辑应用。 ISE 中的逻辑应用只能引用同一 ISE 中的这些集成帐户。 创建集成帐户时，可以选择 ISE 作为集成帐户的位置。 若要了解如何使用 ISE 实现集成帐户的定价和计费, 请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价费率, 请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。

## <a name="next-steps"></a>后续步骤

* [从隔离逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [向 integration service 环境添加项目](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [管理 integration service 环境](../logic-apps/ise-manage-integration-service-environment.md)
* 详细了解 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
