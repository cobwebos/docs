---
title: Azure 中的 OpenShift 概述 | Microsoft Docs
description: Azure 中的 OpenShift 概述。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: d68215359d50ac153d6df3bbcce5a9b6171698bb
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085433"
---
# <a name="openshift-in-azure"></a>Azure 中的 OpenShift

OpenShift 是一个开放可扩展的容器应用程序平台，面向企业提供 Docker 和 Kubernetes。  

OpenShift 包括用于容器业务流程和管理的 Kubernetes 容器。 它增加了以开发人员为中心的工具和以操作为中心的工具，这些工具可实现：

- 快速应用程序开发。
- 轻松部署和缩放。
- 针对团队和应用程序的长期生命期维护。

有多个可用的 OpenShift 版本：

- OpenShift 容器平台
- Azure 上的 OpenShift（完全托管的 OpenShift，将于 CY2019 年初推出）
- OKD（以前称为 OpenShift Origin）
- OpenShift Dedicated
- OpenShift Online

在本文所述的五个版本中，目前只有两个（OpenShift 容器平台和 OKD）可供客户部署到 Azure 中。

## <a name="openshift-container-platform"></a>OpenShift 容器平台

容器平台 是 Red Hat 支持的企业就绪[商业版本](https://www.openshift.com)。 使用此版本时，客户需购买 OpenShift 容器平台的必要权利，并负责安装和管理整个基础结构。

由于客户“拥有”整个平台，他们可在本地数据中心或公有云（例如 Azure、AWS 或 Google）位置中进行安装。

## <a name="openshift-on-azure"></a>Azure 上的 OpenShift

Azure 上的 OpenShift 是一种在 Azure 中运行的完全托管的 OpenShift 产品/服务。 此服务由 Microsoft 和 Red Hat 共同管理并提供支持。 群集将部署到客户的 Azure 订阅中。 此服务目前为个人预览版，计划在 CY2019 年初推出。 随着产品/服务越来越临近正式发布，将提供更多的信息。

## <a name="okd-formerly-openshift-origin"></a>OKD（以前称为 OpenShift Origin）

OKD 是社区支持的 OpenShift 的[开源](https://www.okd.io/)上游项目。 可在 CentOS 或 Red Hat Enterprise Linux (RHEL) 上安装 OKD。

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Dedicated 是 Red Hat 托管的，使用 OpenShift 容器平台的单租户 OpenShift。 Red Hat 管理所有底层基础结构（VM、OpenShift 群集、网络、存储等）。 该群集特定于一个客户，并在公有云（例如 AWS 或 Google）中运行。 起始群集包括四个应用程序节点，所有费用都按年收取并且预付款。

## <a name="openshift-online"></a>OpenShift Online

Online 是 Red Hat 托管的，使用容器平台的多租户 OpenShift。 Red Hat 管理所有底层基础结构（例如 VM、OpenShift 群集、网络和存储）。 

使用此版本时，客户需部署容器，但无法控制容器在哪个主机上运行。 由于 Online 是多租户产品，因此可能将容器放置到与其他客户的容器相同的 VM 主机。 成本按容器计算。

## <a name="next-steps"></a>后续步骤

- [在 Azure 中配置 OpenShift 的常见先决条件](./openshift-prerequisites.md)
- [在 Azure 中部署 OpenShift 容器平台](./openshift-container-platform.md)
- [在 Azure 中部署 OKD](./openshift-okd.md)
- [在 Azure Stack 中部署 OpenShift](./openshift-azure-stack.md)
- [部署后任务](./openshift-post-deployment.md)
- [OpenShift 部署故障排除](./openshift-troubleshooting.md)
