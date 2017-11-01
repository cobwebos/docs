---
title: "Azure 上的 OpenShift 概述 | Microsoft Docs"
description: "Azure 上的 OpenShift 概述。"
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
ms.openlocfilehash: f9641b52db91a4356f6d5789a8cd78a6bb3da02b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="openshift-overview"></a>OpenShift 概述

OpenShift 是一个开放可扩展的容器应用平台，面向企业提供 Docker 和 Kubernetes。  

OpenShift 包括用于容器业务流程和管理的 Kubernetes 容器。 它增添的开发人员工具和以操作为中心的工具可实现：

- 快速应用程序开发
- 轻松部署和缩放
- 针对团队和应用程序的长期生命期维护

OpenShift 的产品/服务有多种，其中有两种可在 Azure 中运行。

- OpenShift Origin
- OpenShift 容器平台
- OpenShift Online
- OpenShift Dedicated

在包含的四种产品/服务中，客户可使用其中两种（OpenShift Origin 和 OpenShift 容器平台）自行部署到 Azure 中。

## <a name="openshift-origin"></a>OpenShift Origin

社区支持的 OpenShift 的[开放源](https://www.openshift.org/)上游项目。 可在 CentOS 或 RHEL 上安装 OpenShift Origin。

## <a name="openshift-container-platform"></a>OpenShift 容器平台

Red Hat 支持的 Red Hat 企业级（[商业产品/服务](https://www.openshift.com)）就绪版本。 客户购买 OpenShift 容器平台的必要权利，并负责安装和管理整个基础结构。

客户“拥有”整个平台后，他们即可在本地数据中心、公有云（Azure、AWS、Google 等）等位置中进行安装。

## <a name="openshift-online"></a>OpenShift Online

Red Hat 托管的多租户 OpenShift（使用容器平台）。 Red Hat 管理所有底层基础结构（VM、OpenShift 群集、网络服务、存储等）。 

客户部署容器，但无法控制容器在哪个主机上运行。 因其是多租户的，因此可能将容器归置到与其他客户的容器相同的 VM 主机。 成本按容器计算。

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Red Hat 托管的单个租户 OpenShift（使用容器平台）。 Red Hat 管理所有底层基础结构（VM、OpenShift 群集、网络服务、存储等）。 该群集特定于某个客户，并在公有云（AWS、Google、Azure - 2018 年初推出）中运行。 起始群集包含四个应用程序节点，价格为 31.6 万人民币/年（整年度的预付款）。

## <a name="next-steps"></a>后续步骤

- [在 Azure 中配置 OpenShift 的常见先决条件](./openshift-prerequisites.md)
- [部署 OpenShift Origin](./openshift-origin.md)
- [部署 OpenShift 容器平台](./openshift-container-platform.md)
- [部署后任务](./openshift-post-deployment.md)
- [OpenShift 部署疑难解答](./openshift-troubleshooting.md)
