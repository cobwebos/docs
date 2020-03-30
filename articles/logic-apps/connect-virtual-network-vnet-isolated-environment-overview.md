---
title: 访问 Azure 虚拟网络
description: 集成服务环境 （ISE） 如何帮助逻辑应用访问 Azure 虚拟网络 （VNET） 的概述
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127242"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>使用集成服务环境 (ISE) 从 Azure 逻辑应用访问 Azure 虚拟网络资源

有时，逻辑应用需要访问[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)内的安全资源，如虚拟机 （VM） 和其他系统或服务。 要设置此访问，您可以创建[*集成服务环境*（ISE）。](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) ISE 是逻辑应用服务的一个孤立实例，它使用专用资源，与"全局"多租户逻辑应用服务分开运行。

在您自己的独立实例中运行逻辑应用有助于减少其他 Azure 租户可能对应用性能的影响，也称为["嘈杂邻居"效果](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)。 ISE 还提供以下优势：

* 您自己的静态 IP 地址，这些地址与多租户服务中的逻辑应用共享的静态 IP 地址分开。 您还可以设置单个公共、静态和可预测的出站 IP 地址，以便与目标系统通信。 这样，您就不必为每个 ISE 在这些目标系统设置额外的防火墙打开。

* 增加了对运行持续时间、存储保留、吞吐量、HTTP 请求和响应超时、消息大小和自定义连接器请求的限制。 有关详细信息，请参阅[Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md)。

> [!NOTE]
> 某些 Azure 虚拟网络使用专用终结点[（Azure 专用链接](../private-link/private-link-overview.md)） 来提供对 Azure PaaS 服务（如 Azure 存储、Azure 宇宙数据库或 Azure SQL 数据库、合作伙伴服务或在 Azure 上托管的客户服务）的访问。 如果逻辑应用需要访问使用专用终结点的虚拟网络，则必须在 ISE 中创建、部署和运行这些逻辑应用。

创建 ISE 时，Azure*会将*该 ISE 注入或部署到 Azure 虚拟网络。 然后，可以使用此 ISE 作为需要访问的逻辑应用和集成帐户的位置。

