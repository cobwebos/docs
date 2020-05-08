---
title: 访问 Azure 虚拟网络
description: 概述 integration service 环境（ISEs）如何帮助逻辑应用访问 Azure 虚拟网络（Vnet）
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: d74303df74a1e877645b333fa0726a68055c819b
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734904"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>使用集成服务环境 (ISE) 从 Azure 逻辑应用访问 Azure 虚拟网络资源

有时，逻辑应用需要访问[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)中受保护的资源，例如虚拟机（vm）以及其他系统或服务。 若要设置此访问权限，可以[创建*集成服务环境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。 ISE 是逻辑应用服务的独立实例，该实例使用专用资源并独立于 "全局" 多租户逻辑应用服务运行。

在单独的独立实例中运行逻辑应用有助于降低其他 Azure 租户对应用性能的影响，也称为["干扰邻居"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)的影响。 ISE 还提供以下优势：

* 你自己的静态 IP 地址，它们不同于多租户服务中的逻辑应用共享的静态 IP 地址。 还可以设置单个公共、静态和可预测的出站 IP 地址，以便与目标系统进行通信。 这样，就无需在每个 ISE 的目标系统上设置其他防火墙。

* 增加了对运行持续时间、存储保留、吞吐量、HTTP 请求和响应超时、消息大小和自定义连接器请求的限制。 有关详细信息，请参阅[Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md)。

> [!NOTE]
> 某些 Azure 虚拟网络使用专用终结点（[Azure 专用链接](../private-link/private-link-overview.md)）来提供对 azure PaaS 服务（例如 azure 存储、Azure Cosmos DB 或 Azure SQL 数据库、合作伙伴服务或托管在 azure 上的客户服务）的访问权限。 如果逻辑应用需要访问使用专用终结点的虚拟网络，则必须在 ISE 中创建、部署和运行这些逻辑应用。

创建 ISE 时，Azure 会将此 ISE*注入*或部署到 Azure 虚拟网络。 然后，你可以将此 ISE 用作需要访问的逻辑应用和集成帐户的位置。

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

逻辑应用可以使用这些项目（在与逻辑应用相同的 ISE 中运行）访问位于虚拟网络内部或连接到虚拟网络的资源：

* **核心**标记的内置触发器或操作，例如 HTTP 触发器或操作
* 用于该系统或服务的**ISE**标记的连接器
* 自定义连接器

你还可以使用 ISE 中没有**核心**或**ise**标签的连接器和逻辑应用。 这些连接器改为在多租户逻辑应用服务中运行。 有关详细信息，请参阅以下部分：

* [隔离的与多租户](#difference)
* [从 integration service 环境连接](../connectors/apis-list.md#integration-service-environment)
* [ISE 连接器](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> 在 ISE 中运行的逻辑应用、内置触发器、内置操作和连接器使用不同于基于消耗的定价计划的定价计划。 有关详细信息，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价的详细信息，请参阅[逻辑应用定价](../logic-apps/logic-apps-pricing.md)。

本概述介绍了如何通过 ISE 为逻辑应用提供对 Azure 虚拟网络的直接访问权限并比较 ISE 与多租户逻辑应用服务之间的差异的详细信息。

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>隔离的与多租户

在 ISE 中创建和运行逻辑应用时，可以获得与多租户逻辑应用服务相同的用户体验和类似功能。 可以使用多租户逻辑应用服务中提供的所有相同内置触发器、操作和托管连接器。 某些托管连接器提供其他 ISE 版本。 在 ISE 中工作时，ISE 连接器和非 ISE 连接器之间的区别在于它们的运行位置以及它们在逻辑应用设计器中的标签。

![ISE 中具有和不含标签的连接器](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* 内置触发器和操作显示 "**核心**" 标签。 它们始终在与逻辑应用相同的 ISE 中运行。 显示**ise**标签的托管连接器也与逻辑应用在同一 ISE 中运行。

  例如，以下是一些提供 ISE 版本的连接器：

  * Azure Blob 存储、文件存储和表存储
  * Azure 队列、Azure 服务总线、Azure 事件中心和 IBM MQ
  * FTP 和 SFTP-SSH
  * SQL Server、Azure SQL 数据仓库、Azure Cosmos DB
  * AS2、X12 和 EDIFACT

* 不显示任何其他标签的托管连接器始终在多租户逻辑应用服务中运行，但你仍可以在 ISE 托管的逻辑应用中使用这些连接器。

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>访问本地系统

若要访问连接到 Azure 虚拟网络的本地系统或数据源，ISE 中的逻辑应用可以使用以下项：

* HTTP 操作

* 用于该系统的 ISE 标记的连接器

  > [!NOTE]
  > 若要对[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的 SQL Server 连接器使用 Windows 身份验证，请将连接器的非 ISE 版本用于[本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 ISE 标记版本不支持 Windows 身份验证。

* 自定义连接器

  * 如果你有需要本地数据网关的自定义连接器，并且你在 ISE 外部创建了这些连接器，则 ISE 中的逻辑应用还可以使用这些连接器。

  * 在 ISE 中创建的自定义连接器不适用于本地数据网关。 但是，这些连接器可以直接访问连接到托管 ISE 的虚拟网络的本地数据源。 因此，在与这些资源通信时，ISE 中的逻辑应用最有可能不需要数据网关。

对于未连接到虚拟网络或没有 ISE 标记的连接器的本地系统，你必须首先[设置本地数据网关](../logic-apps/logic-apps-gateway-install.md)，然后才能将逻辑应用连接到这些系统。

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE Sku

创建 ISE 时，可以选择开发人员 SKU 或高级 SKU。 下面是这些 Sku 之间的差异：

* **开发人员**

  提供了可用于试验、开发和测试，但不能用于生产或性能测试的低成本 ISE。 开发人员 SKU 包括内置的触发器和操作、标准连接器、企业连接器，以及每月固定价格的单个[免费层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帐户。 但是，此 SKU 不包括任何服务级别协议（SLA）、用于扩展容量的选项或回收期间的冗余性，这意味着你可能会遇到延迟或停机时间。

* **Premium**

  提供了一种可用于生产的 ISE，并包括 SLA 支持、内置触发器和操作、标准连接器、企业连接器、单个[标准层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帐户、用于扩展容量的选项，以及回收期间的冗余（固定的每月价格）。

> [!IMPORTANT]
> SKU 选项仅在创建 ISE 时可用，不能在以后更改。

有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 若要了解 ISEs 的定价和计费工作原理，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE 终结点访问

创建 ISE 时，可以选择使用内部或外部访问终结点。 你的选择将确定 ISE 中逻辑应用的请求或 webhook 触发器是否可以从虚拟网络外部接收调用。 这些终结点还会影响从逻辑应用的运行历史记录访问输入和输出的方式。

> [!IMPORTANT]
> 只能在 ISE 创建过程中选择访问终结点，以后无法更改此选项。

* **内部**：专用终结点允许调用 ISE 中的逻辑应用，其中你只能*从虚拟网络内部*查看和访问逻辑应用 "运行历史记录" 中的输入和输出。 请确保专用终结点与要从中访问运行历史记录的计算机之间存在网络连接。 例如，你的客户端计算机可以位于 ISE 的虚拟网络中，也可以存在于连接到 ISE 虚拟网络的虚拟网络中，例如通过对等互连或虚拟专用网络。

* **外部**：公用终结点允许调用 ISE 中的逻辑应用，可在其中查看和访问来自*虚拟网络外部*的逻辑应用的运行历史记录的输入和输出。 如果使用网络安全组（Nsg），请确保将其设置为具有入站规则，以允许访问运行历史记录的输入和输出。 有关详细信息，请参阅[为 ISE 启用访问权限](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)。

若要确定 ISE 是否使用内部或外部访问终结点，请在 ISE 菜单上的 "**设置**" 下，选择 "**属性**"，然后找到 "**访问终结点**" 属性：

![查找 ISE 访问终结点](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>集成帐户与 ISE

可以在集成服务环境 (ISE) 中将集成帐户与逻辑应用结合使用。 但是，这些集成帐户必须使用相同的 ISE** 作为链接的逻辑应用。 ISE 中的逻辑应用只能引用同一 ISE 中的这些集成帐户。 创建集成帐户时，可以选择 ISE 作为集成帐户的位置。 若要了解如何使用 ISE 实现集成帐户的定价和计费，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。

## <a name="next-steps"></a>后续步骤

* [从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* 详细了解 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
* 了解[Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
