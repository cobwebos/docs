---
title: 访问 Azure 虚拟网络
description: '概述 (的集成服务环境如何) 帮助逻辑应用访问 Azure 虚拟网络 (Vnet) '
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 07/05/2020
ms.openlocfilehash: f152283b1280cde2a26569b8acf10738e883e39e
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816009"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>使用集成服务环境 (ISE) 从 Azure 逻辑应用访问 Azure 虚拟网络资源

有时，逻辑应用需要访问受保护的资源，例如虚拟机 (Vm) 以及连接到 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)的其他系统或服务。 若要设置此访问权限，可以 [ (ISE) 创建 *integration service 环境* ](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。 ISE 是逻辑应用服务的实例，它使用专用资源并独立于 "全局" 多租户逻辑应用服务运行。

例如，某些 Azure 虚拟网络使用专用终结点，你可以通过 [Azure 专用链接](../private-link/private-link-overview.md)进行设置，以提供对 azure PaaS 服务（例如 azure 存储、Azure Cosmos DB 或 Azure SQL 数据库、合作伙伴服务或托管在 azure 上的客户服务）的访问权限。 如果逻辑应用需要访问使用专用终结点的虚拟网络，则必须在 ISE 中创建、部署和运行这些逻辑应用。

当你创建 ISE 时，Azure 会将该 ISE 注入或部署到 Azure 虚拟网络中。 然后，你可以将该 ISE 用作需要进行访问的逻辑应用和集成帐户的位置。

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

此概述提供了有关 [为何要使用 ISE 的原因](#benefits)、 [专用和多租户逻辑应用服务之间的差异](#difference)，以及如何直接访问 Azure 虚拟网络内部或连接的资源的详细信息。

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>为什么使用 ISE？

在你自己的专用实例中运行逻辑应用有助于降低其他 Azure 租户对应用性能的影响，此影响也称为[“邻近干扰”影响](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)。 ISE 还带来以下好处：

* 直接访问虚拟网络内部或连接的资源

  在 ISE 中创建和运行的逻辑应用可以使用 [在 ise 中运行的专门设计的连接器](../connectors/apis-list.md#ise-connectors)。 如果某个本地系统或数据源存在 ISE 连接器，则无需使用 [本地数据网关](../logic-apps/logic-apps-gateway-connection.md)即可直接连接。 有关详细信息，请参阅本主题后面的 [专用与多租户](#difference) 以及对 [本地系统的访问权限](#on-premises) 。

* 继续访问外部或未连接到虚拟网络的资源

  当 ISE 特定的连接器不可用时，在 ISE 中创建和运行的逻辑应用仍可使用在多租户逻辑应用服务中运行的连接器。 有关详细信息，请参阅 [专用与多租户](#difference)。

* 你自己的静态 IP 地址，它们不同于多租户服务中的逻辑应用共享的静态 IP 地址。 还可以设置单个公共的、静态的和可预测的出站 IP 地址，以便与目标系统通信。 这样就无需在每个 ISE 的那些目标系统上设置其他防火墙开口。

* 增加了对运行持续时间、存储保留、吞吐量、HTTP 请求和响应超时、消息大小和自定义连接器请求的限制。 有关详细信息，请参阅 [Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md)。

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>专用与多租户

在 ISE 中创建和运行逻辑应用时，可以获得与多租户逻辑应用服务相同的用户体验和类似功能。 可以使用多租户逻辑应用服务中提供的所有相同内置触发器、操作和托管连接器。 某些托管连接器提供其他 ISE 版本。 在 ISE 中工作时，ISE 连接器和非 ISE 连接器之间的区别在于它们的运行位置以及它们在逻辑应用设计器中的标签。

![ISE 中具有和不含标签的连接器](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* 内置的触发器和操作（如 HTTP）显示 **核心** 标签，并在与逻辑应用相同的 ISE 中运行。

* 显示 **ISE** 标签的托管连接器专门为 ISEs 设计，并 *始终在与逻辑应用相同的 ISE 中运行*。 例如，以下是一些 [提供 ISE 版本的连接器](../connectors/apis-list.md#ise-connectors)：<p>

  * Azure Blob 存储、文件存储和表存储
  * Azure 服务总线，Azure 队列，Azure 事件中心
  * Azure 自动化、Azure Key Vault、Azure 事件网格和 Azure Monitor 日志
  * FTP、SFTP-SSH、文件系统和 SMTP
  * SAP、IBM MQ、IBM DB2 和 IBM 3270
  * SQL Server、Azure SQL 数据仓库、Azure Cosmos DB
  * AS2、X12 和 EDIFACT

  在少数例外情况下，如果 ISE 连接器可用于本地系统或数据源，则无需使用 [本地数据网关](../logic-apps/logic-apps-gateway-connection.md)即可直接连接。 有关详细信息，请参阅本主题后面的对 [本地系统的访问权限](#on-premises) 。

* 不显示 **ise** 标签的托管连接器继续适用于 ISE 内的逻辑应用。 这些连接器 *始终在多租户逻辑应用服务中运行*，而不是在 ISE 中运行。

* 在 *ise 外*创建的自定义连接器，无论它们是否需要 [本地数据网关](../logic-apps/logic-apps-gateway-connection.md)，都将继续在 ise 内使用逻辑应用。 但是，在 *ISE 中* 创建的自定义连接器不会使用本地数据网关。 有关详细信息，请参阅对 [本地系统的访问权限](#on-premises)。

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>访问本地系统

在 ISE 内运行的逻辑应用可以使用以下项直接访问内部或连接到 Azure 虚拟网络中的本地系统和数据源：<p>

* HTTP 触发器或操作，用于显示 **核心** 标签

* 适用于本地系统或数据源的 **ISE** 连接器（如果可用）

  如果 ISE 连接器可用，则可以直接访问系统或数据源，而无需使用 [本地数据网关](../logic-apps/logic-apps-gateway-connection.md)。 但是，如果需要从 ISE 访问 SQL Server 并使用 Windows 身份验证，则必须使用连接器的非 ISE 版本和本地数据网关。 连接器的 ISE 版本不支持 Windows 身份验证。 有关详细信息，请参阅 [ISE 连接器](../connectors/apis-list.md#ise-connectors) 和 [从 Integration service 环境连接](../connectors/apis-list.md#integration-service-environment)。

* 自定义连接器

  * 在 *ise 外*创建的自定义连接器，无论它们是否需要 [本地数据网关](../logic-apps/logic-apps-gateway-connection.md)，都将继续在 ise 内使用逻辑应用。

  * 在 *ISE 中* 创建的自定义连接器不适用于本地数据网关。 但是，这些连接器可以直接访问内部或连接到托管 ISE 的虚拟网络的本地系统和数据源。 因此，在访问这些资源时，ISE 内的逻辑应用通常不需要数据网关。

若要访问没有 ISE 连接器、位于虚拟网络外部或未连接到虚拟网络的本地系统和数据源，则仍需使用本地数据网关。 ISE 中的逻辑应用可以继续使用没有 **CORE** 或 **ISE** 标签的连接器。 这些连接器只在多租户逻辑应用服务中运行，而不是在 ISE 中运行。 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE Sku

创建 ISE 时，可以选择开发人员 SKU 或高级 SKU。 下面是这些 Sku 之间的差异：

* **开发人员**

  提供了可用于试验、开发和测试，但不能用于生产或性能测试的低成本 ISE。 开发人员 SKU 包括内置的触发器和操作、标准连接器、企业连接器，以及每月固定价格的单个 [免费层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 集成帐户。 但是，此 SKU 不包括任何服务级别协议 (SLA) 、用于扩展容量的选项或回收期间的冗余性，这意味着你可能会遇到延迟或停机时间。

* **高级**

  提供了一种可用于生产的 ISE，并包括 SLA 支持、内置触发器和操作、标准连接器、企业连接器、单个 [标准层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 集成帐户、用于扩展容量的选项，以及回收期间的冗余（固定的每月价格）。

> [!IMPORTANT]
> SKU 选项仅在创建 ISE 时可用，不能在以后更改。

有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 要了解 ISE 的定价和计费原理，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE 终结点访问

创建 ISE 时，可以选择使用内部或外部访问终结点。 你的选择将确定 ISE 中逻辑应用的请求或 webhook 触发器是否可以从虚拟网络外部接收调用。 这些终结点还会影响从逻辑应用的运行历史记录访问输入和输出的方式。

> [!IMPORTANT]
> 只能在 ISE 创建过程中选择访问终结点，以后无法更改此选项。

* **内部**：专用终结点允许调用 ISE 中的逻辑应用，其中你只能 *从虚拟网络内部*查看和访问逻辑应用 "运行历史记录" 中的输入和输出。

  > [!IMPORTANT]
  > 请确保专用终结点与要从中访问运行历史记录的计算机之间存在网络连接。 否则，当你尝试查看逻辑应用的运行历史记录时，会收到一条错误消息，指出 "意外错误。 未能提取 "。
  >
  > ![由于无法通过防火墙发送流量导致的 Azure 存储操作错误](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > 例如，你的客户端计算机可以位于 ISE 的虚拟网络中，也可以存在于通过对等互连或虚拟专用网络连接到 ISE 的虚拟网络的虚拟网络中。 

* **外部**：公用终结点允许调用 ISE 中的逻辑应用，可在其中查看和访问来自 *虚拟网络外部*的逻辑应用的运行历史记录的输入和输出。 如果使用 (Nsg) 的网络安全组，请确保它们已设置为允许访问运行历史记录的输入和输出的入站规则。 有关详细信息，请参阅 [为 ISE 启用访问权限](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)。

若要确定 ISE 是否使用内部或外部访问终结点，请在 ISE 菜单上的 " **设置**" 下，选择 " **属性**"，然后找到 " **访问终结点** " 属性：

![查找 ISE 访问终结点](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>定价模型

在 ISE 中运行的逻辑应用、内置触发器、内置操作和连接器使用不同于基于消耗的定价计划的固定定价计划。 有关详细信息，请参阅 [逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>集成帐户与 ISE

可以在集成服务环境 (ISE) 中将集成帐户与逻辑应用结合使用。 但是，这些集成帐户必须使用相同的 ISE** 作为链接的逻辑应用。 ISE 中的逻辑应用只能引用同一 ISE 中的这些集成帐户。 创建集成帐户时，可以选择 ISE 作为集成帐户的位置。 若要了解如何使用 ISE 实现集成帐户的定价和计费，请参阅 [逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。

## <a name="next-steps"></a>后续步骤

* [从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* 详细了解 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)