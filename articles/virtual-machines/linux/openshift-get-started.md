---
title: "Azure 中的 OpenShift 概述 | Microsoft Docs"
description: "Azure 中的 OpenShift 概述。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 5d154a1c860a5ebd7af2efd55b470bb14efe6c67
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2017
---
# <a name="openshift-in-azure"></a>Azure 中的 OpenShift

OpenShift 是一个开放可扩展的容器应用程序平台，面向企业提供 Docker 和 Kubernetes。  

OpenShift 包括用于容器业务流程和管理的 Kubernetes 容器。 它增添的开发人员工具和以操作为中心的工具可实现：

- 快速应用程序开发。
- 轻松部署和缩放。
- 针对团队和应用程序的长期生命期维护。

OpenShift 有多个版本，其中有两种可在 Azure 中运行。

- OpenShift Origin
- OpenShift 容器平台
- OpenShift Online
- OpenShift Dedicated

在本文所述的四个版本中，客户可使用其中两种（OpenShift Origin 和 OpenShift 容器平台）自行部署到 Azure 中。

## <a name="openshift-origin"></a>OpenShift Origin

Origin 是社区支持的 OpenShift 的[开放源](https://www.openshift.org/)上游项目。 可在 CentOS 或 Red Hat Enterprise Linux (RHEL) 上安装 Origin。

## <a name="openshift-container-platform"></a>OpenShift 容器平台

容器平台 是 Red Hat 支持的企业就绪[商业版本](https://www.openshift.com)。 使用此版本时，客户需购买 OpenShift 容器平台的必要权利，并负责安装和管理整个基础结构。

由于客户“拥有”整个平台，他们可在本地数据中心或公有云（例如 Azure、AWS 或 Google）位置中进行安装。

## <a name="openshift-online"></a>OpenShift Online

Online 是 Red Hat 托管的，使用容器平台的多租户 OpenShift。 Red Hat 管理所有底层基础结构（例如 VM、OpenShift 群集、网络和存储）。 

使用此版本时，客户需部署容器，但无法控制容器在哪个主机上运行。 由于 Online 是多租户产品，因此可能将容器放置到与其他客户的容器相同的 VM 主机。 成本按容器计算。

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Dedicated 是 Red Hat 托管的，使用容器平台的单租户 OpenShift。 Red Hat 管理所有底层基础结构（VM、OpenShift 群集、网络、存储等）。 该群集特定于某个客户，并在公有云（例如 AWS 或 Google，Azure 版将在 2018 年初推出）中运行。 起始群集包括四个应用程序节点，费用为每年 48,000 美元（预付）。

## <a name="next-steps"></a>后续步骤

- [在 Azure 中配置 OpenShift 的常见先决条件](./openshift-prerequisites.md)
- [在 Azure 中部署 OpenShift Origin](./openshift-origin.md)
- [在 Azure 中部署 OpenShift 容器平台](./openshift-container-platform.md)
- [部署后任务](./openshift-post-deployment.md)
- [OpenShift 部署故障排除](./openshift-troubleshooting.md)
