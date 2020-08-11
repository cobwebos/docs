---
title: Azure 安全中心和 Azure 容器注册表
description: 了解如何通过 Azure 安全中心扫描容器注册表
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2020
ms.author: memildin
ms.openlocfilehash: 718f9a29b70dab34269c959ccd62452e56a32d72
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056595"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>安全中心扫描 Azure 容器注册表映像

Azure 容器注册表 (ACR) 是一种托管的专用 Docker 注册表服务，它在中心注册表中存储和管理用于 Azure 部署的容器映像。 它基于开源 Docker 注册表 2.0。

如果你使用的是 Azure 安全中心标准层，则可以添加容器注册表捆绑包。 此可选功能使你能够更深入地了解 Azure 资源管理器的注册表中的映像的漏洞。 在订阅级别启用或禁用涵盖订阅中所有注册表的捆绑包。 此功能按映像收费，如[定价页](security-center-pricing.md)所示。 启用容器注册表捆绑包可确保安全中心准备好扫描推送到注册表的映像。 

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式版|
|计价|标准层|
|支持的注册表和映像：|![是 ](./media/icons/yes-icon.png) 可以从公共 internet 访问并提供外壳访问的 Linux 托管 ACR 注册表。<br>![无 ](./media/icons/no-icon.png) Windows 承载的 ACR 注册表。<br>![无 ](./media/icons/no-icon.png) "专用" 注册表项-安全中心要求可以从公共 internet 访问注册表。 安全中心当前无法使用防火墙、服务终结点或专用终结点（如 Azure Private Link）连接或扫描访问权限。<br>![没有任何 ](./media/icons/no-icon.png) 超级最简单映像（例如[Docker 暂存](https://hub.docker.com/_/scratch/)映像）或仅包含应用程序和其运行时依赖项的 "Distroless" 映像，无需包管理器、shell 或 OS。|
|必需的角色和权限：|**安全读取器**和[Azure 容器注册表读取器角色](https://docs.microsoft.com/azure/container-registry/container-registry-roles)|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权 (US Gov、中国 Gov、其他 Gov) |
|||




## <a name="when-are-images-scanned"></a>何时扫描图像？

每当将映像推送到注册表时，安全中心都会自动扫描该映像。 若要触发映像扫描，请将该映像推送到存储库。

扫描完成后 (通常为大约2分钟后，但最多可以有15分钟的时间) ，如以下安全中心建议所示：

[![有关在 Azure 容器注册表 (ACR) 托管映像中发现的漏洞的 Azure 安全中心建议示例](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>集成的好处

安全中心在订阅中识别基于 Azure 资源管理器的 ACR 注册表并无缝提供：

* **Azure 原生漏洞扫描**，适用于所有推送的 Linux 映像。 安全中心使用行业领先的漏洞扫描供应商 Qualys 提供的扫描程序来扫描映像。 默认情况下，此原生解决方案已无缝集成。

* **安全建议**，适用于具有已知漏洞的 Linux 映像。 安全中心提供每个报告的漏洞的详细信息和严重性分类。 此外，它还指导如何修正在推送到注册表的每个映像上发现的特定漏洞。

![Azure 安全中心和 Azure 容器注册表 (ACR) 综合概述](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Azure 容器注册表映像扫描常见问题解答

### <a name="how-does-security-center-scan-an-image"></a>安全中心如何扫描图像？
先从注册表中拉取映像。 然后在独立沙盒中运行该映像，沙盒中包含的 Qualys 扫描程序会提取已知漏洞的列表。

安全中心会对扫描程序的扫描结果进行筛选和分类。 当映像正常运行时，安全中心会将其标为正常。 安全中心仅为存在待解决问题的映像生成安全建议。 安全中心仅在出现问题时发出通知，这样会降低发送不必要的信息警报的可能性。

### <a name="how-often-does-security-center-scan-my-images"></a>安全中心扫描图像的频率如何？
每次推送都会触发映像扫描。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>是否可以通过 REST API 获取扫描结果？
是的。 结果位于[子评估 Rest API](/rest/api/securitycenter/subassessments/list/) 下。 此外，还可以对所有资源使用 Azure Resource Graph (ARG)，一个类似于 Kusto 的 API：查询可以提取特定扫描。
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>扫描哪些注册表类型？ 哪些类型会计费？
"[可用性" 部分](#availability)列出容器注册表绑定支持的容器注册表类型。 

如果不受支持的注册表连接到 Azure 订阅，则不会对其进行扫描，并且不会向你收费。


## <a name="next-steps"></a>后续步骤

若要详细了解安全中心的容器安全功能，请参阅：

* [Azure 安全中心和容器安全性](container-security.md)

* [与 Azure Kubernetes 服务集成](azure-kubernetes-service-integration.md)

* [虚拟机保护](security-center-virtual-machine-protection.md) - 介绍安全中心的建议
