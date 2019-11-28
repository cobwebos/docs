---
title: 了解如何将预留折扣应用于 Azure 存储 | Microsoft Docs
description: 了解如何向块 blob 和 Azure Data Lake Storage Gen2 资源应用 Azure 存储预留容量折扣。
author: tamram
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: tamram
ms.openlocfilehash: 4dd224036989b4ce78ee382b539e99c12015128e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225751"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>了解如何将预留折扣应用于 Azure 存储

购买 Azure 存储预留容量后，预留折扣自动应用于与预留期匹配的块 blob 和 Azure Data Lake Storage Gen2 资源。 预留折扣仅适用于存储容量。 带宽和请求费率按即用即付费率收费。

有关 Azure 存储预留容量的详细信息，请参阅[借助预留容量优化 Blob 存储的成本](../storage/blobs/storage-blob-reserved-capacity.md)。

有关 Azure 存储预留定价的信息，请参阅[块 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)和 [Azure Data Lake Storage Gen 2 定价](https://azure.microsoft.com/pricing/details/storage/data-lake/)。

## <a name="how-the-reservation-discount-is-applied"></a>如何应用预留折扣

按小时向块 blob 和 Azure Data Lake Storage Gen2 资源应用 Azure 存储预留容量折扣。

Azure 存储预留容量折扣的性质是“不用就会失效”。 如果没有任何块 blob 或 Azure Data Lake Storage Gen2 资源达到某个小时的预留期限，则会丢失该小时的预留量。 不能结转未使用的预留小时数。

删除资源时，预留折扣自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，预留时数将丢失。

## <a name="discount-examples"></a>折扣示例

以下示例演示如何根据部署应用 Azure 存储预留容量折扣。

假设你在美国西部 2 区域购买了一年期 100 TB 的预留容量。 预留适用于热访问层中的本地冗余存储 (LRS)。

假设此示例预留的成本为 18,540 美元。 你可以选择提前支付全部金额或者在接下来的十二个月每月固定分期付款 1,545 美元。

就这些示例而言，假设你已注册了按月预留付款计划。 以下场景说明预留容量未充分使用或过量使用会发生的情况。

### <a name="underusing-your-capacity"></a>未充分使用容量

假设在预留期内的某一个小时，你只使用了 100 TB 预留容量中的 80 TB。 剩余的 20 TB 未在该小时应用，也不会继续结转。

### <a name="overusing-your-capacity"></a>超额使用容量

假设在预留期内的某一个小时，你使用了 101 TB 的存储容量。 预留折扣应用于 100 TB 的数据，该小时剩余的 1 TB 按即用即付费率收费。 如果在接下来的一小时内，你的使用量变为 100 TB，则预留涵盖所有使用量。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [借助预留容量优化 Blob 存储的成本](../storage/blobs/storage-blob-reserved-capacity.md)
- [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
