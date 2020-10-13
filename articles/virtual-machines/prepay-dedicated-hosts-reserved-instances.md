---
title: 预付 Azure 专用主机以节省资金
description: 了解如何购买 Azure 专用托管预订实例，以节省计算成本。
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: d7af95b9fe2f6d31faa239985f8e8165fd968372
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978588"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>通过 Azure 专用主机预留节省成本

当你提交到 Azure 专用主机的保留实例时，可以节省资金。 预订折扣将自动应用于与预订范围和属性匹配的运行的专用主机的数量。 无需将预订分配给专用主机即可获取折扣。 购买的预订实例仅涵盖使用情况的计算部分，并包括软件许可成本。 请参阅 [虚拟机的 Azure 专用主机概述](./dedicated-hosts.md)。

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>购买之前确定适当的专用主机 SKU


在购买预订之前，应确定所需的专用主机。 为代表 VM 系列和类型的专用主机定义 SKU。 

首先，请使用 [Windows 虚拟机](./sizes.md) 或 [Linux](./sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 的支持大小来识别 VM 系列。

接下来，请检查 Azure 专用主机上是否支持该选项。 [Azure 专用主机定价](https://aka.ms/ADHPricing) 页提供了专用主机 sku、其 CPU 信息和各种定价选项的完整列表， (包括) 的预订实例。

你可能会发现多个 Sku 支持不同类型) 的 VM 序列 (。 通过比较主机的容量 () 个 vcpu 数来确定最佳 SKU。 请注意，你将能够将你的预订应用于多个支持同一 VM 系列的专用主机 Sku (例如 DSv3_Type1 和 DSv3_Type2) 但不能跨不同 VM 系列 (如 DSv3 和 ESv3) 。



## <a name="purchase-restriction-considerations"></a>购买限制注意事项

保留实例适用于大多数专用主机大小，但有一些例外情况。

预订折扣不适用于以下各项：

- **云**  -预订不适用于德国或中国地区的购买。

- **配额不足**  -作用域为单个订阅的保留必须在订阅中为新的保留实例提供 vCPU 配额。 例如，如果目标订阅的配额限制为10个 vcpu （对于 DSv3 系列），则不能购买保留支持此系列的专用主机。 保留配额检查包括已在订阅中部署的 Vm 和专用主机。 你可以 [创建配额增加请求](../azure-portal/supportability/resource-manager-core-quotas-request.md)   以解决此问题。

- **容量限制**  -在极少数情况下，Azure 会限制专用主机 Sku 子集的新保留购买，因为区域中的容量不足。

## <a name="buy-a-reservation"></a>购买预留项

可以在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)中购买 Azure 专用主机实例的保留实例。

提前支付预订费用， [按月](../cost-management-billing/reservations/prepare-buy-reservation.md)支付。 这些要求适用于购买保留的专用主机实例：

- 对于至少一个 EA 订阅或具有即用即付费率的订阅，必须是所有者角色。

- 对于 EA 订阅， **Add Reserved Instances**   必须在 [ea 门户](https://ea.azure.com/)中启用 "添加保留实例" 选项。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。

- 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理才能购买预留。

若要购买实例，请执行以下操作：

1. 登录  [Azure 门户](https://portal.azure.com/)。

2. 选择 " **所有服务**   \>  **保留**"。

3. 选择 " **添加**   " 购买新的预订，并单击 " **专用主机**"。

4. 输入必填字段。 运行与所选属性匹配的专用主机实例，以获得预订折扣。 获取折扣的专用主机实例的实际数目取决于所选的范围和数量。

如果你有 EA 协议，则可以使用 " **添加更多" 选项**   快速添加其他实例。 选项不适用于其他订阅类型。

| **字段**           | **说明**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 订阅        | 用于支付预订费用的订阅。 将向订阅的付款方式收取预留的费用。 订阅类型必须是企业协议 (产品/服务编号： BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p) 或 Microsoft 客户协议，或者使用即用即付费率 (产品/服务，产品/服务，) 。 从货币承诺余额中扣除费用（如果可用）或作为超额收取费用。 对于使用即用即付费率的订阅，将对订阅上的信用卡或发票付款方式收取费用。 |
| 范围               | 预订的范围可以包含一个订阅或多个订阅（共享范围）。 如果选择：                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 区域              | 预订涵盖的 Azure 区域。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| 专用主机大小 | 专用主机实例的大小。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| 术语                | 一年或三年。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| 数量            | 预订中购买的实例数。 数量是可以获得计费折扣的正在运行的专用主机实例数。                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **单个资源组作用域**  -仅将预订折扣应用于所选资源组中的匹配资源。

- **单个订阅范围**  -将预订折扣应用于所选订阅中的匹配资源。

- **共享作用域**  -将预订折扣应用于计费上下文中符合条件的订阅中的匹配资源。 对于 EA 客户，计费上下文为 "注册"。 对于采用即用即付费率的单个订阅，计费范围是由帐户管理员创建的所有符合条件的订阅。

## <a name="usage-data-and-reservation-utilization"></a>使用情况数据和预订利用率

对于获得预留折扣的使用量，使用数据的有效价格为零。 你可以看到哪个 VM 实例收到每个预订的预订折扣。

有关如何在使用情况数据中显示预订折扣的详细信息，请参阅 [了解企业注册的 Azure 保留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)（   如果你是 EA 客户）。 如果有单独的订阅，请参阅 [了解即用即付订阅的 Azure 保留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)。

## <a name="change-a-reservation-after-purchase"></a>在购买后更改保留

可以在购买后对预留进行以下类型的更改：

- 更新预留范围

- 实例大小灵活性 (（如果适用）) 

- 所有权

还可以将保留拆分为较小的区块，并合并已拆分的保留项。 任何更改都不会导致新的商业交易，也不会更改预订的结束日期。

在购买后，不能进行以下类型的更改：

- 现有预订的区域

- SKU

- 数量

- 持续时间

不过，如果想要进行更改，则可以 *交换*   预订。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预订的自助服务交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请 [创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

若要了解如何管理预订，请参阅 [管理 Azure 预订](../cost-management-billing/reservations/manage-reserved-vm-instance.md)。

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [使用 Azure 专用主机](./dedicated-hosts.md)

- [专用主机定价](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [管理 Azure 中的预留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [了解预留折扣的应用方式](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [了解采用即用即付费率的订阅的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [了解企业合约的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [预留未包含的 Windows 软件成本](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [合作伙伴中心云解决方案提供商 (CSP) 计划中的 Azure 预订](/partner-center/azure-reservations)