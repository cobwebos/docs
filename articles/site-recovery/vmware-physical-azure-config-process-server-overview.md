---
title: 关于 Azure Site Recovery 配置、进程和主目标服务器 |Microsoft Docs
description: 本文概述了使用 Azure Site Recovery 设置本地 VMware VM 到 Azure 的灾难恢复时使用的配置服务器、进程服务器和主目标服务器
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: raynew
ms.openlocfilehash: d835eda044c2a6d1e0e7c678073711e45fde7395
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972142"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>关于 Site Recovery 组件（配置服务器、进程服务器、主目标服务器）

本文介绍通过 [Site Recovery](site-recovery-overview.md) 服务将 VMware VM 和物理服务器复制到 Azure 所用的配置服务器、进程服务器和主目标服务器。

## <a name="configuration-server"></a>配置服务器

若要对本地 VMware VM 和物理服务器进行灾难恢复，需要一个在本地部署的 Site Recovery 配置服务器。

**设置** | **详细信息** | **Links**
--- | --- | ---
**组件**  | 配置服务器计算机运行所有本地 Site Recovery 组件，包括配置服务器、进程服务器和主目标服务器。<br/><br/> 安装配置服务器时，所有组件会自动进行安装。 | [阅读](vmware-azure-common-questions.md#configuration-server)配置服务器常见问题解答。
**角色** | 配置服务器在本地和 Azure 之间协调通信并管理数据复制。 | 详细了解将 [VMware](vmware-azure-architecture.md) 和[物理服务器](physical-azure-architecture.md)灾难恢复到 Azure 所需的体系结构。
**VMware 要求** | 若要对本地 VMware VM 进行灾难恢复，必须将配置服务器作为高度可用的本地 VMware VM 进行安装和运行。 | [了解](vmware-azure-deploy-configuration-server.md#prerequisites)先决条件。
**VMware 部署** | 建议使用下载的 OVA 模板部署配置服务器。 可以通过此方法轻松地设置符合所有要求和先决条件的配置服务器。<br/><br/> 如果因为某种原因而无法使用 OVA 模板部署 VMware VM，则可按照下面的针对物理计算机灾难恢复的说明手动设置配置服务器计算机。 | 使用 OVA 模板进行[部署](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template)。
**物理服务器要求** | 若要在本地物理服务器上进行灾难恢复，需手动部署配置服务器。 | [了解](physical-azure-set-up-source.md#prerequisites)先决条件。
**物理服务器部署** | 如果无法将它安装为 VMware VM，则可将它安装在物理服务器上。 | 手动[部署](physical-azure-set-up-source.md#set-up-the-source-environment)配置服务器。


## <a name="process-server"></a>进程服务器

**设置** | **详细信息** | **Links**
--- | --- | ---
**部署**  | 若要对本地 VMware VM 和物理服务器进行灾难恢复和复制，需要一个本地的进程服务器。 默认情况下，进程服务器在部署时安装在配置服务器上。 | [了解详细信息](vmware-azure-architecture.md?#architectural-components)。
**角色（本地）** | - 从允许进行复制的计算机接收复制数据。<br/> - 通过缓存、压缩和加密对复制数据进行优化，然后将其发送到 Azure 存储。<br/> - 将 Site Recovery 移动服务推送安装在要复制的本地 VMware VM 和物理服务器上。<br/> - 对本地计算机执行自动发现。 | [了解详细信息](vmware-physical-azure-config-process-server-overview.md#process-server)。 
**角色（从 Azure 故障回复）** | 从本地站点故障转移后，在 Azure 中将进程服务器设置为 Azure VM，以便处理故障回复到本地位置的事项。<br/><br/> Azure 中的进程服务器是临时的。 故障回复完成后，即可删除 Azure VM。 | [了解详细信息](vmware-azure-set-up-process-server-azure.md)。
**缩放** | 对于更大型的部署，可以在本地设置更多的横向扩展进程服务器。 更多的服务器可以处理更多数量的复制计算机以及更大的复制流量，从而横向扩展容量。<br/><br/> 可以在两个进程服务器之间移动计算机，以便对复制流量进行负载均衡。 | [了解详细信息](vmware-azure-set-up-process-server-scale.md)。


## <a name="master-target-server"></a>主目标服务器

主目标服务器处理从 Azure 进行故障回复期间产生的复制数据。

- 它默认安装在配置服务器上。
- 对于大型部署，可以另外添加一个单独的主目标服务器用于故障回复。


## <a name="next-steps"></a>后续步骤
- 查看对 VMware VM 和物理服务器进行灾难恢复所需的[体系结构](vmware-azure-architecture.md)。
- 查看将 VMware VM 和物理服务器灾难恢复到 Azure 的[要求和先决条件](vmware-physical-azure-support-matrix.md)。 
