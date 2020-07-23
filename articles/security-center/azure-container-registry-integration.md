---
title: Azure 安全中心和 Azure 容器注册表
description: 了解 Azure 安全中心与 Azure 容器注册表的集成
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: f3ef633ff0271d74eea7320faadf17685976d3b6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970461"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Azure 容器注册表与安全中心的集成

Azure 容器注册表 (ACR) 是一种托管的专用 Docker 注册表服务，它在中心注册表中存储和管理用于 Azure 部署的容器映像。 它基于开源 Docker 注册表 2.0。

如果你使用的是 Azure 安全中心标准层，则可以添加容器注册表捆绑包。 此可选功能使你可以更深入地了解基于 ARM 的注册表中的映像的漏洞。 在订阅级别启用或禁用涵盖订阅中所有注册表的捆绑包。 此功能按映像收费，如[定价页](security-center-pricing.md)所示。 启用容器注册表捆绑包可确保安全中心准备好扫描推送到注册表的映像。 


## <a name="availability"></a>可用性

- 发布状态：**公开上市**
- 必需的角色：**安全读取器**和[Azure 容器注册表读取器角色](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- 云： 
    - ✔ 商业云
    - ✘美国政府云
    - ✘中国政府云，其他 gov 云


## <a name="when-are-images-scanned"></a>何时扫描图像？

每当将映像推送到注册表时，安全中心都会自动扫描该映像。 若要触发映像扫描，请将该映像推送到存储库。

扫描完成时（通常在大约10分钟后，最长可达40分钟），会提供如下所示的安全中心建议：

[![有关在 Azure 容器注册表 (ACR) 托管映像中发现的漏洞的 Azure 安全中心建议示例](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>集成的好处

安全中心在订阅中识别基于 ARM 的 ACR 注册表并无缝提供以下内容：

* **Azure 原生漏洞扫描**，适用于所有推送的 Linux 映像。 安全中心使用行业领先的漏洞扫描供应商 Qualys 提供的扫描程序来扫描映像。 默认情况下，此原生解决方案已无缝集成。

* **安全建议**，适用于具有已知漏洞的 Linux 映像。 安全中心提供每个报告的漏洞的详细信息和严重性分类。 此外，它还指导如何修正在推送到注册表的每个映像上发现的特定漏洞。

![Azure 安全中心和 Azure 容器注册表 (ACR) 综合概述](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="acr-with-security-center-faq"></a>具有安全中心常见问题的 ACR

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Azure 安全中心可以扫描哪些类型的映像？
安全中心扫描基于 Linux OS 的映像，这些映像提供 shell 访问权限。 

Qualys 扫描程序不支持极简映像，例如 [Docker 暂存](https://hub.docker.com/_/scratch/)映像或仅包含应用程序及其运行时依赖项而没有包管理器、shell 或 OS 的“Distroless”映像。

### <a name="how-does-azure-security-center-scan-an-image"></a>Azure 安全中心如何扫描映像？
先从注册表中拉取映像。 然后在独立沙盒中运行该映像，沙盒中包含的 Qualys 扫描程序会提取已知漏洞的列表。

安全中心会对扫描程序的扫描结果进行筛选和分类。 当映像正常运行时，安全中心会将其标为正常。 安全中心仅为存在待解决问题的映像生成安全建议。 安全中心仅在出现问题时发出通知，这样会降低发送不必要的信息警报的可能性。

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Azure 安全中心扫描映像的频率如何？
每次推送都会触发映像扫描。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>是否可以通过 REST API 获取扫描结果？
是的。 结果位于[子评估 Rest API](/rest/api/securitycenter/subassessments/list/) 下。 此外，还可以对所有资源使用 Azure Resource Graph (ARG)，一个类似于 Kusto 的 API：查询可以提取特定扫描。
 



## <a name="next-steps"></a>后续步骤

若要详细了解安全中心的容器安全功能，请参阅：

* [Azure 安全中心和容器安全性](container-security.md)

* [与 Azure Kubernetes 服务集成](azure-kubernetes-service-integration.md)

* [虚拟机保护](security-center-virtual-machine-protection.md) - 介绍安全中心的建议
