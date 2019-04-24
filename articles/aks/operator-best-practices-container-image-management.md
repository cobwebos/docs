---
title: 操作员最佳做法 - Azure Kubernetes 服务 (AKS) 中的容器映像管理
description: 了解有关如何在 Azure Kubernetes 服务 (AKS) 中管理和保护容器映像的群集操作员最佳做法
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 1cc91f55d3895f06176875cb9ae620685dc09a26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464794"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS) 中容器映像管理和安全性的最佳做法

在 Azure Kubernetes 服务 (AKS) 中开发和运行应用程序时，容器和容器映像的安全性是需要考虑的一个重要方面。 包括过时基础映像或未修补的应用程序运行时的容器可能会引入安全风险和可能的攻击途径。 为了尽量降低这些风险，你应集成一些工具，这些工具在生成时和运行时扫描并修正容器中的问题。 越早在过程中捕获漏洞或过时的基础映像，群集就安全。 在本文中，“容器”是指容器注册表中存储的容器映像以及正在运行的容器。

本文重点介绍如何保护 AKS 中的容器。 学习如何：

> [!div class="checklist"]
> * 扫描并修复映像漏洞
> * 使用含数字签名容器映像的可信注册表
> * 在更新基础映像时自动触发并重新部署容器映像

还可以阅读[群集安全性][best-practices-cluster-security]和[Pod 安全性][best-practices-pod-security]的最佳做法。

## <a name="secure-the-images-and-run-time"></a>保护映像和运行时

**最佳做法指南** - 扫描容器映像是否存在漏洞，只部署通过了验证的映像。 定期更新基础映像和应用程序运行时，然后在 AKS 群集中重新部署工作负荷。

采用基于容器的工作负荷的一个要素是：验证用于生成自己的应用程序的映像和运行时的安全性。 如何确保不向部署中引入安全漏洞？ 部署工作流应包含一个使用 [Twistlock][twistlock] 或 [Aqua][aqua] 等工具扫描容器映像的流程，然后只允许部署经过验证的映像。

![扫描并修复容器映像、验证和部署](media/operator-best-practices-container-security/scan-container-images-simplified.png)

在实际的示例中，可以使用持续集成和持续部署 (CI/CD) 管道自动执行映像的扫描、验证和部署。 Azure 容器注册表包含这些漏洞扫描功能。

## <a name="use-a-trusted-registry"></a>使用可信的注册表

**最佳做法指南** - 限制 Pod 和部署可以使用的映像注册表。 只允许使用可信的注册表来验证和控制可用的映像。

为了提高安全性，还可以对容器映像进行数字签名，就像对应用程序代码进行数字签名一样。 然后就可以使用 AKS 部署签名映像。 此过程提供一层额外的安全性，因为你将 AKS 限制为仅拉取你已进行数字签名且信任的映像，而不只是通过了漏洞检查的映像。 你还确保容器映像未遭到篡改，未被完全同名的映像替换。

提供数字签名容器映像的可信注册表使环境更复杂，但某些策略或法规符合性要求使用这样的注册表。 Azure 容器注册表支持使用可信注册表和签名映像。

有关数字签名映像的详细信息，请参阅 [Azure 容器注册表中的内容信任][acr-content-trust]。

## <a name="automatically-build-new-images-on-base-image-update"></a>更新基础映像时自动生成新映像

**最佳做法指南** - 你使用的是应用程序映像的基础映像，请在更新基础映像时通过自动化生成新映像。 这些基础映像通常包含安全修补程序，请更新所有下游应用程序容器映像。

每次更新基础映像时，都应更新任何下游容器映像。 应将此生成过程集成到验证和部署管道中（例如 [Azure Pipelines][azure-pipelines] 或 Jenkins）。 这些管道确保应用程序继续在更新后的基础映像上运行。 验证应用程序容器映像后，就可以更新 AKS 部署，以便运行最新的安全映像。

Azure 容器注册表任务也可以在更新基础映像时自动更新容器映像。 通过此功能，你可以生成少量基础映像，并通过 bug 修复和安全修复定期更新它们。

有关基础映像更新的详细信息，请参阅[使用 Azure 容器注册表任务在基础映像更新时自动化映像生成][acr-base-image-update].

## <a name="next-steps"></a>后续步骤

本文重点介绍了如何保护容器。 若要实施其中某些做法，请参阅以下文章：

* [使用 Azure 容器注册表任务在基础映像更新时自动化映像生成][acr-base-image-update]
* [Azure 容器注册表中的内容信任][acr-content-trust]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-content-trust]: ../container-registry/container-registry-content-trust.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
