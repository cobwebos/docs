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
ms.openlocfilehash: f2a07774fa23173738bc33907dd00017ca260fd9
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196386"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Azure 容器注册表与安全中心集成（预览版）

Azure 容器注册表（ACR）是一项托管的专用 Docker 注册表服务，用于在中央注册表中存储和管理 Azure 部署的容器映像。 它基于开源 Docker 注册表2.0。

为了更深入地了解你的注册表和映像漏洞，Azure 安全中心的标准层用户可以启用可选的容器注册表绑定。 有关详细信息，请参阅[定价](security-center-pricing.md)。 启用捆绑包后，只要将映像推送到注册表，安全中心就会自动扫描 ACR 服务中的映像。 扫描完成时（通常在大约10分钟后），安全中心提供的结果如下所示：

[![示例 Azure 安全中心建议，了解 Azure 容器注册表（ACR）托管图像中发现的漏洞](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>集成的好处

安全中心标识订阅中的 ACR 注册表并无缝提供：

* 针对所有推送的 Linux 映像的**Azure 本机漏洞扫描**。 安全中心使用 Qualys 中业界领先的漏洞扫描供应商提供的扫描程序扫描映像。 默认情况下，此本机解决方案无缝集成。

* 针对具有已知漏洞的 Linux 映像的**安全建议**。 安全中心提供每个报告的漏洞和严重性分类的详细信息。 此外，它还提供了有关如何修正推送到注册表的每个映像上发现的特定漏洞的指南。

![Azure 安全中心和 Azure 容器注册表（ACR）高级概述](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>后续步骤

若要了解有关安全中心容器安全功能的详细信息，请参阅：

* [Azure 安全中心和容器安全性](container-security.md)

* [与 Azure Kubernetes 服务集成](azure-kubernetes-service-integration.md)

* [虚拟机保护](security-center-virtual-machine-protection.md)-介绍安全中心的建议