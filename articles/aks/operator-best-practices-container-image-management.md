---
title: 操作员最佳做法 - Azure Kubernetes 服务 (AKS) 中的容器映像管理
description: 了解有关如何在 Azure Kubernetes 服务 (AKS) 中管理和保护容器映像的群集操作员最佳做法
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: efe72157f598c336248e407c57bce92fe87da23a
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594732"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中容器映像管理和安全性的最佳做法

在 Azure Kubernetes 服务 (AKS) 中开发和运行应用程序时，容器和容器映像的安全性是需要考虑的一个重要方面。 包括过时基础映像或未修补的应用程序运行时的容器可能会引入安全风险和可能的攻击途径。 为了尽量降低这些风险，你应集成一些工具，这些工具在生成时和运行时扫描并修正容器中的问题。 越早在过程中捕获漏洞或过时的基础映像，群集就安全。 在本文中，“容器”是指容器注册表中存储的容器映像以及正在运行的容器。

本文重点介绍如何保护 AKS 中的容器。 学习如何：

> [!div class="checklist"]
> * 扫描并修复映像漏洞
> * 在更新基础映像时自动触发并重新部署容器映像

你还可以阅读[群集安全][best-practices-cluster-security]的最佳方案和[pod 安全性][best-practices-pod-security]。

你还可以使用[安全中心中的容器安全性][security-center-containers]来帮助扫描容器中的漏洞。  此外， [Azure 容器注册表][security-center-acr]与安全中心集成，可帮助保护映像和注册表免受漏洞的影响。

## <a name="secure-the-images-and-run-time"></a>保护映像和运行时

**最佳做法指南** - 扫描容器映像是否存在漏洞，只部署通过了验证的映像。 定期更新基础映像和应用程序运行时，然后在 AKS 群集中重新部署工作负荷。

采用基于容器的工作负荷的一个要素是：验证用于生成自己的应用程序的映像和运行时的安全性。 如何确保不向部署中引入安全漏洞？ 部署工作流应包含使用[Twistlock][twistlock]或[浅绿色][aqua]等工具扫描容器映像的过程，然后仅允许部署已验证的映像。

![扫描并修复容器映像、验证和部署](media/operator-best-practices-container-security/scan-container-images-simplified.png)

在实际的示例中，可以使用持续集成和持续部署 (CI/CD) 管道自动执行映像的扫描、验证和部署。 Azure 容器注册表包含这些漏洞扫描功能。

## <a name="automatically-build-new-images-on-base-image-update"></a>更新基础映像时自动生成新映像

**最佳做法指南** - 你使用的是应用程序映像的基础映像，请在更新基础映像时通过自动化生成新映像。 这些基础映像通常包含安全修补程序，请更新所有下游应用程序容器映像。

每次更新基础映像时，都应更新任何下游容器映像。 此生成过程应集成到[Azure Pipelines][azure-pipelines]或 Jenkins 等验证和部署管道。 这些管道确保应用程序继续在更新后的基础映像上运行。 验证应用程序容器映像后，就可以更新 AKS 部署，以便运行最新的安全映像。

Azure 容器注册表任务也可以在更新基础映像时自动更新容器映像。 通过此功能，你可以生成少量基础映像，并通过 bug 修复和安全修复定期更新它们。

有关基本映像更新的详细信息，请参阅[通过 Azure 容器注册表任务在基本映像更新上自动构建映像][acr-base-image-update]。

## <a name="next-steps"></a>后续步骤

本文重点介绍了如何保护容器。 若要实施其中某些做法，请参阅以下文章：

* [利用 Azure 容器注册表任务自动构建基本映像更新的映像][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: /azure/security-center/container-security
[security-center-acr]: /azure/security-center/azure-container-registry-integration