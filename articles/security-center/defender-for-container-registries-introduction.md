---
title: 适用于容器注册表的 Azure Defender - 优点和功能
description: 了解容器注册表的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 12264a79ee5428e98d6cf7d37bef6706295e68dc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448380"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>适用于容器注册表的 Azure Defender 简介

Azure 容器注册表 (ACR) 是一种托管的专用 Docker 注册表服务，它在中心注册表中存储和管理用于 Azure 部署的容器映像。 它基于开源 Docker 注册表 2.0。

若要保护订阅中所有基于 Azure 资源管理器的注册表项，请在订阅级别启用**适用于容器注册表的 Azure Defender**。 然后，安全中心将扫描推送到注册表、导入注册表或在过去 30 天内提取的任何映像。 此功能按映像收费。

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>适用于容器注册表的 Azure Defender 有哪些优点？

安全中心会标识订阅中基于 Azure 资源管理器的 ACR 注册表，并无缝为注册表映像提供 Azure 原生漏洞评估和管理。

**适用于容器注册表的 Azure Defender** 包含一个漏洞扫描程序，可扫描基于 Azure 资源管理器的 Azure Container Registry 注册表中的映像，使你能够更深入地了解映像漏洞。 该集成的扫描程序由业界领先的漏洞扫描供应商 Qualys 提供支持。

当 Qualys 或安全中心发现问题时，你将在安全中心仪表板中收到通知。 安全中心会针对每个漏洞提供可行的建议、严重性分类，以及有关如何修正问题的指南。 若要详细了解安全中心针对容器提供的建议，请参阅[建议的参考列表](recommendations-reference.md#recs-containers)。

安全中心会对扫描程序的扫描结果进行筛选和分类。 当映像正常运行时，安全中心会将其标为正常。 安全中心仅为存在待解决问题的映像生成安全建议。 安全中心针对每个报告的漏洞提供相关详细信息和严重性类别。 此外，它还提供有关如何修正每个映像上发现的特定漏洞的指导内容。

安全中心仅在出现问题时发出通知，这样会降低发送不必要的信息警报的可能性。


## <a name="when-are-images-scanned"></a>何时扫描映像？

映像扫描有三个触发器：

- **在推送时** - 每当向注册表推送映像时，安全中心会自动扫描该映像。 若要触发映像扫描，请将该映像推送到存储库。

- **最近拉取** - 由于每天都会发现新漏洞，适用于容器注册表的 Azure Defender 还会扫描过去 30 天内拉取的任何映像。 重新扫描不产生额外费用；如前所述，每个映像只需支付一次扫描费用。

- **导入时** - Azure 容器注册表具有导入工具，用于将 Docker 中心、Microsoft 容器注册表或其他 Azure 容器注册表中的映像引入你的注册表。 **适用于容器注册表的 Azure Defender** 扫描导入的任何受支持的映像。 请参阅[将容器映像导入容器注册表](../container-registry/container-registry-import-images.md)，了解详细信息。
 
扫描通常在 2 分钟内完成，最多可能需要 15 分钟。 扫描结果以“安全中心建议”的形式提供，如下所示：

[![有关在 Azure 容器注册表 (ACR) 托管映像中发现的漏洞的 Azure 安全中心建议示例](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>安全中心如何使用 Azure 容器注册表

下面是有关使用安全中心保护注册表的组件和优点的概括性关系图。

![Azure 安全中心和 Azure 容器注册表 (ACR) 综合概述](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Azure 容器注册表映像扫描常见问题解答

### <a name="how-does-security-center-scan-an-image"></a>安全中心如何扫描图像？
先从注册表中拉取映像。 然后在独立沙盒中运行该映像，沙盒中包含的 Qualys 扫描程序会提取已知漏洞的列表。

安全中心会对扫描程序的扫描结果进行筛选和分类。 当映像正常运行时，安全中心会将其标为正常。 安全中心仅为存在待解决问题的映像生成安全建议。 安全中心仅在出现问题时发出通知，这样会降低发送不必要的信息警报的可能性。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>是否可以通过 REST API 获取扫描结果？
是的。 结果位于[子评估 Rest API](/rest/api/securitycenter/subassessments/list/) 下。 此外，还可以对所有资源使用 Azure Resource Graph (ARG)，一个类似于 Kusto 的 API：查询可以提取特定扫描。
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>会扫描哪些类型的注册表？ 会对哪些类型计费？
有关适用于容器注册表的 Azure Defender 支持的容器注册表类型的列表，请参阅[可用性](defender-for-container-registries-usage.md#availability)。

如果将不支持的注册表连接到 Azure 订阅，不会对其进行扫描，也不会收取扫描费。


## <a name="next-steps"></a>后续步骤

若要详细了解安全中心的容器安全功能，请参阅：

- [Azure 安全中心和容器安全性](container-security.md)

- [适用于 Kubernetes 的 Azure Defender 简介](defender-for-kubernetes-introduction.md)


