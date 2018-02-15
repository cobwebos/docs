---
title: "Azure 容器服务常见问题解答"
description: "提供有关 Azure 容器服务的某些常见问题的解答。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/01/2018
ms.author: nepeters
ms.openlocfilehash: 2b78479c257930669729a7781b3893b3e2064bab
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>有关 Azure 容器服务 (AKS) 的常见问题解答

此文章地址频繁问题有关 Azure 容器服务 (AKS)。

## <a name="which-azure-regions-will-have-azure-container-service-aks"></a>哪些 Azure 区域将具有 Azure 容器服务 (AKS)？ 

- 加拿大中部 
- 加拿大东部 
- 美国中部 
- 美国东部 
- 东南亚 
- 欧洲西部 
- 美国西部 2 

## <a name="when-will-additional-regions-be-added"></a>何时将添加更多的地区？ 

添加更多的地区均为需求增加时。

## <a name="are-security-updates-applied-to-aks-nodes"></a>安全更新应用于 AKS 节点？ 

OS 安全修补程序应用于按夜间计划，群集中节点中，但不执行重新启动。 如果需要则在通过门户或 Azure CLI 都可能重新启动节点。 如果升级群集，则使用最新的 Ubuntu 映像和所有安全修补程序都应用 （具有重新启动）。

## <a name="do-you-recommend-customers-use-acs-or-akss"></a>使用 ACS 或 AKS 确实建议客户？ 

考虑到 Azure 容器服务 (AKS) 将在以后的日子 GA，我们建议，生成 PoC 的、 开发和测试中 AKS 群集，但生产中 ACS Kubernetes 群集。  

## <a name="when-will-acs-be-deprecated"></a>何时弃用 ACS？ 

ACS 将弃用围绕 AKS 变得提供 GA 版。 必须将群集迁移到 AKS 该日期后的 12 个月。 在 12 个月期间，可以运行所有 ACS 操作。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支持节点自动缩放？ 

节点自动缩放不支持，但在规划之中。 可能想要看一看这开源[自动缩放实现][auto-scaler]。

## <a name="why-are-two-resource-groups-created-with-aks"></a>为什么使用 AKS 创建两个资源组？ 

第二个资源组是自动创建的与 AKS 部署关联的所有资源的轻松删除。

## <a name="is-azure-key-vault-integrated-with-aks"></a>不是，它没有与 Azure Key Vault 集成。 

否，但计划此集成。 在此期间，还可以尝试以下解决方案从[Hexadite][hexadite]。 

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  