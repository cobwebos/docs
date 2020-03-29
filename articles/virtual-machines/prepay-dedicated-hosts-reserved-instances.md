---
title: 为 Azure 专用主机预付费用以节省资金
description: 了解如何购买 Azure 专用主机预留实例以节省计算成本。
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 57123abfe7f343a75d264d43afb88f9de1409e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207740"
---
# <a name="save-costs-with-a-reserved-instance-of-azure-dedicated-hosts"></a>使用 Azure 专用主机的预留实例节省成本

提交 Azure 专用主机的保留实例时，可以节省资金。 预留折扣将自动应用于与预留范围和属性匹配的正在运行的专用主机的数量。 您无需将预留分配给专用主机以获得折扣。 预留实例购买仅涵盖您的使用的计算部分，并且确实包括软件许可成本。 请参阅[虚拟机的 Azure 专用主机概述](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)。

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>购买前确定正确的专用主机 SKU


在购买预订之前，您应该确定您需要哪个专用主机。 SKU 是为表示 VM 系列和类型的专用主机定义的。 

首先，通过查看[Windows 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)或[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)的支持大小来标识 VM 系列。

接下来，检查 Azure 专用主机上是否支持它。 [Azure 专用主机定价](https://aka.ms/ADHPricing)页包含专用主机 SKU 的完整列表、其 CPU 信息以及各种定价选项（包括预留实例）。

您可能会发现多个 SKU 支持 VM 系列（具有不同类型）。 通过比较主机的容量（vCPU 数量）确定最佳 SKU。 请注意，您将能够将预留应用于支持同一 VM 系列的多个专用主机 SKU（例如DSv3_Type1和DSv3_Type2），但不能跨不同的 VM 系列（如 DSv3 和 ESv3）。



## <a name="purchase-restriction-considerations"></a>购买限制注意事项

保留实例可用于大多数专用主机大小，但有一些例外。

预订折扣不适用于以下事项：

- **云** - 预订不适用于在德国或中国地区购买。

- **配额不足** - 限定为单个订阅的预留必须在新保留实例的订阅中具有 vCPU 配额。 例如，如果目标订阅的配额限制为 DSv3 系列 10 vCPU，则无法购买支持此系列的预留专用主机。 预留的配额检查包括已在订阅中部署的 VM 和专用主机。 您可以创建 [配额增加请求](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) 以解决此问题。

- **容量限制** - 在极少数情况下，Azure 会限制为专用主机 SKU 子集购买新预留，因为区域中的容量较低。

## <a name="buy-a-reservation"></a>购买预留项

您可以在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中购买 Azure 专用主机实例的保留实例。

提前 [或按月付款](https://docs.microsoft.com/azure/billing/billing-monthly-payments-reservations)支付预订费用。 这些要求适用于购买预留的专用主机实例：

- 您必须在至少一个 EA 订阅或具有即用即付费率的订阅中担任所有者角色。

- 对于 EA 订阅，必须在 [EA 门户](https://ea.azure.com/)中启用 **"添加预留实例"** 选项。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。

- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理才能购买预留。

若要购买实例：

1. 登录到 Azure [门户](https://portal.azure.com/)。

2. 选择 **所有服务** \> **预订**。

3. 选择 **"添加"** 以购买新的预订，然后单击 **"专用主机**"。

4. 输入必填字段。 运行与您选择的属性匹配的专用主机实例有资格获得预留折扣。 获得折扣的专用主机实例的实际数量取决于所选的范围和数量。

如果您有 EA 协议，则可以使用 **"添加更多"选项** 快速添加其他实例。 该选项不适用于其他订阅类型。

| **字段**           | **说明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 订阅        | 用于支付预订费用的订阅。 订阅上的付款方式将收取预订费用。 订阅类型必须是企业协议（产品/服务编号：MS-AZR-0017P 或 MS-AZR-0148P）或 Microsoft 客户协议或具有即用即付费率的个人订阅（产品编号：MS-AZR-0003P 或 MS-AZR-0023P）。 费用从货币承诺余额中扣除（如有）或按超额收取。 对于具有即用即付费率的订阅，费用将计入订阅上的信用卡或发票付款方式。 |
| 范围               | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择：                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 区域              | 预订涵盖的 Azure 区域。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 专用主机大小 | 专用主机实例的大小。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| 术语                | 一年或三年。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 数量            | 预订中购买的实例数。 数量是运行的专用主机实例的数量，这些实例可以获得计费折扣。                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **单个资源组范围** – 仅将预留折扣应用于所选资源组中的匹配资源。

- **单个订阅范围** – 将预留折扣应用于所选订阅中的匹配资源。

- **共享范围** – 将预留折扣应用于计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文是注册。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。

## <a name="usage-data-and-reservation-utilization"></a>使用数据和预订利用率

对于获得预留折扣的使用量，使用数据的有效价格为零。 您可以看到哪个 VM 实例为每个预留提供了预留折扣。

有关预订折扣如何显示在使用情况数据中的详细信息，请参阅 [了解企业注册的](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) Azure 预留使用情况（如果您是 EA 客户）。 如果您有单独的订阅，请参阅 [了解即用即付订阅的 Azure 预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)。

## <a name="change-a-reservation-after-purchase"></a>购买后更改预订

可以在购买后对预留进行以下类型的更改：

- 更新预留范围

- 实例大小灵活性（如果适用）

- 所有权

您还可以将预留拆分为较小的块，并将已拆分的预留合并。 任何更改都不会导致新的商业交易或更改预订的结束日期。

购买后不能直接进行以下类型的更改：

- 现有保留区域

- SKU

- 数量

- Duration

但是，如果要进行更改，可以 *交换* 预订。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助服务交换和退款](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果您有疑问或需要帮助， [请创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

要了解如何管理预留，请参阅 [管理 Azure 预留](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)。

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预留？](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [使用 Azure 专用主机](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [专用主机定价](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [管理 Azure 中的预留](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [了解预留折扣的应用方式](https://docs.microsoft.com/azure/billing/billing-understand-vm-reservation-charges)

- [了解采用即用即付费率的订阅的预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [了解企业合约的预留使用情况](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [预留未包含的 Windows 软件成本](https://docs.microsoft.com/azure/billing/billing-reserved-instance-windows-software-costs)

- [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](https://docs.microsoft.com/partner-center/azure-reservations)