![选择集成服务环境](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

逻辑应用可以使用这些项目访问虚拟网络内部或连接到虚拟网络的资源，这些项目在与逻辑应用相同的 ISE 中运行：

* **CORE**- 标记的内置触发器或操作，如 HTTP 触发器或操作
* 该系统或服务的**ISE**标记连接器
* 自定义连接器

您仍可以使用没有**CORE**或**ISE**标签的连接器与 ISE 中的逻辑应用一起。 这些连接器在多租户逻辑应用服务中运行。 有关详细信息，请参阅以下部分：

* [隔离与多租户](#difference)
* [从集成服务环境进行连接](../connectors/apis-list.md#integration-service-environment)
* [ISE 连接器](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> 在 ISE 中运行的逻辑应用、内置触发器、内置操作和连接器使用不同于基于消耗的定价计划的定价计划。 有关详细信息，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价详细信息，请参阅[逻辑应用定价](../logic-apps/logic-apps-pricing.md)。

本概述介绍了有关 ISE 如何使逻辑应用直接访问 Azure 虚拟网络以及比较 ISE 和多租户逻辑应用服务之间的差异的详细信息。

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>隔离与多租户

在 ISE 中创建和运行逻辑应用时，您将获得与多租户逻辑应用服务相同的用户体验和类似功能。 您可以使用多租户逻辑应用服务中提供的所有相同的内置触发器、操作和托管连接器。 某些托管连接器提供其他 ISE 版本。 ISE 连接器和非 ISE 连接器之间的差异存在于它们运行的位置和在 ISE 中工作时逻辑应用设计器中的标签。

![ISE 中带标签和没有标签的连接器](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* 内置触发器和操作显示**CORE**标签。 它们始终与逻辑应用在同一 ISE 中运行。 显示**ISE**标签的托管连接器也运行在与逻辑应用相同的 ISE 中。

  例如，以下是一些提供 ISE 版本的连接器：

  * Azure Blob 存储、文件存储和表存储
  * Azure 队列、Azure 服务总线、Azure 事件中心和 IBM MQ
  * FTP 和 SFTP-SSH
  * SQL 服务器、Azure SQL 数据仓库、Azure 宇宙数据库
  * AS2、X12 和 EDIFACT

* 不显示任何其他标签的托管连接器始终在多租户逻辑应用服务中运行，但仍可以在 ISE 托管的逻辑应用中使用这些连接器。

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>访问本地系统

要访问连接到 Azure 虚拟网络的本地系统或数据源，ISE 中的逻辑应用可以使用以下项：

* HTTP 操作

* 该系统的 ISE 标记连接器

  > [!NOTE]
  > 要在[集成服务环境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中使用 SQL Server 连接器的 Windows 身份验证，请使用连接器的非 ISE 版本与[本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 ISE 标记的版本不支持 Windows 身份验证。

* 自定义连接器

  * 如果您有需要本地数据网关的自定义连接器，并且在 ISE 之外创建了这些连接器，则 ISE 中的逻辑应用也可以使用这些连接器。

  * 在 ISE 中创建的自定义连接器与本地数据网关不起作用。 但是，这些连接器可以直接访问连接到托管 ISE 的虚拟网络的本地数据源。 因此，在与这些资源通信时，ISE 中的逻辑应用很可能不需要数据网关。

对于未连接到虚拟网络或没有 ISE 标记连接器的本地系统，必须先[设置本地数据网关](../logic-apps/logic-apps-gateway-install.md)，然后才能将逻辑应用连接到这些系统。

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU

创建 ISE 时，可以选择开发人员 SKU 或高级 SKU。 以下是这些 SKU 之间的差异：

* **开发人员**

  提供低成本的 ISE，可用于实验、开发和测试，但不能用于生产或性能测试。 开发人员 SKU 包括内置触发器和操作、标准连接器、企业连接器和单个[免费层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帐户，每月固定价格。 但是，此 SKU 不包括任何服务级别协议 （SLA）、扩展容量的选项或回收过程中的冗余，这意味着您可能会遇到延迟或停机。

* **溢价**

  提供可用于生产的 ISE，包括 SLA 支持、内置触发器和操作、标准连接器、企业连接器、单个[标准层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)集成帐户、扩展容量的选项以及回收过程中的冗余，按固定月价进行。

> [!IMPORTANT]
> SKU 选项仅在 ISE 创建时可用，以后无法更改。

有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。 要了解 ISEs 的定价和计费的工作原理，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE 端点访问

创建 ISE 时，可以选择使用内部或外部访问终结点。 您的选择确定 ISE 中逻辑应用上的请求或 Webhook 触发器是否可以从虚拟网络外部接收呼叫。

这些终结点还会影响访问逻辑应用运行历史记录中的输入和输出的方式。

* **内部**：允许调用 ISE 中的逻辑应用的专用终结点，您可以在其中*仅从虚拟网络内部*查看和访问运行历史记录中的逻辑应用的输入和输出

* **外部**：允许调用 ISE 中的逻辑应用的公共终结点，您可以在其中*从虚拟网络外部*查看和访问逻辑应用在运行历史记录中的输入和输出。 如果使用网络安全组 （NSG），请确保它们已设置入站规则，以允许访问运行历史记录的输入和输出。 有关详细信息，请参阅启用[ISE 的访问](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)。

> [!IMPORTANT]
> 访问终结点选项仅在 ISE 创建时可用，以后无法更改。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>集成帐户与 ISE

可以在集成服务环境 (ISE) 中将集成帐户与逻辑应用结合使用。 但是，这些集成帐户必须使用相同的 ISE** 作为链接的逻辑应用。 ISE 中的逻辑应用只能引用同一 ISE 中的这些集成帐户。 创建集成帐户时，可以选择 ISE 作为集成帐户的位置。 要了解使用 ISE 的集成帐户的定价和计费的工作原理，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。

## <a name="next-steps"></a>后续步骤

* [从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* 详细了解 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
* 了解[Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
