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
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 069ce6ca1e76a9bd954031708702c973387abbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205987"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Azure 容器注册表与安全中心的集成

Azure 容器注册表 （ACR） 是一种托管的专用 Docker 注册表服务，用于在中央注册表中存储和管理 Azure 部署的容器映像。 它基于开源 Docker 注册表 2.0。

如果您位于 Azure 安全中心的标准层，则可以添加容器注册表捆绑包。 此可选功能可更深入地了解基于 ARM 的注册表中图像的漏洞。 在订阅级别启用或禁用捆绑包，以涵盖订阅中的所有注册表。 此功能按图像收费，如[定价页上](security-center-pricing.md)所示。 启用容器注册表包，可确保安全中心准备好扫描推送到注册表的图像。 

每当图像推送到注册表时，安全中心会自动扫描该图像。 要触发映像的扫描，请将其推送到存储库。

扫描完成后（通常在大约 10 分钟后），安全中心建议中提供了调查结果，如下所示：

[![有关 Azure 容器注册表 （ACR） 托管映像中发现的漏洞的 Azure 安全中心建议示例](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>集成的好处

安全中心在订阅中标识基于 ARM 的 ACR 注册表，并无缝提供：

* 针对所有推送的 Linux 映像的**Azure 本机漏洞扫描**。 安全中心使用业界领先的漏洞扫描供应商 Qualys 的扫描仪扫描图像。 默认情况下，此本机解决方案是无缝集成的。

* 具有已知漏洞的 Linux 映像**的安全建议**。 安全中心提供每个报告漏洞的详细信息和严重性分类。 此外，它还提供了如何修复推送到注册表的每个映像上发现的特定漏洞的指导。

![Azure 安全中心和 Azure 容器注册表 （ACR） 高级概述](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>后续步骤

要了解有关安全中心的容器安全功能的更多，请参阅：

* [Azure 安全中心和容器安全性](container-security.md)

* [与 Azure Kubernetes 服务集成](azure-kubernetes-service-integration.md)

* [虚拟机保护](security-center-virtual-machine-protection.md)- 描述安全中心的建议
