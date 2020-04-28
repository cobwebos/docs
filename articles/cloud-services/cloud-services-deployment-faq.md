---
title: Microsoft Azure 云服务部署常见问题解答 | Microsoft Docs
description: 本文列出有关 Microsoft Azure 云服务的部署的常见问题。
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 63a219078927e9001a8eb4085c722e7ec8d2fac9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75980635"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure 云服务的部署问题：常见问题 (FAQ)

本文包含 [Microsoft Azure 云服务](https://azure.microsoft.com/services/cloud-services)的常见部署问题。 还可以参阅[云服务 VM 大小页面](cloud-services-sizes-specs.md)，了解大小信息。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>如果生产槽中存在现有的部署，将云服务部署到过渡槽为何有时失败并出现资源分配错误？
如果某个云服务在任一槽中存在部署，则会将整个云服务固定到特定的群集。 这意味着，如果生产槽中已存在部署，则只能将新的过渡部署分配到与生产槽相同的群集中。

如果云服务所在的群集没有足够的物理计算资源用于满足部署请求，则会发生分配失败。

有关解决此类分配失败的帮助，请参阅[云服务分配失败：解决方法](cloud-services-allocation-failures.md#solutions)。

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>为何纵向扩展或横向扩展云服务部署有时会导致分配失败？
部署云服务后，该服务通常会固定到特定的群集。 这意味着，纵向扩展/横向扩展现有的云服务时必须在同一群集中分配新实例。 如果群集接近容量限制或所需的 VM 大小/类型不可用，则请求可能会失败。

有关解决此类分配失败的帮助，请参阅[云服务分配失败：解决方法](cloud-services-allocation-failures.md#solutions)。

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>为何将云服务部署到地缘组有时会导致分配失败？
进行新的目标为空云服务的部署时，可以通过该区域任何群集中的结构对部署进行分配，除非已将云服务固定到地缘组。 将会在相同的群集中尝试部署到相同的地缘组。 如果群集已接近容量限制，则请求可能失败。

有关解决此类分配失败的帮助，请参阅[云服务分配失败：解决方法](cloud-services-allocation-failures.md#solutions)。

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>为何更改 VM 大小或将新 VM 添加到现有云服务有时会导致分配失败？
数据中心内的群集可能具有不同的计算机类型配置（例如，系列、Av2 系列、D 系列、Dv2 系列、G 系列、H 系列，等等）。 但是，并非所有群集都一定要包含所有类型的 VM。 例如，如果尝试将 D 系列 VM 添加到已在仅限 A 系列的群集中部署的云服务，则会发生分配失败。 如果尝试更改 VM SKU 大小（例如，从 A 系列切换到 D 系列），也会发生此问题。

有关解决此类分配失败的帮助，请参阅[云服务分配失败：解决方法](cloud-services-allocation-failures.md#solutions)。

要查看你所在区域的可用大小，请参阅 [Microsoft Azure：产品区域可用性](https://azure.microsoft.com/regions/services)。

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>为什么我的订阅或服务的限制/配额/约束有时会导致部署云服务失败？
如果需要分配的资源超过服务所在区域/数据中心级别允许的默认或最大配额，则云服务部署可能会失败。 有关详细信息，请参阅[云服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits)。

还可以在门户上跟踪订阅的当前使用情况/配额：Azure门户 => 订阅 => \<相应订阅> =>“使用情况 + 配额”。

资源使用情况/相关消耗信息也可以通过 Azure 计费 API 检索。 请参阅 [Azure 资源使用状况 API（预览）](../cost-management-billing/manage/usage-rate-card-overview.md#azure-resource-usage-api-preview)。

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>如何在不重新部署已部署云服务虚拟机的情况下更改其大小？
你无法在不重新部署已部署云服务虚拟机的情况下更改其大小。 虚拟机大小内置在 CSDEF 中，只能通过重新部署进行更新。

有关详细信息，请参阅[如何更新云服务](cloud-services-update-azure-service.md)。

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>使用 Azure 资源管理器存储帐户时，为什么不能够通过服务管理 API 或 PowerShell 部署云服务？ 

由于云服务不是直接与 Azure 资源管理器模型兼容的经典资源，因此不能将其与 Azure 资源管理器存储帐户相关联。 下面是几个选项： 

- 通过 REST API 部署。

    通过服务管理 REST API 部署时，可以通过指定指向 blob 存储（同时使用经典和 Azure 资源管理器存储帐户）的 SAS URL 绕过限制。 在[此处](/previous-versions/azure/reference/ee460813(v=azure.100))阅读有关 PackageUrl 属性的详细信息。

- 通过 [Azure 门户](https://portal.azure.com)部署。

    这将从 [Azure 门户](https://portal.azure.com)进行，因为调用将通过一个代理/填充程序完成，该代理/填充程序使得 Azure 资源管理器可以与经典资源通信。 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>为什么 Azure 门户要求我提供部署所需的存储帐户？

在经典门户中，包直接上传到管理 API 层，然后 API 层暂时将其放入内部存储帐户。  API 层并不是文件上传服务，因此这个过程会导致性能和可伸缩性问题。  在 Azure 门户中（资源管理器部署模型），我们越过了先上传到 API 层这一临时步骤，因此实现了更快、更可靠的部署。

所需成本很少，并且可以在所有部署中重复使用同一存储帐户。 可以使用[存储成本计算器](https://azure.microsoft.com/pricing/calculator/#storage1)确定上传服务包 (CSPKG)、下载 CSPKG 以及之后删除 CSPKG 的成本。
