---
title: 容器注册表的 Azure Defender-优点和功能
description: 了解 Azure Defender 对于容器注册表的优势和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 6254b78ad19e7034f78f7891d57a3474fee0c602
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301911"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Azure Defender for container 注册表简介

Azure 容器注册表 (ACR) 是一种托管的专用 Docker 注册表服务，它在中心注册表中存储和管理用于 Azure 部署的容器映像。 它基于开源 Docker 注册表 2.0。

若要在订阅中保护所有基于 Azure 资源管理器的注册表项，请在订阅级别 **为容器注册表启用 Azure Defender** 。 然后，安全中心将扫描推送到注册表、导入注册表或在过去30天内提取的任何图像的映像。 此功能按映像收费。

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Azure Defender 对容器注册表有哪些好处？

安全中心在订阅中识别基于 Azure 资源管理器的 ACR 注册表，并无缝为你的注册表映像提供 Azure 本机漏洞评估和管理。

**容器注册表的 Azure Defender** 包含漏洞扫描程序，可扫描 Azure 资源管理器 Azure 容器注册表注册表中的映像，并更深入地了解映像的漏洞。 集成扫描程序由 Qualys （业界领先的漏洞扫描供应商）提供支持。

当通过 Qualys 或安全中心发现问题时，你将在安全中心仪表板中收到通知。 安全中心会针对每个漏洞提供可行的建议、严重性分类，以及有关如何修正问题的指南。 若要详细了解安全中心针对容器提供的建议，请参阅[建议的参考列表](recommendations-reference.md#recs-containers)。

安全中心会对扫描程序的扫描结果进行筛选和分类。 当映像正常运行时，安全中心会将其标为正常。 安全中心仅为存在待解决问题的映像生成安全建议。 安全中心提供每个报告的漏洞和严重性分类的详细信息。 此外，它还提供了有关如何修正每个映像上发现的特定漏洞的指南。

安全中心仅在出现问题时发出通知，这样会降低发送不必要的信息警报的可能性。


## <a name="when-are-images-scanned"></a>何时扫描图像？

图像扫描有三个触发器：

- **推送** 时-每次将映像推送到注册表时，安全中心会自动扫描该映像。 若要触发映像扫描，请将该映像推送到存储库。

- **最近已拉取** -由于每天都发现了新的漏洞，因此 **容器注册表的 Azure Defender** 还会扫描过去30天内请求的任何映像。 重新扫描无额外费用;如上所述，每个映像只需支付一次费用。

- **导入时** -Azure 容器注册表具有导入工具，可用于将图像从 Docker 中心、Microsoft 容器注册表或其他 Azure 容器注册表中引入到注册表。 **Azure Defender for container 注册表** 将扫描您导入的任何受支持的映像。 在 [将容器映像导入到容器注册表](../container-registry/container-registry-import-images.md)中了解详细信息。
 
扫描通常在2分钟内完成，但可能需要最多15分钟。 此类结果作为安全中心建议提供，如下所示：

[![有关在 Azure 容器注册表 (ACR) 托管映像中发现的漏洞的 Azure 安全中心建议示例](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>安全中心如何与 Azure 容器注册表配合使用

下面是使用安全中心保护注册表的组件和优点的高级关系图。

![Azure 安全中心和 Azure 容器注册表 (ACR) 综合概述](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Azure 容器注册表映像扫描常见问题解答

### <a name="how-does-security-center-scan-an-image"></a>安全中心如何扫描图像？
先从注册表中拉取映像。 然后在独立沙盒中运行该映像，沙盒中包含的 Qualys 扫描程序会提取已知漏洞的列表。

安全中心会对扫描程序的扫描结果进行筛选和分类。 当映像正常运行时，安全中心会将其标为正常。 安全中心仅为存在待解决问题的映像生成安全建议。 安全中心仅在出现问题时发出通知，这样会降低发送不必要的信息警报的可能性。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>是否可以通过 REST API 获取扫描结果？
是的。 结果位于[子评估 Rest API](/rest/api/securitycenter/subassessments/list/) 下。 此外，还可以对所有资源使用 Azure Resource Graph (ARG)，一个类似于 Kusto 的 API：查询可以提取特定扫描。
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>扫描哪些注册表类型？ 哪些类型会计费？
有关 Azure Defender 为容器注册表支持的容器注册表类型的列表，请参阅 [可用性](defender-for-container-registries-usage.md#availability)。

如果将不受支持的注册表连接到 Azure 订阅，则不会对其进行扫描，也不会向你收费。


## <a name="next-steps"></a>后续步骤

若要详细了解安全中心的容器安全功能，请参阅：

- [Azure 安全中心和容器安全性](container-security.md)

- [Azure Defender for Kubernetes 简介](defender-for-kubernetes-introduction.md)


